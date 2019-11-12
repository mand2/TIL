191111 - 191112 OJT 정리,,

### Contents packaging

[DRM 개념 및 용어 정리](https://pallycon.com/docs/ko/multidrm/drm-concepts/)   
MPEG - CENC 찾다가 DRM 관련 [정리 DOC URL](http://docs.unified-streaming.com/documentation/drm/mpeg-dash.html#common-encryption-cenc) 찾음

<br><br>

### Goal : 

PallyCon 멀티DRM 서비스의 빠른 체험을 위해 **콘텐츠 패키징과 DRM 라이선스 연동 및 재생 과정** 

[라이선스 토큰 가이드](https://pallycon.com/docs/ko/multidrm/license/license-token/#external-key)

<br>

-----------

토큰: base64 로 토큰을 비교   
how? aes256 > base64 > 토큰 값 비교

base64 [위키백과](https://ko.wikipedia.org/wiki/%EB%B2%A0%EC%9D%B4%EC%8A%A464) / [아주간단설명](https://effectivesquid.tistory.com/entry/Base64-%EC%9D%B8%EC%BD%94%EB%94%A9%EC%9D%B4%EB%9E%80) /64만으로decoding시 [문제점](https://bbolmin.tistory.com/46)

<br>

#### 토큰 JSON 형식

```javascript
{
    "drm_type": "<DRM 종류>",
    "site_id": "<사이트 ID>",
    "user_id": "<사용자 ID>",
    "cid": "<콘텐트 ID>",
    "token": "<base64(aes256(라이선스 발급 관련 토큰 룰 데이터))>",
    "timestamp": "<yyyy-mm-ddThh:mm:ssZ 형식의 토큰 유효성 발효 시간(GMT)>",
    "hash": "<base64(sha256(해시 메시지))>"
}
```

HDCP  [위키](https://ko.wikipedia.org/wiki/%EA%B3%A0%EB%8C%80%EC%97%AD_%EB%94%94%EC%A7%80%ED%84%B8_%EC%BD%98%ED%85%90%EC%B8%A0_%EB%B3%B4%ED%98%B8)  :

* HDTV에서 [HDCP/HDMI/DVI 비교](HDCP/HDMI/DVI 비교)
* [HDCP 간단정리](https://chitsol.com/entry/hdcp-%EC%9C%A0%EB%AC%B4%EC%9D%98-%EC%B0%A8%EC%9D%B4/)     
  고대역 디지털 컨텐츠 프로텍션(High-bandwidth Digital Content Protection)는 고화질 컨텐츠의 복제를 막기 위한 보호 수단 중 하나   
  HDCP는 디지털 컨텐츠 보호 장치이므로 소스 장치와 표시장치를 디지털로 연결할 때만 작동합니다. 컴포지트나 컴포넌트, D-Sub 같은 아날로그에서는 작동하지 않고, HDMI나 DVI, UDI 같은 디지털 연결 방식에서만 작동하는 보호 장치인게지요. 하지만 이 같은 디지털 연결 단자나 케이블을 쓴다고 다 HDCP가 작동하는 건 아닙니다. HDCP도 라이센스를 내야만 쓸 수 있으니까요. 



<br><br>

### 퀵가이드 f/u

https://mand2.github.io/assets/dash/stream.mpd

#1 test용 웹서버 github로 대체

#2 CLI패키져 다운로드 후 cmd에서 사용, 

```
cd p_test (절대경로/상대경로 상관없음 일단 p_test파일로 감)
exe 파일 실행
```


##### file directory tree

```
p_test
|----input
     |----intro.mp4
|----output
     |----intro
|----packager.exe
```

```
exe파일 --site_id --access_key --content_id --dash -i -o
```

<br>

##### DRM패키징 이력에서 확인 > 웹서버에 업로드(dash 파일 전체)

라이선스 토큰 생성시 (생성 [URL](https://sample.pallycon.com/dev/devconsole/customData.do?lang=ko#create-token)) 토큰 룰을 json포맷형식으로 한다. 근데 쭈우우욱 줄글로 하면 안됨. 엔터로 {} 해야함 :: example below :: 

```javascript
{
    "playback_policy": {
        "limit": true,
        "persistent": false,
        "duration" : 300
    }
}
```

example에서 지정한대로 duration 내에만 동영상 재생 가능! token으로 권한 체크하기 때문임.

HTML5 재생 테스트 시 `pallycon-customdata-v2`에서 띄어쓰기, **엔터 안되도록 체크체크** 안하면 error code발생됨

<br><br>

### SPEKE
> AWS Elemental MediaConvert, MediaPackage 에서 Multi DRM 패키징에 필요한 키를 발급 

Secure Packager and Encoder Key Exchange (SPEKE) defines the standard for communication between encryptors and packagers of media content and digital rights management (DRM) key providers. The specification accommodates encryptors running on-premises and in the AWS Cloud.
![general architecture](https://docs.aws.amazon.com/ko_kr/speke/latest/documentation/images/speke-high-level.png)

- **Encryptor** – Provides the encryption technology. Receives encryption requests from its operator, and retrieves the required keys from the DRM key provider to secure the encrypted content.
- **DRM platform key provider** – Provides encryption keys to the encryptor through a SPEKE-compliant API. The provider also provides licenses to media players for decryption.
- **Player** – Requests keys from the same DRM platform key provider, which the player uses to unlock the content and serve it to its viewers.

> MediaConvert 연동을 하려면 
>
> MediaConvert IAM 권한 생성을 해야함 > AWS S3에서 버킷을 만들고 IAM권한 create



<br><br>

##### -2 AWS S3?

 Amazon Simple Storage Service는 인터넷용 스토리지 서비스 

<br><br><br><br>

-------------
#### 주의할 점
*  REST API의 규격은 예전 버전을 따를 수 밖에 없다. 바꾸면 기존 업체들이 다 바꿔야하는 상황이 발생되어서, 어려움
* 내부에서 사용하는 API의 경우는 (완전 내부적으로만,, ) 신 규격으로 만들어도 됨. ㅇㅋㅇㅋ
* JIRA_confluence_Space에 있는 doc 보기,,
* git은 branch_dev를 보고, 모르는 것 있으면 찾아보다 물어보자,,,,
* 생활코딩  server 부분 보기:  https://opentutorials.org/course/2717/11268 
* react + boot + JPA로 (queryDSL로, 속도 이슈가 있어서 분리해서 쓰거나 암튼 방법 찾아봐야함.)



오늘의 결론: AWS에서 제공하는 cloud 서비스를 우리는 거의 다 쓰는 것 같다,,, 공부하기,,