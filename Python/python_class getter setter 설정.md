# Python : getter setter 설정

클래스 내부에서 getter setter 설정 쉽게하기 메서드 낭비하지 않고 쉽게 쓸 수 있다!  불러오는 방법이나 셋하는 방법도 java와 조금 다름. 주의할 것..



```python
# test.py

class Test:
	
	def __init__(self, limit=None, persistent=None):
        if limit is None:
            self.__limit = False
        else : 
            self.__limit = limit

        if persistent is None:
            self.__persistent = False
        else :
            self.__persistent = persistent
	
    @property
    def limit(self):
        return self.__limit

    @property
    def persistent(self):
        return self.__persistent
    
    @limit.setter
    def limit(self, limit):
        self.__limit = limit

    @persistent.setter
    def persistent(self, persistent):
        self.__persistent = persistent
```



```python
user = Test() 
	# 값이 None, None 으로 나옴

# set: limit
user.limit = True
# get: limit 
print(user.limit)
```

