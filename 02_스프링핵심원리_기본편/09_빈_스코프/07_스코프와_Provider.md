# 07. 스코프와 Provider

Provider를 사용해서 해결할 수 있다.

```java
@Controller
@RequiredArgsConstructor
public class LogDemoController {
    private final LogDemoService logDemoService;
    private final ObjectProvider<MyLogger> myLoggerProvider;

    @RequestMapping("log-demo")
    @ResponseBody
    public String logDemo(HttpServletRequest request) {
        String requestURL = request.getRequestURI().toString();
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.setRequestURL(requestURL);

        myLogger.log("controller test");
        logDemoService.logic("testId");
        return "OK";
    }
}
```

``` java
@Service
@RequiredArgsConstructor
public class LogDemoService {
    private final ObjectProvider<MyLogger> myLoggerProvider;

    public void logic(String id) {
        MyLogger myLogger = myLoggerProvider.getObject();
        myLogger.log("service id = " + id);
    }
}
```



수정 결과 log가 제대로 출력되는 것을 확인할 수 있다.

``` 
[7b928324-3fff-49bc-bc76-cf65f55599eb] request scope bean create:hello.core.common.MyLogger@6f122b51
[7b928324-3fff-49bc-bc76-cf65f55599eb][/log-demo] controller test
[7b928324-3fff-49bc-bc76-cf65f55599eb][/log-demo] service id = testId
[7b928324-3fff-49bc-bc76-cf65f55599eb] request scope bean close:hello.core.common.MyLogger@6f122b51
```

* ObjectProvider 덕분에 ObjectProvider.getObject() 를 호출하는 시점까지 request scope 빈의 생성을 지연할 수 있다. 
* ObjectProvider.getObject() 를 호출하시는 시점에는 HTTP 요청이 진행중이므로 request scope 빈의 생성이 정상 처리된다. 
* ObjectProvider.getObject() 를 LogDemoController , LogDemoService 에서 각각 한번씩 따로 호출해도 같은 HTTP 요청이면 같은 스프링 빈이 반환된다
