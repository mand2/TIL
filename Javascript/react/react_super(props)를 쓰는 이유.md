자바스크립트 에서는 `super`는 부모 클래스 생성자를 가리킨다(리액트에서는 `React.Component`)

중요한것은, super(props) 선언전까지 constructor에서 `this` 키워드를 사용 할 수 없다.
**자바스크립트에서는 허용되지 않는다.**

<br><br><br>

#### props 넘기는 경우

```react
class MyComponent extends React.Component {
  constructor(props) {
    super(props);

    console.log(this.props); // prints { name: 'John', age: 42 }
  }
}
```
<br><br><br><br>
#### props 안 넘기는 경우

```react
class MyComponent extends React.Component {
  constructor(props) {
    super();

    console.log(this.props); // prints undefined

    // but props parameter is still available
    console.log(props); // prints { name: 'John', age: 42 }
  }

  render() {
    // no difference outside constructor
    console.log(this.props); // prints { name: 'John', age: 42 }
  }
}
```

<br><br><br>
