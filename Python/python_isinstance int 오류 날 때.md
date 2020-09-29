

### python isinstance int 오류 날 때 

isinstance( object , int / bool ) 

object 에 bool을 넣으면 int의 instance라고 나오면서 True 케이스로 반환함.

이유: `bool`이 `int`의 하위클래스라, object가 `bool` 로 들어가면 int가 아님에도 `True` 값이 나오게 됨.

참고 [URL](https://stackoverflow.com/questions/48675868/python-3-isinstance-bool-without-external-validator-or-type)

