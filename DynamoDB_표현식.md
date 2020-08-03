# DynamoDB 에 원하는 값만 접근 하는 방법

### ProjectionExpression (표현식)

DynamoDB의 버전이 업데이트 되면서 표현식으로 원하는 컬럼만 가져오는 방법이 생김. `표현식`은 depth가 있는 컬럼(중첩속성이 있는 컬럼)에 쉽게 접근 하는 방법.



### 중첩속성

DynamoDB에서는 JAVA의 List나 Map, Array 등을 `중첩` 속성이라고 말함. 중첩속성에 접근하는 방법은 

- `[n]` — 목록 요소의 경우
- `.`(점) — 맵 요소의 경우

----------

중첩 속성은 크게 두 가지다. (참고: [AWS DynamoDB 개발자 안내서](https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/Expressions.Attributes.html))

**1 목록**

```json
"RelatedItems": [
    341,
    472,
    649
],
```

위와 같은 속성을 목록 속성이라고 함. 목록 속성의 요소에 접근하기 위해서는 `[n]` 으로 접근한다. 예를들어 두번째 값인 472 를 가져오고 싶다면

- `RelatedItems[1]` 로 접근.



**2 맵**

```json
"Pictures": {
    "FrontView": "http://example.com/products/123_front.jpg",
    "RearView": "http://example.com/products/123_rear.jpg",
    "SideView": "http://example.com/products/123_left_side.jpg"
},
```

RearView의 값을 알고 싶다면 

- `Pictures.RearView` 로 접근하면 된다. 





### 예제 - java - 원하는 값만 나오게 하는 방법

`Member_Info` 의 hash_key 인 `id`가 `mand2` 인 멤버가 갖고 있는 값들

```json
{
  "aws:rep:deleting": false,
  "aws:rep:updateregion": "ap-northeast-2",
  "aws:rep:updatetime": 1595901429.531001,
  "id": "mand2",
  "iv": "jo5nu6edvolobzs8",
  "keyAccess": "access_key",
  "keySecret": "secret_key",
  "test": {
    "FiveStar": [
      "Excellent! Can't recommend it highly enough! Buy it!",
      "Do yourself a favor and buy this."
    ],
    "OneStar": [
      "Terrible product! Do not buy this."
    ]
  }
}
```







그 중에서 idVendor, passwd_Aka, test.FiveStar[1], test.OneStar[0] 값만 나오게 하고 싶다면 

```java
class DynamoDBTest {
    private Logger logger = LoggerFactory.getLogger(this.getClass());

    @Test
    public void projectionExpressionTest() {
        Table table = new DynamoDB().getTable("Member_Info");

        GetItemSpec spec = new GetItemSpec()
            .withPrimaryKey("id", "mand2")
            .withProjectionExpression("id, test.FiveStar[1], test.OneStar[0]")
            .withConsistentRead(true);
        
        Item item = table.getItem(spec);

        logger.info("table                 ::: " + item.toJSONPretty());
    }
}
```

참고: AWS DynamoDB 개발자 안내서 - [항목작업](https://docs.aws.amazon.com/ko_kr/amazondynamodb/latest/developerguide/JavaDocumentAPIItemCRUD.html#JavaDocumentAPIGetItem)



- 기본 전제: DynamoDB 에 getTable 메서드 따로 만듬.

```java
public Table getTable(String tableName) {
    return this.dynamoDB.getTable(tableName);
}
```

실제 값 가져오기 : aws docs 참고 ([URL](https://docs.aws.amazon.com/AWSJavaSDK/latest/javadoc/index.html?com/amazonaws/auth/BasicAWSCredentials.html)) 





#### Result

```json
table                 ::: 
{
  "test" : {
    "OneStar" : [ "Terrible product! Do not buy this." ],
    "FiveStar" : [ "Do yourself a favor and buy this." ]
  },
  "idVendor" : "mand2"
}
```

> retrieve 한 값 중 test 의 전체 값을 가져오고 싶을 때

```java
item.get("test");
> {OneStar=[Terrible product! Do not buy this.], FiveStar=[Excellent! Can't recommend it highly enough! Buy it!, Do yourself a favor and buy this.]}
```

> retrieve 한 값 중 test.Fivestar[1]의 값을 가져오고 싶을 때

```java
Map<String, Object> map = (Map<String, Object>) item.get("test");
String five = (String) ((List) map.get("FiveStar")).get(1);
```







