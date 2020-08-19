# installing logstash 

참고 주소: https://www.elastic.co/guide/en/logstash/7.8/installing-logstash.html 

여러 방법이 있지만 YUM 으로 하는 방법만. 환경: AWS EC2 



#### 1. GPG 키(public signing key) 다운로드 및 인스톨:

```sh
sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
```



#### 2. logstash.repo 생성

`/etc/yum.repos.d/`  디렉토리에 `logstash.repo`와 같이 접미사 `.repo` 로 된 파일 생성

```sh
$ cat > /etc/yum.repos.d/logstash.repo
[logstash-7.x]
name=Elastic repository for 7.x packages
baseurl=https://artifacts.elastic.co/packages/7.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```



#### 3. logstash 인스톨

잘 작성했는지 확인 후, logstash 인스톨

```sh
sudo yum install logstash
```



✔ RPM v3, like CentOS5와 같은 예전 버전으로는 인스톨 안됨. > [Running Logstash](https://www.elastic.co/guide/en/logstash/7.8/running-logstash.html) document for managing Logstash as a system service

```bash
$ sudo yum install logstash

Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
amzn2-core                                                                                                                                                                                                    | 3.7 kB  00:00:00
logstash-7.x                                                                                                                                                                                                  | 1.3 kB  00:00:00
logstash-7.x/primary                                                                                                                                                                                          | 146 kB  00:00:00
logstash-7.x                                                                                                                                                                                                                 409/409
Resolving Dependencies
--> Running transaction check
---> Package logstash.noarch 1:7.8.1-1 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=====================================================================================================================================================================================================================================
 Package                                               Arch                                                Version                                                   Repository                                                 Size
=====================================================================================================================================================================================================================================
Installing:
 logstash                                              noarch                                              1:7.8.1-1                                                 logstash-7.x                                              152 M

Transaction Summary
=====================================================================================================================================================================================================================================
Install  1 Package

Total download size: 152 M
Installed size: 276 M
Is this ok [y/d/N]: y
Downloading packages:
logstash-7.8.1.rpm                                                                                                                                                                                            | 152 MB  00:00:05
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : 1:logstash-7.8.1-1.noarch                                                                                                                                                                                         1/1
Using provided startup.options file: /etc/logstash/startup.options
/usr/share/logstash/vendor/bundle/jruby/2.5.0/gems/pleaserun-0.0.31/lib/pleaserun/platform/base.rb:112: warning: constant ::Fixnum is deprecated
Successfully created system startup script for Logstash
  Verifying  : 1:logstash-7.8.1-1.noarch                                                                                                                                                                                         1/1

Installed:
  logstash.noarch 1:7.8.1-1

Complete!

```





#### 4. conf 세팅

logstash의 config file 은 크게 두 종류로 나눈다: 1) pipeline configuration file 2) setting files (`.yml` 혹은 `.properties` 로 세팅되는 파일) 여기서는 1) pipeline 형태로 세팅한다.

> You create pipeline configuration files when you define the stages of your Logstash processing pipeline. On deb and rpm, you place the pipeline configuration files in the `/etc/logstash/conf.d` directory. Logstash tries to load only files with `.conf` extension in the `/etc/logstash/conf.d directory` and ignores all other files. 

logstash repo 설정과 마찬가지로, logstash는 `/etc/logstash/conf.d`  디렉토리 내부에서 `.conf` 로 끝나는 파일명만 읽는다. 

```bash
$ cat > /etc/logstash/conf.d/logger.conf 

input {
    file {
        path => ["/var/log/info_*"]
        codec => json
        start_position => "beginning"
        exclude => "*.txt"
    }
}
output {
    elasticsearch {
        hosts => ["192.168.100.1:9200"]
    }
    stdout {
        codec => "rubydebug"
    }
}

```



👀 conf 파일이 제대로 설정 되었는지 확인!!



#### 5. logstash 시작 

리눅스 내부에서 부팅시 자동으로 시작하게 하기 / 서비스 등록

```bash
sudo systemctl enable logstash.service

# 명령어 입력 후 아래와 같은 말이 나옴.
# Created symlink from /etc/systemd/system/multi-user.target.wants/logstash.service to /etc/systemd/system/logstash.service.
```

logstash 시작/중지 및 상태확인 

```bash
# 시작
sudo systemctl start logstash.service
# 중지
sudo systemctl stop logstash.service
상태확인
sudo systemctl status logstash.service
```

처음부터 설치를 root 계정 내에서 했으므로 sudo로 안전하게 넣는게 좋다..











