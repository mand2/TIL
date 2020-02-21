# git에 여러 user 등록하고 github용 SSH key 생성

회사에서 회사용 유저와 개인용 유저를 동시에 사용하고 있었는데, 인텔리제이로 버전컨트롤을 하려고 하니 user conflict와 함께 모든게 망가져버렸다..

일단 기본 global user를 회사계정으로 통일하기위해서





### 1 윈도우 자격증명 관리

> 제어판 - 사용자 계정 - 자격 증명 관리자 - Windows 자격 증명

에서 회사 계정을 제외하고 다 삭제.



### 2 git의 기본 user 세팅 그 외 삭제

git bash로 들어가서 현재 config 세팅을 확인해본다.

```bash
$ git config --list
```

를 하면 현재 user 정보들을 볼 수 있는데, 전체 삭제하고 글로벌 user (default user)를 만들어준다.

```bash
$ git config --unset --global user.name [your name]
$ git config --unset --global user.email [your email]
```

global 설정되지 않은 계정은 `--global` 이라는 단어 제외하고 삭제하면 됨.

다시 `global` 설정(즉 기본 user)을 하기 위해선, 

```bash
$ git config --global user.name "name"
$ git config --global user.email "email"
```



### 3 github 용 유저계정 설정

[Github Help](https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-ad ding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent) 페이지 따라하면 된다. (Window 10 gitbash 기준으로 작성)

그전에 앞서, github 용 리포지터리(삭제안하고 놔두고 있었음) 파일에서 gitbash 시작하고,

local user 설정을 해준다.

```bash
$ git config --local user.name "user name only valid for this repository"
$ git config --local user.email "user email only valid for this repository"
```



#### 3-1 SSH key 생성

```bash
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

로 하면 다음과 같은 메세지가 나타난다.

```bash
> Generating public/private rsa key pair.
> Enter a file in which to save the key (/c/Users/you/.ssh/id_rsa):[Press enter]
```

바로 엔터를 치면 `id_rsa` 로 나오고, 개별로 따로 설정하고 싶다면 엔터를 치기 전에 이름을 적어주면 된다. 나는 개인용도로 쓰기 때문에 다음과 같이 했다.

```bash
> Enter a file in which to save the key (/c/Users/you/.ssh/id_rsa): id_rsa_me
```

그러면 **/c/Users/you/.ssh/id_rsa_me** 형식으로 저장이 된다. 



```bash
> Enter passphrase (empty for no passphrase): [Type a passphrase]
> Enter same passphrase again: [Type passphrase again]
```

라고 터미널에 뜬다고 당황하지 말자. 기본설정으로 하고 싶다면 엔터쳐줘도 됨.



#### 3-2 SSH key를 ssh-agent에 추가하기

그 전에, ssh-agent가 구동되고 있는지와 SSH key가 제대로 생성되었는지 확인하는게 좋다.

```bash
# start the ssh-agent in the background
$ eval $(ssh-agent -s)
> Agent pid 1234

# check out SSH key
$ ls /c/Users/you/.ssh
> id_rsa_me id_rsa_me.pub

# add to ssh-agent
$ ssh-add ~/.ssh/id_rsa_me
```



#### 3-3 github에서 local에 설정되는 user로 연결되도록 만들어주기

```bash
$ touch ~/.ssh/config # create config file
$ vim ~/.ssh/config # edit the 'config' file

####################
# default for account for github
Host github.com
  HostName [your user email for github account]
  User [your user name for github account]
  IdentityFile ~/.ssh/id_rsa_me
######################
```

vim 명령어 내에서 저장후 나가기하려면 `esc` 버튼 클릭 후 `:x!` 를 타이핑 후 엔터하면 됨.

제대로 되었는지 확인

```bash
$ vi ~/.ssh/config
### file 확인 후 나가기
# esc + :qa! 
```



#### 3-3-2 Host에 `github.com`이라고 쓴 이유?

github에 접속할 때 identityFile에 저장되어있는 ssh-key 경로를 쓴다고 알려주기 위함.



#### 3-4 github 계정에 SSH key 연결하기

1 그전에 생성해둔 SSH key를 복사한다

```bash
$ clip < ~/.ssh/id_rsa_me.pub
# Copies the contents of the id_rsa.pub file to your clipboard
```



2 깃허브 세팅에 들어간다

 ![Settings icon in the user bar](https://help.github.com/assets/images/help/settings/userbar-account-settings.png) 



3 사이드바에서 SSH and GPG keys 클릭

 ![Authentication keys](https://help.github.com/assets/images/help/settings/settings-sidebar-ssh-keys.png) 



4 New SSH key 클릭

 ![SSH Key button](https://help.github.com/assets/images/help/settings/ssh-add-ssh-key.png) 

5 `Key`라고 쓰여있는 곳에 아까 복사한 SSH key 값을 붙여넣어준다. 구별가능한 타이틀명도 작성후 add

 ![The key field](https://help.github.com/assets/images/help/settings/ssh-key-paste.png) 





#### 그 외

다른 repository에도 local user를 설정해줘야 github에 commit할 때 user가 정확하게 세팅된다. local user가 없으면 global user로 들어가서 다른 사람이 commit한 거로 뜨게 됨.