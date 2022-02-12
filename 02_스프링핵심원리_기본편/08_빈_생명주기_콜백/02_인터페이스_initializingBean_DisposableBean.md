# 02. 인터페이스 InitializingBean, DisposableBean

### 인터페이스 InitializingBean, DisposableBean

* InitializingBean 은 afterPropertiesSet() 메서드로 초기화를 지원한다. 

* DisposableBean 은 destroy() 메서드로 소멸을 지원한다.

``` java
package hello.core.lifecycle;

import org.springframework.beans.factory.DisposableBean;
import org.springframework.beans.factory.InitializingBean;

public class NetworkClient implements InitializingBean, DisposableBean {

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

    @Override
    public void destroy() throws Exception {
        disconnect();
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        connect();
        call("초기화 연결 메시지");
    }
}

```

``` 생성자 호출, url =null
connect:http://hello-spring.dev
call: http://hello-spring.dev message: 초기화 연결 메시지
14:24:09.374 [main] DEBUG org.springframework.context.annotation.AnnotationConfigApplicationContext - Closing org.springframework.context.annotation.AnnotationConfigApplicationContext@6253c26, started on Sat Feb 12 14:24:08 KST 2022
close: http://hello-spring.dev
```



### 초기화, 소명 인터페이스 단점

* 인터페이스가 스프링 전용 인터페이스이기 때문에 코드가 스프링 전용 인터페이스에 의존한다.
* 초기화, 소멸 메서드의 이름을 변경할 수 없다.
* 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.



스프링 초기에 나온 방식이라 이러한 단점들로 해당 방식은 거의 사용하지 않는다.
