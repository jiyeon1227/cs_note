❓**싱글톤 패턴이란?**

❗하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴. 데이터베이스 연결 모듈에 많이 사용

기본적인 싱글톤 생성

```java
class Singleton {
    // 단 1개만 존재해야 하는 객체의 인스턴스, static으로 선언
    private static Singleton instance;

    // private생성자로 외부에서 직 객체 생성을 막음
    private Singleton() {}

    //외부에서는 getInstance()로 instance를 반환
    public static Singleton getInstance() {
        // instance가 null일 때만 생성
        if (instance == null) {
            instance = new Singleton();
        }
        return instance;
    }
}
public class HelloWorld{
    public static void main(String []args){
        Singleton a = Singleton.getInstance();
        Singleton b = Singleton.getInstance();
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());
        if (a == b){
         System.out.println(true);
        }
    }
}
```

❗위 코드는 멀티 스레드 환경에서 Thread-Safe를 보장하지 않음. 두 개의 스레드가 동시에 getInstance를 호출 할 경우   if (instance == null) 조건문에 동시도달 → instance를 두 번 생성할 수 있음

이는 java에서 스레드동기화를 지원하는 Synchronized를 사용할 수있지만 Thread-Safe를 보장하기위해 성능저하가 심함 → Synchronized, 성능저하 두마리 토끼를 다 잡기위해 LazyHolder 사용

❓Synchronized를 사용하면 왜 성능저하가 왜 심해질까?

❗Synchronized 키워드를 사용하면 자바 내부적으로 메서드나 변수에 동기화를 하기 위해 block과 unblock을 처리하게 되는데 이런 처리들이 만약 너무 많아지게 되면 오히려 프로그램 성능저하를 일으킬 수 있음

> Synchronized : 현재 데이터를 사용하고 있는 해당 스레드를 제외하고 나머지 스레드들은 데이터에 접근 할 수 없도록 막는 개념
>

LazyHolder를 이용한 싱글톤 생성

```java
class Singleton { // 싱글톤 인스턴스를 생성하고 관리하는 클래스
    private static class singleInstanceHolder { // 중첩 클래스 
				// LazyHolder 클래스 초기화 과정에서 JVM이 Thread-Safe 하게 instance를 생성
        private static final Singleton INSTANCE = new Singleton();
    }

		// getInstance() 메서드가 처음 호출될때 INSTANCE 가 생성됨
		// 생성은 한 번만 되고 이후에는 생성된 인스턴스를 리턴
    public static Singleton getInstance() { 
        return singleInstanceHolder.INSTANCE;
    }
}

public class HelloWorld{ 
     public static void main(String []args){ 
        Singleton a = Singleton.getInstance(); 
        Singleton b = Singleton.getInstance(); 
        System.out.println(a.hashCode());
        System.out.println(b.hashCode());  
        if (a == b){
         System.out.println(true); 
        } 
     }
}
/*
705927765
705927765
true
*/
```

[Synchronized  블로그 참고]

https://steady-coding.tistory.com/556

✏️**싱글톤 패턴의 단점**

TDD(Test Driven Development)를 할 때 걸림돌이됨. TDD에서는 단위테스트를 주로 하는데, 단위테스트는 테스트가 서로 독립적이어야 하며 테스트를 어떤 순서로든 실행할 수 있어야함.

싱글톤 패턴은 미리 생성된 하나의 인스턴스를 기반으로 구현하기 때문에 각 테스트마다 독립적인 인스턴스를 만들기 어려움

> 단위테스트(Unit Test) : 일반적으로 실무에서 테스트 코드를 작성할때는 거의 단위테스트를 의미함.  하나의 모듈을 기준으로 독립적으로 진행되는 가장 작은 단위테스트.

> 테스트 레벨 <img src="C:\CS_NOTE\src\images\테스트 레벨.jpg" width="500" height="300"/>


✏️**의존성 주입**

싱글톤 패턴은 실용적이지만 모듈간의 결합을 강하게 만들어 개방-폐쇄 원칙을 위배하게됨. 이걸 DI(Dependency Injection)를 통해 결합을 느슨하게 할 수있음

❓**의존성 주입이란?**

❗메인 모듈이 직접 다른 하위 모듈에 의존성을 주지않고, 의존성 주입자 (dependency injector)를 중간에 둬서 간접적으로 의존성을 주입하는 방식이며 ‘디커플링’ 이라고도 한다.

> 결합도는 낮게, 응집도는 높게 구성 되어야 좋은 설계(유지보수성이 높아짐)
결합도(Coupling) 또는 의존도 : 외부의 모듈과의 연관도
(의존성 : (`A → B`) A 가 B에 의존한다 → B의 상태가 변하면 A도 변경되어야 하는 관계)
응집도(Cohesion) : 모듈 내부 구성요소 간 연관 정도
<img src="C:\CS_NOTE\src\images\결합도, 응집도.jpg" width="500" height="300"/>

> 객체지향 설계원칙 (solid)
> - 단일 책임 원칙(SRP, Single responsiboility principle) : 한 클래스는 하나의 책임만을 가져야한다.
> - 개방 폐쇄 원칙 (OCP, Open-closed principle): 기존 코드를 변경하지 않으면서 기능을 추가하도록 설계 → 디자인 패턴에서 많이 사용
> - 리스코프 치환 원칙 (LSP, Liskov substitution principle) : 자식클래스는 언제나 자신의 부모 클래스를 대체할 수 있어야한다. (부모 클래스가 들어갈 자리에 자식 클래스를 넣어도 계획대로 작동해야한다.)
> - 인터페이스 분리 원칙(ISP, Interface Segregation Principle) : 자신이 사용하지 않는 인터페이스는 구현하지 말하야한다. (자신이 사용하지 않는 인터페이스때문에 영향을 받아서는 안된다.)
> - 의존성 역전 원칙(DIP, Dependenct Inversion Principle) : 의존 관계를 맺을 때 자주 변화하는 것보다, 변화가 거의 없는것에 의존해야한다.(구체적인 클래스보다 인터페이스나 추상 클래스와 의존 관계를 맺어야한다.)


> 의존성 주입의 방법
> 1. 생성자를 이용한 주입
> 2. setter를 이용한 주입
> 3. @Autowired 어노테이션으로 주입 등등

✏️의존성 주입의 장점

모듈들을 쉽게 교체할 수 있는 구조가 되어 테스팅이 쉽고 마이그레이션이 수월해진다.

또한 구현 시 추상화 layer를 넣고 이를 기반으로 구현체를 넣기 때문에 어플리케이션 의존성 방향이 일관된고, 어플리케이션을 쉽게 추론할 수 있으며, 모듈 간 관계가 좀 더 명확해진다.

✏️의존성 주입의 단점

모듈들이 더 분리되므로 클래스의 수가 늘어나 복잡성이 증가하고 약간의 런타임 페널티가 생길 수 있다.

✏️의존성 주입의 원칙

의존성 주입은 상위 모듈은 하위 모듈에서 어떤 것도 가져오면 안된다. 또한 둘 다 추상화에 의존해야 하며 추상화는 세부사항에 의존하지 말아야 한다.

⭐ 자바 싱글톤 VS 스프링 싱글톤

자바

- Class loader에 의해 구현
- Thread Safety는 개발자 로직에 따라 다름
- 공유범위 :  Class loader 기준

스프링

- 스프링 컨테이너에 의해 구현
- Thread Safety를 자동으로 보장
- 공유범위 :  ApplicationContext 기준