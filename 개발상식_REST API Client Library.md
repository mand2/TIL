# REST API Client 부분에서 사용 가능한 Library





### 1 HttpURLConnection

```java
void sendRequest(String request)
{
    // i.e.: request = "http://example.com/index.php?param1=a&param2=b&param3=c";
    URL url = new URL(request); 
    HttpURLConnection connection = (HttpURLConnection) url.openConnection();           
    connection.setDoOutput(true); 
    connection.setInstanceFollowRedirects(false); 
    connection.setRequestMethod("GET"); 
    connection.setRequestProperty("Content-Type", "text/plain"); 
    connection.setRequestProperty("charset", "utf-8");
    connection.connect();
}
```





### 2 RestTemplate

[DOCS](https://docs.spring.io/spring/docs/current/javadoc-api/org/springframework/web/client/RestTemplate.html)

요즘 들어 선호하는 형태는 `postForObject`  . 어떤 형태로든 받아 올 수 있어서 개발 생산성이 조금 더 높은 것 같다. `uriVariables` 형태도 있어서 `https://naver.com/{id}/posts/{index}` 이런 형식으로 url을 작성하면 더 간편하게 구현할 수 있다.

물론 #1의 `HttpURLConnection` 보다 속도가 느림. 가벼운 건 아님. 조금 더 안정성이 높다고 하는데 그건 잘 모르겠고 개발할 때 조금 더 간편하고 길게 쓰지 않아도 되어서 선호하는 편.





### 참고 주소

- HttpURLConnection과 RestTemplate 등 다른 라이브러리를 잘 설명한 [스택오버플로우](https://stackoverflow.com/questions/53795268/should-i-use-httpurlconnection-or-resttemplate)

- 한글로 된 설명 [Aaron_h님 블로그](https://digitalbourgeois.tistory.com/56?category=678387)