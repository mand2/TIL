# Shell Scripting

서버 배포를 하며 shell scripting에 대해 접하게 되었다. 학원에서 프로젝트 할 때엔 생각없이 AWS ec2 t2서버에 배포했었는데 회사에서는 그러면 큰일나니까 + 서버개발자기도 하고 + 지금 쓰고 있는 커맨드를 기본적으로 알아둬야 할 필요성을 점차 느끼고 있어서 shell scripting에 대해 찾아보았다. 출처는 제일 하단에 있다. 이 글은 대충 영한 번역한 수준임..



##### 목차

> 1. Shell 이란?
> 2. Shell의 종류
> 3. Shell Scripting ?
> 4. Basic Shell Scripting





### 01 shell 쉘이 무엇인가?

![Introduction to Shell Scripting](https://www.guru99.com/images/ShellScripting.png) 

- Linux 운영체제

- 사용자의 명령어를 받아 실행하고 결과를 반환

- 사용자가 프로그램, 커맨드, 스크립트를 사용할 때 같이 실행되는 인터페이스

- 터미널로 접근가능

- 운영체제를 감싸안고 있기에 `shell`이라고 부름



<br><br>



### 02 Shell의 종류

크게 두 가지 종류가 있다. `Bourne Shell` 과 `C shell` . `Bourne Shell`의 명령 프롬프트는 `$` 이며, `C shell`의 명령 프롬프트는 `%` 으로 차이가 있다.

**1**. The **Bourne Shell**: 

- POSIX shell also is known as `sh`
- Korn Shell also knew as `sh`
- **B**ourne **A**gain **SH**ell also knew as `bash` (most popular)

**2.** **The C shell**: 

- C shell also is known as `csh`
- Tops C shell also is known as `tcsh`



<br><br>



### 03 Shell Scripting ?

요약하자면 아래와 같다.

- Shell scripting is writing a series of **command for the shell to execute**
- Shell variables store the value of a string or a number for the shell to read
- Shell scripting can **help you create complex programs** containing conditional statements, loops, and functions
- 리소스를 많이 사용하지 않음(사용자가 커맨드라인 하나만 입력하면 기존에 저장된 shell script로 실행되기 때문)



<br><br>



### 04 Basic Shell Scripting

1. 파일 생성 (practice.sh 와 같이 확장자가 sh로)
2. `#!/bin/sh`  로 첫 줄을 시작한다. 
   `#!` 은 shebang으로 불림. 출처 [wikipedia](https://en.wikipedia.org/wiki/Shebang_%28Unix%29)
3. 실행할 코드 작성
4. 저장
5. terminal에서 해당 shell 호출하여 실행-!





<br><br>

### 05 Shell Variables 기본 예제

```shell
#!/bin/sh
echo "what is your name?"
read name
echo "How do you do, $name?"
read remark
echo "I am $remark too!"
```

![Shell Variables](https://www.guru99.com/images/program.jpg) 



<br><br>



### 06 기본 문법 (sh 기준)

- `for/while ~ do ~ done ` loop
- `for ~ in ~ do ~ done` loop
- `case ~ in ~ esac` selection 
- `if ~ then ~ elif ~ then ~ else ~ fi `







<br><br>

### 07 더 자세한 command

Linux sh command 에 대한 주소로 [바로가기](https://www.computerhope.com/unix/ush.htm)



<br><br>

##### 출처 [guru99](https://www.guru99.com/introduction-to-shell-scripting.html)