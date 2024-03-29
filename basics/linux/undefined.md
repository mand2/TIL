# 시작 서비스 관련 명령어

### 리눅스에서 시작 서비스란?

부팅을 하면 자동으로 실행되는 서비스를 말한다.\
`systemctl` 이라는 명령어를 사용하여 서비스 등록 / 상태 확인 / 삭제 등을 할 수 있다.\
`systemctl` 명령어는 root 사용자만 가능하다. 따라서 `sudo` 를 앞에 작성하거나 `root` 사용자로 들어가서 명령어 실행해야 함.

📌 배포판 별로 명령어가 다를 수 있다. `systemctl` or `service` 로 나뉨.

\


### 시작서비스 등록

1.  자동으로 실행될 데몬을 작성한다\
    /etc/systemd/system/ 하위에 `{}.service` 형식으로 작성한다. test-case.service 라고 한다면

    ```shell
    cat test-case.service 
    # ... 서비스 내용 작성 ...
    ```
2.  데몬을 시작하도록 한다

    ```shell
    sudo systemctl start test-case 
    ```
3.  재부팅 시에도 자동 시작을 하도록 하기

    ```shell
    sudo systemctl enable test-case
    ```

\


### 시작서비스 관련 명령어들

1. 서비스 관리 명령어
   *   등록된 서비스 확인 (실행되지 않은 서비스까지 보기)

       ```shell
       sudo systemctl list-unit-files
       # 혹은
       sudo systemctl list-units
       # 혹은 `서비스` 인 것만 보려면
       sudo systemctl list-units --type=service
       ```
   *   등록된 서비스 중 active 서비스 목록 확인

       ```shell
       sudo systemctl list-units --state=active
       ```
   *   등록된 서비스 중 실패한 서비스 목록 확인

       ```shell
       sudo systemctl list-units --state=failed
       ```
   *   등록된 서비스 중 실행중인 서비스 목록 확인

       ```shell
       sudo systemctl list-units --state=running --type=service
       ```
   *   서비스와 관련된 모든 프로세스 죽이기

       ```shell
       sudo systemctl kill {서비스명}
       ```

\


1. 서비스 실행 관련 명령어\
   systemctl 로 시작하지 않고 service 로 한다면\
   `systemctl {명령어} {서비스명}` 대신 `service {서비스명} {명령어}` 로 쓰면 된다.
   *   서비스 실행/시작

       ```shell
       sudo systemctl start {서비스명}
       ```
   *   서비스 종료/중지

       ```shell
       sudo systemctl stop {서비스명}
       ```
   *   서비스 상태 확인

       ```shell
       sudo systemctl status {서비스명}
       ```
   *   서비스 재시작

       ```shell
       sudo systemctl restart {서비스명} 
       ```
   *   서비스 갱신

       ```shell
       sudo systemctl reload {서비스명} 
       ```

\


1. 서비스 자동 실행 관련 명령어\
   각 명령어 중 배포판에 맞는 명령어를 하나 선택해서 사용하면 됨.
   *   서비스 자동실행 활성화

       ```shell
       sudo systemctl enable {서비스명} 
       sudo chkconfig {서비스명} enable  
       ```
   *   서비스 자동실행 비활성화(중지)

       ```shell
       sudo systemctl disable {서비스명}
       sudo chkconfig {서비스명} disable 
       ```
   *   서비스 자동실행 여부 확인

       ```shell
       sudo systemctl is-enabled {서비스명}
       sudo chkconfig {서비스명} --list
       ```

### systemctl restart vs reload ?

restart : 서비스를 재시작 하기. shutdown 후 다시 start

reload : .service 로 등록된 config 파일을 다시 로드하라는 명령. 적절하게 reload 안 될 수 있다.

\
\
\
\
\
\


***

참고

* [서비스 목록 확인과 서비스 명령어들, reload와 restart의 차이점](https://fabxoe.tistory.com/130)
* [\[Linux\] CentOS 7 서비스 관리](https://ict-nroo.tistory.com/75)
* [Linux 부팅 시, 서비스 자동으로 시작하기](https://jamesu.dev/posts/2019/12/20/starting-service-automatically-on-boot-in-linux/)

Advanced

* [systemd 서비스 unit파일 작성에서 했던 실수](https://springboot.cloud/16) 환경 변수 적용이 안됨. nfs 관련 내용도 있고 알참!
