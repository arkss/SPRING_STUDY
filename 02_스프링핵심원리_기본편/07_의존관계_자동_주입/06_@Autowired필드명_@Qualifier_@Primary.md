# 06. @Autowired 필드 명, @Qualifier, @Primary

조회 대상 빈이 2개 이상일 때 해결 방법은 아래와 같다. 

* @Autowired 필드 명 매칭 
* @Qualifier -> @Qualifier끼리 매칭 -> 빈 이름 매칭 
* @Primary 사용



### @Autowired 필드 명 매칭 

1. `@Autowired` 는 타입 매칭을 시도한다.
2. 이때 여러 빈이 있으면 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭한다.

``` java
@Autowired
private DiscountPolicy rateDiscountPolicy
```

이 경우 `rateDiscountPolicy` 가 매칭이 된다.



### @Qualifier

1. 빈 등록 시 @Qualifier를 통해 새로운 식별자를 부여한다.
2. @Qualifier 식별자를 사용하여 빈을 찾는다.
3. @Qualifier 식별자를 못찾으면 @Qualifier에 해당하는 이름의 스프링 빈을 추가로 찾는다.

``` java
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
@Qualifier("fixDiscountPolicy")
public class FixDiscountPolicy implements DiscountPolicy {}
```

``` java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
 @Qualifier("mainDiscountPolicy") DiscountPolicy
discountPolicy) {
 this.memberRepository = memberRepository;
 this.discountPolicy = discountPolicy;
}
```



### @Primary

1. `@Autowired` 시에 여러 빈이 매칭되면 `@Primary` 가 우선권을 가진다.

``` java
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}
```

``` java
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
 DiscountPolicy discountPolicy) {
 this.memberRepository = memberRepository;
 this.discountPolicy = discountPolicy;
}
```



### @Primary와 @Qualifier 활용

@Primary, @Qualifier 활용 코드에서 자주 사용하는 메인 데이터베이스의 커넥션을 획득하는 스프링 빈이 있고, 코드에서 특별한 기능으로 가끔 사용하는 서브 데이터베이스의 커넥션을 획득하는 스프링 빈이 있다고 생각해보자. 

메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary 를 적용해서 조회하는 곳에서 @Qualifier 지정 없이 편리하게 조회하고, 서브 데이터베이스 커넥션 빈을 획득할 때는 @Qualifier 를 지정해서 명시적으로 획득 하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있다. 

우선순위는 `@Qualifier`가 높다.
