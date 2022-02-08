# 05. @Configuration과 싱글톤

AppConfig를 보면 이상한 점이 하나 있다.

```java
@Configuration
public class AppConfig {

    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }

    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(
                memberRepository(),
                discountPolicy());
    }

    @Bean
    public DiscountPolicy discountPolicy() {
        //return new FixDiscountPolicy();
        return new RateDiscountPolicy();
    }
}
```

memberService와 orderService가 각각 memberRepository를 호출하는 것을 보면 MemoryMemberRepository가 두 번 생성된다.

이는 싱글톤에 위배된다.



하지만 각 Bean 메서드에 로그를 남기면 한 번씩만 호출되는 것을 확인할 수 있다.

```
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
```

