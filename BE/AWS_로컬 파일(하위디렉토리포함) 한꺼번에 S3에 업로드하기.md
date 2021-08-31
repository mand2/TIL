### 전제조건
- gradle
- kotlin
- jdk : aws-correto-11 (aws 오픈소스) 


## gradle 세팅

1. 기본 세팅  
```kotlin
implementation("com.amazonaws:aws-java-sdk-s3:1.12.52")
```


2. 버전관리 용 세팅  
aws-sdk 라이브러리를 많이 사용한다면 한번에 버전관리를 할 수 있다
(관련 [docs](https://docs.aws.amazon.com/ko_kr/sdk-for-java/v1/developer-guide/setup-project-gradle.html)).  
[bom 버전 확인하러 가기](https://mvnrepository.com/artifact/com.amazonaws/aws-java-sdk-bom)  
    - 코틀린 4.6 버전 이상:
        ```kotlin
        dependencies {
            implementation("com.amazonaws:aws-java-sdk-bom:1.12.52")
            implementation("software.amazon.awssdk:s3")
        }
        ```
    
    - 코틀린 4.6 버전 이전:
        ```kotlin
        dependencyManagement {
            imports {
                mavenBom("software.amazon.awssdk:bom:1.12.52")
            }
        }
        dependencies {
            implementation("software.amazon.awssdk:s3")
        }
        ```

<br>
<br>

## 디렉토리 S3 업로더 코틀린으로 작성하기 
### 1. 업로드할 S3 버킷과 연결하기 - TransferManagerBuilder 생성하기  
S3 버킷에 접근 가능한 `aws_access_key_id` 와 `aws_secret_access_key`, `region` 을 설정하여 `AmazonS3ClientBuilder` 를 만들고 
한꺼번에 업로드 할 수 있는 `TransferManagerBuilder` 를 만들어 반환하기
    
```kotlin
fun createS3DirectoryClient(accessKeyId: String, secretAccessKey: String, region: String): TransferManager {
    val awsCredentials = AWSStaticCredentialsProvider(BasicAWSCredentials(accessKeyId, secretAccessKey))

    val s3Client:AmazonS3 = AmazonS3ClientBuilder.standard()
            .withCredentials(awsCredentials)
            .withRegion(Regions.fromName(region))
            .build()
    
    return TransferManagerBuilder.standard()
            .withS3Client(s3Client)
            .build()
}
```
<br>

### 2. S3 버킷에 업로드하는 부분 만들기 - basic
1. 먼저 `local_path` 가 /d/test/uploader/ 라고 하자.   
uploader 내부에는 수많은 파일이 있고, 각 파일마다 2mb 를 넘지 않늗다고 가정.  
(만약 파일 하나 하나의 사이즈가 크다면 다른 방식으로 해야 됨. 하단 참고부분에서 `멀티파트 업로드를 사용한 객체 업로드` 부분을 참고할 것.)  
<br>

2. s3에 업로드 될 path 는 {bucket_name} path1/path2/upload/210831/ 이라고 가정.  
`bucket_name` = mand2 로 한다.  
`default_path` = /path1/path2/ 로 가정.  
참고로 s3 업로드시 해당 객체(경로)가 없어도 자동으로 생성되므로 먼저 객체를 만들어야하나? 하고 고민하지 않아도 된다. (내가 고민하다 삽질해봄..ㅎ)  
    > 👀 주의사항   
    각 path 앞, 뒤에 `/` 가 있다면 없애주자.   
    예를들어 `/mand2/path1/path2/....` 이라고 설정이 된다면 실제로 업로드 되지 않음.   
    테스트 해보면 알겠지만 업로드 완료 라고 뜨지만 실제 s3에는 객체생성이 되지 않는다.

<br>

```kotlin
@JvmField val pathRegEx: Regex = Regex("^(/)|(/)$")

fun uploadDirectoryOfClient(s3DirectoryClient:TransferManager, defaultPath: String) {
    // 로컬 디렉토리 세팅
    val localPath = "/d/test/uploader/"
    val localDirectory = File(localPath)
    
    // s3 객체 경로 세팅
    val bucketName = "mand2"
    val s3Path = pathRegEx.replace(defaultPath, "")
    val s3UploadPath = "$s3Path/upload/210831"
    try {
        // 실제 업로드
        val uploadDirectory: MultipleFileUpload = s3DirectoryClient.uploadDirectory(
                bucketName,
                s3UploadPath,
                localDirectory,
                true
        )
        uploadDirectory.waitForCompletion()
        logger.info("[Uploader] Upload Directory to S3 Success !")
        
    } catch (e: AmazonServiceException) {
        logger.error("Amazon service error: {}, {}", e.message, e)
    } catch (e: AmazonClientException) {
        logger.error("Amazon client error: {}, {}", e.message, e)
    } catch (e: InterruptedException) {
        logger.error("Transfer interrupted: {}, {}", e.message, e)
    } finally {
        s3DirectoryClient.shutdownNow()
    }
}
```
 

<br>
<br>


### 3. S3 버킷에 업로드하는 부분 만들기 - advanced
위에 코드를 실행해보면 답답할 것이다. 얼마나 완료되었는지 확인을 못하므로..🤔   
그러니 로그를 같이 남겨서 확인할 수 있게 해주자


```kotlin
@JvmField val pathRegEx: Regex = Regex("^(/)|(/)$")

fun uploadDirectoryOfClient(s3DirectoryClient:TransferManager, defaultPath: String) {
    // 로컬 디렉토리 세팅
    val localPath = "/d/test/uploader/"
    val localDirectory = File(localPath)
    
    // s3 객체 경로 세팅
    val bucketName = "mand2"
    val s3Path = pathRegEx.replace(defaultPath, "")
    val s3UploadPath = "$s3Path/upload/210831"

    var uploadProgressBar = StringBuffer() // 업로드 진행상황 log
    try {
        // 실제 업로드
        val uploadDirectory: MultipleFileUpload = s3DirectoryClient.uploadDirectory(
                bucketName,
                s3UploadPath,
                localDirectory,
                true
        )
        
        uploadProgressBar
                .append("[Running] upload progressing... start")
                .appendLine()

        while (!uploadDirectory.isDone) {
            try {
                Thread.sleep(1000)
            } catch (e: InterruptedException) {
                return;
            }
            val progress: TransferProgress = uploadDirectory.progress
            val pct: Double = progress.percentTransferred
            val pctFormat = DecimalFormat("##0.00")
            uploadProgressBar
                    .append("[Running] ${pctFormat.format(pct)}% upload progressing...")
                    .appendLine()
        }

        logger.info("[Uploader] Upload Directory to S3 Success !")
        
    } catch (e: AmazonServiceException) {
        logger.error("Amazon service error: {}, {}", e.message, e)
    } catch (e: AmazonClientException) {
        logger.error("Amazon client error: {}, {}", e.message, e)
    } catch (e: InterruptedException) {
        logger.error("Transfer interrupted: {}, {}", e.message, e)
    } finally {
        s3DirectoryClient.shutdownNow()
        logger.info(uploadProgressBar.toString())
    }
}
```

<br>
<br>

결과 예시
```text
[Running] upload progressing... start
[Running] 0.35% upload progressing...
[Running] 0.70% upload progressing...
[Running] 1.06% upload progressing...
[Running] 1.44% upload progressing...
[Running] 1.80% upload progressing...
[Running] 2.15% upload progressing...
[Running] 2.52% upload progressing...
[Running] 6.81% upload progressing...
[Running] 11.00% upload progressing...
[Running] 15.00% upload progressing...
[Running] 19.12% upload progressing...
[Running] 23.31% upload progressing...
```



<br>
<br>

#### 참고 주소 

- Using TransferManager for Amazon S3 Operations 中 `**Upload a Directory` 참고**
[https://docs.aws.amazon.com/ko_kr/sdk-for-java/v1/developer-guide/examples-s3-transfermanager.html#transfermanager-upload-directory](https://docs.aws.amazon.com/ko_kr/sdk-for-java/v1/developer-guide/examples-s3-transfermanager.html#transfermanager-upload-directory)

- aws s3 transferManager exmaple code : 
[https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/java/example_code/s3/src/main/java/aws/example/s3/XferMgrUpload.java](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/java/example_code/s3/src/main/java/aws/example/s3/XferMgrUpload.java)

- 진행률
    - 관련 docs _ Poll the Current Progress of a Transfer
    [https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/examples-s3-transfermanager.html#transfermanager-get-status-and-progress](https://docs.aws.amazon.com/sdk-for-java/v1/developer-guide/examples-s3-transfermanager.html#transfermanager-get-status-and-progress)
    - 진행률 관련 aws 깃허브 
    [https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/java/example_code/s3/src/main/java/aws/example/s3/XferMgrProgress.java](https://github.com/awsdocs/aws-doc-sdk-examples/blob/master/java/example_code/s3/src/main/java/aws/example/s3/XferMgrProgress.java)
- Spring Boot :: Kotlin으로 AWS S3 업로드 기능 구현
[https://wave1994.tistory.com/131](https://wave1994.tistory.com/131)
- Performing Operations on Amazon S3 Objects
[https://docs.aws.amazon.com/ko_kr/sdk-for-java/v1/developer-guide/examples-s3-objects.html](https://docs.aws.amazon.com/ko_kr/sdk-for-java/v1/developer-guide/examples-s3-objects.html)
- 멀티파트 업로드를 사용한 객체 업로드 _ AWS SDK 사용(상위 수준 API)
[https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/mpu-upload-object.html](https://docs.aws.amazon.com/ko_kr/AmazonS3/latest/userguide/mpu-upload-object.html)

    참고: 하위수준 도 사용가능. 가능 범위:

    - 멀티파트 업로드를 일시 중지한 후 다시 시작해야 하거나
    - 업로드 중에 부분 크기를 변경해야 하거나
    - 업로드 데이터 크기를 미리 확인하지 않은 경우


