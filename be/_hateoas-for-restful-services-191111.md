# HATEOAS for RESTful Services

Spring boot에서 rest를 조금 더 자유자재로 사용할 수 있는 방법 HATEOAS 를 사용한다

\
\
\


## 1 - Richardson Maturity Model

[reference URL](https://www.springboottutorial.com/spring-boot-hateoas-for-rest-services)

* LELVE 0 ​ rest 스타일에 SOAP web service를 사용\
  ​ 예) `http://server/getPosts`\

* LELEL 1 ​ URI를 활용\
  ​ 예) `http://server/accounts` `http://server/accounts/10`\

* LEVEL 2 ​ URI + HTTP method\
  ​ 예) 계정 업데이트시 `posts/1/comments/5`\

* LEVEL 3 ​ HATEOAS( Hypermedia as the engine of application state )\
  ​ 요청 info와 다음 액션(possible maybe) 설정까지 가능\
  \


## 2 - HATEOAS

아래의 영문은 위의 LEVEL3의 개념을 더 쉽고 길게 설명. 개념 잡기 어려울 때 보기

The data that you would want to see. Is that all? You would also see **links and buttons to see related data**.

For example, if you go to a student page, you will see

* Student profile
* Links to Edit and Delete Student details
* Links to see details of other students
* Link to see details of the courses and grades of the student

HATEOAS brings the same concepts to RESTful Web Services.

When some details of a resource are requested, you will provide the **resource details** as well as details of **related resources** and the **possible actions** you can perform on the resource. For example, when requesting information about a facebook user, a REST service can return the following

* User details
* Links to get his recent posts
* Links to get his recent comments
* Links to retrieve his friend’s list.

\
\


> **Spring HATEOAS** offers three abstractions for creating the URI – _ResourceSupport, Link, and ControllerLinkBuilder_

**참고**: Servlet contexct로 URI 만들지 않음!! [referenceURL](https://www.baeldung.com/spring-hateoas-tutorial) [tutorialGithub](https://github.com/eugenp/tutorials/tree/master/spring-boot-rest)

한글로 된 [설명](https://engkimbs.tistory.com/866)(그런데 글씨가 매우 작앗,,!) 코드 보기 불편 ㅠ\_ㅠ

\
\


### ResourceSupport

link를 연결할 메서드인 `add()`를 사용하려면 resourceSupport 클래스를 extends 해야 한다.\
`where?` server에서 client로 넘겨줄 때의 **객체가 상속해야** 함.

\
\


### Link > linkTo()

```java
public static ControllerLinkBuilder linkTo(Object invocationValue)
 @RequestMapping("/customers")
 class CustomerController {

   @RequestMapping("/{id}/addresses")
   HttpEntity<Addresses> showAddresses(@PathVariable Long id) { … }
 }

 Link link = linkTo(methodOn(CustomerController.class).showAddresses(2L)).withRel("addresses");
```

Creates a `ControllerLinkBuilder` pointing to a controller method. Hand in a dummy method invocation result you can create via `methodOn`(Class, Object...) or `DummyInvocationUtils.methodOn`(Class, Object...).

The resulting [`Link`](https://docs.spring.io/spring-hateoas/docs/0.25.3.BUILD-SNAPSHOT/api/org/springframework/hateoas/Link.html) instance will point to `/customers/2/addresses` and have a rel of `addresses`. For more details on the method invocation constraints, see [`DummyInvocationUtils.methodOn(Class, Object...)`](https://docs.spring.io/spring-hateoas/docs/0.25.3.BUILD-SNAPSHOT/api/org/springframework/hateoas/core/DummyInvocationUtils.html#methodOn-java.lang.Class-java.lang.Object...-).

\
\


### methodOn()

Controller에서 사용할 때 기본 controller의 URI 뒤에 **pathVariable 설정하도록** 만들 수 있다.\
비슷한 용법으로 `slash()` 메서드를 사용해도 됨.

\
\


```java
@GetMapping(produces = { "application/hal+json" })
public Resources<Customer> getAllCustomers() {
    List<Customer> allCustomers = customerService.allCustomers();
 
    for (Customer customer : allCustomers) {
        String customerId = customer.getCustomerId();
        Link selfLink = linkTo(CustomerController.class).slash(customerId).withSelfRel();
        customer.add(selfLink);
        if (orderService.getAllOrdersForCustomer(customerId).size() > 0) {
            Link ordersLink = linkTo(methodOn(CustomerController.class)
              .getOrdersForCustomer(customerId)).withRel("allOrders");
            customer.add(ordersLink);
        }
    }
 
    Link link = linkTo(CustomerController.class).withSelfRel();
    Resources<Customer> result = new Resources<Customer>(allCustomers, link);
    return result;
}
```

\#1 `customer.add(selfLink)` : customers(**기본URI**)/10A(**customerID**)

\#2 `customer.add(ordersLink)` : customers(**기본URI**)/10A(**customerID**)/orders

\#3 이 링크를 누르면 10A가 주문한 모든 주문 물품의 link가 나오게 만듬.

\
\


front 쪽에서 보여지는 link result #1 #2

```javascript
"_links": {
    "self": { // #1
    	"href": "http://localhost:8080/spring-security-rest/api/customers/10A"
	},
    "allOrders": { // #2
    	"href": "http://localhost:8080/spring-security-rest/api/customers/10A/orders"
	}
}
```

\
\


```javascript
// #3
"self": { //order 1
	"href": "http://localhost:8080/spring-security-rest/api/customers/10A/001A"
}
"self": { //order 2
	"href": "http://localhost:8080/spring-security-rest/api/customers/10A/002A"
}
"self": { //order 3
	"href": "http://localhost:8080/spring-security-rest/api/customers/10A/003A"
}
```

\
\
