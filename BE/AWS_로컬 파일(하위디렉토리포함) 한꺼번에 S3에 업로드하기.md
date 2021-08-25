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
혹은 aws-sdk 라이브러리를 많이 사용한다면 한번에 버전관리를 할 수 있다(관련 [docs](https://docs.aws.amazon.com/ko_kr/sdk-for-java/v1/developer-guide/setup-project-gradle.html)).   
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


