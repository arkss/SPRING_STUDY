# 03. 애노테이션 @PostConstruct, @PreDestroy

### 애노테이션 @PostConstruct, @PreDestroy

스프링에서는 이 방법을 권장하고 있다.

@PostConstruct, @PreDestroy을 사용하면 편리하게 초기화와 종료를 실행할 수 있다.

``` java
package hello.core.lifecycle;

import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;

public class NetworkClient {

    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url =" + url);
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public void connect() {
        System.out.println("connect:" + url);
    }

    public void call(String message) {
        System.out.println("call: " + url + " message: " + message);
    }

    public void disconnect() {
        System.out.println("close: " + url);
    }

    @PostConstruct
    public void init() {
        disconnect();
    }

    @PreDestroy
    public void close() {
        connect();
        call("초기화 연결 메시지");
    }
}

```



#### @PostConstruct, @PreDestroy 특징

* 스프링에서 가장 권장하는 방법이다.
* 애노테이션 하나만 붙이면 되서 편리하다.
* 스프링에 종속적인 기술이 아니라 JSR-250라는 자바 표준이기 때문에 다른 컨테이너에서도 동작한다.
* 컴포넌트 스캔과 잘 어울린다.
* 외부 라이브러리에는 적용하지 못한다. 외부 라이브러리를 초기화, 종료 해야 하면 @Bean의 기능을 사용하자. 
