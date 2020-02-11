## python type-hinting

파이썬은 자바스크립트와 같이 자료형이 고정되어있지 않아 class 형식으로 만들 때 에러 혹은 기본값을 반환해줘야 하는 일이 발생한다. 이를 방지하기 위해, (무조건 방지는 아님..ㅎ..) type-hint라는 개념을 찾았다.



입력될 `parameter` 의 타입이 무엇인지 명시, return 값이 어떤 값일지도 명시가능하다. 물론 다른 타입으로 insert하는 걸 막지는 못하지만 pycharm 기준으로 type을 제대로 넣어달라는 힌트가 뜬다.





#### 예제

```python
class Test:
    __num1: int = 0
    __num2: int = 0

    def __init__(self, num1: int, num2: int):
        if isinstance(num1, int):
            self.__num1 = num1
        if isinstance(num2, int):
            self.__num2 = num2

    def add(self):
        return self.__num1 + self.__num2

    @property
    def num1(self):
        return self.__num1

    @property
    def num2(self):
        return self.__num2


t = Test('1', 1)
print(t.add())
print('num1\t', t.num1, '\nnum2\t', t.num2)

```

기본값으로 0을 넣어줬음. 그래서 int 형이 아니면 기존에 세팅되어있는 값인 '0' 을 갖게 된다. 실은 int 형인지 아닌지 제대로 판별하려면 

```python
if not isinstance(num1, bool) and isinstance(num1, int):
    self.__num1 = num1
```

과 같이 해야하지만, ~~시간관계상...~~ 일단 개념만 알고 가자는 뜻에서 생략-





#### 결과

```bash
$ results below:
1
num1     0
num2     1
```





