# Windows OS 우분투 20.04 LTS + WSL2 업데이트하기

다시 공부좀 하려는데 다 도커를 설치해야 되어서(...) 윈도우 환경에서 도커를 돌리는 방법을 찾다보니 우분투 20.04 LTS 업데이트와 WSL2 업데이트 둘 다 해야된다는 걸 찾았다.

현재 사용중인 Ubuntu 18.04 LTS 를 Ubuntu 20.04 LTS 로 업그레이드 먼저 하고, WSL2로 연결하는 방법을 써본다.

## 절차1 : 우분투 20.04 LTS 로 업데이트하기

설치버전 확인

```bash
$ lsb_release -a
No LSB modules are available.
Distributor ID: Ubuntu
Description:    Ubuntu
```

업그레이드 준비를 위한 18.04 최신화

```bash
sudo apt update
sudo apt list --upgradable
sudo apt upgrade
```

사용하지 않는 패키지 정리

```bash
sudo apt --purge autoremove
```

업데이트 관리자 패키지 설치 (기본 되어있어서 안해도 됨)

```bash
sudo apt install update-manager-core
```

업데이트 진행

```bash
sudo do-release-upgrade -d
# 업데이트 하냐 ? Y 누르면 됨.... 계속 y (wsl 재부팅 여부만 물음. 컴퓨터 재부팅X)
```

업데이트 진행 시 설치오류가 났을 때  
오류 : command terminated with exit status 1 인 경우
- reddit
   도움 안 되었음.

    ```bash
    sudo sed -i 's/groovy/hirsute/g' /etc/apt/sources.list
    sudo apt update && sudo apt -y dist-upgrad
    ```

   주소: https://www.reddit.com/r/Ubuntu/comments/mwdlj0/bug_cannot_upgrade_from_2010_to_2104/


- **도움 된 방법** snapd 삭제
    ```bash
    sudo apt remove --purge snapd
    ```

   주소: [https://askubuntu.com/questions/1340153/how-to-upgrade-ubuntu-18-04-to-20-04-in-wsl-when-wsl-export-fails](https://askubuntu.com/questions/1340153/how-to-upgrade-ubuntu-18-04-to-20-04-in-wsl-when-wsl-export-fails)


- **도움된 방법** Windows 기능 켜기/끄기  
   Windows 기능 켜기/끄기(Windows Features) 조작과 재부팅 (이 두개가 다 켜져 있어야 한다)

    - Linux용 Windows 하위 시스템
    - 가상 머신 플랫폼

   이 부분을 다 껐다가 재부팅 → 다시 켜고 → 재부팅 → WSL 실행 해본다 (처음엔 설정값이 잘 안 붙었을 수도 있음.. #3 사용 후 #2 명령어를 사용하니 잘 되었음)

   주소 : [https://bluenotes.kr/272](https://bluenotes.kr/272)


- powershell 에서 `wsl --user root` 하고 zsh 변경하라는데 안됨.
   도움 안됨
   주소: [https://github.com/microsoft/WSL/issues/4899](https://github.com/microsoft/WSL/issues/4899)

버전 설치  확인

```bash
$ lsb_release -a
```

<br>
<br>
<br>

## 절차2 : Windows Terminal 설치

1. Microsoft Store 에서 Windows Terminal 검색하여 설치  

2. 윈도우즈 docs에 들어가서 WSL2 커널 다운로드, 설치  
    [https://docs.microsoft.com/ko-kr/windows/wsl/wsl2-kernel](https://docs.microsoft.com/ko-kr/windows/wsl/wsl2-kernel)
<br>
<br>

3. Windows Terminal 실행  
Powershell 실행
    
    ```bash
    # 현재 WSL 버전 확인
    $ wsl -l -v
    
    > NAME            STATE           VERSION
    > * Ubuntu-18.04    Stopped         1
    
    # 버전 세팅
    # wsl --set-version {distributed Name} {version}
    $ wsl --set-version Ubuntu-18.04 2
    > 변환이 진행 중입니다. 몇 분 정도 걸릴 수 있습니다...
    > WSL 2와의 주요 차이점에 대한 자세한 내용은 https://aka.ms/wsl2를 참조하세요
    > 변환이 완료되었습니다.
    
    # 결과 확인
    $ wsl -l -v
    
    > NAME            STATE           VERSION
    > * Ubuntu-18.04    Stopped         2
    ```

    참고: `wsl —set-default-version 2` 로는 전환이 안된다. set-version 으로 설정하는게 낫다.


<br>
<br>

---

출처
- [https://rottk.tistory.com/entry/WSL-우분투-1804를-2004로-업그레이드-하기](https://rottk.tistory.com/entry/WSL-%EC%9A%B0%EB%B6%84%ED%88%AC-1804%EB%A5%BC-2004%EB%A1%9C-%EC%97%85%EA%B7%B8%EB%A0%88%EC%9D%B4%EB%93%9C-%ED%95%98%EA%B8%B0)
- WSL 2(Windows Subsystem For Linux 2) 정식 버전 사용하기

  [https://www.lesstif.com/software-architect/wsl-2-windows-subsystem-for-linux-2-89555812.html](https://www.lesstif.com/software-architect/wsl-2-windows-subsystem-for-linux-2-89555812.html)

- [Linux] 윈도우에서 WSL2 로 리눅스 사용하는법 (ubuntu18)

  [https://snwo.tistory.com/139](https://snwo.tistory.com/139)

- WSL2 개념과 설치 방법
  [http://melonicedlatte.com/2020/07/05/200400.html](http://melonicedlatte.com/2020/07/05/200400.html)
