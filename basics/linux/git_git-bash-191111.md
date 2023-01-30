# git bash 사용하기

1. git 버전 관리를 시작하려는 프로젝트 폴더 우클릭 여기가 **로컬 저장소**가 됨
2. Git Bash Here 클릭
3. git commit 에 사용할 username 설정 git config --global user.name "이름"
4. email git config --global user.email "이메일"
5.  이 폴더에서 처음으로 git init **3\~5번까지 한번만하면됨**

    git init

### 원격 branch 하나만 가져올 때

1. git remote update
2. git branch -r 원격 저장소의 branch list 보기
3.  git branch -a

    로컬/원격 모든 저장소
4. git checkout -t remote\_path/branch\_name : 원격 브랜치 선택, 복사
5. 7\~9가 안되면 아래와 같이 git clone -b {branch\_name} --single-branch {저장소URL}
