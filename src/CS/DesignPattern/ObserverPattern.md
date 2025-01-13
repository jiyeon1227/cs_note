❓**옵저버 패턴이란?**

❗주체가 어떤 객체의 상태 변화를 관찰 하다가 상태 변화가 있을 때마다 메서드 등을 통해 옵저버 목록에 있는 옵저버들에 변화를 알려주는 디자인 패턴 (일대다 관계)

- 주체: 객체의 상태 변화를 보고 있는 관찰자
- 옵저버들: 이 객체의 상태 변화에 따라 전달되는 메서드 등을 기반으로 ‘추가 변화 사항’이 생기는 객체들을 의미

(주체와 객체를 따로 두지 않고 상태가 변경되는 객체를 기반으로 구축하기도함)

❗MVC에서는 모델(주체)에 변경사항이 생겨서 update() 메서드로 옵저버인 뷰에 알려주고 이를 기반으로 컨트롤러가 작동

pull방식 옵저버 패턴

> 자바에서 제공하는 옵저버 관련 클래스
> - https://docs.oracle.com/javase/8/docs/api/index.html?java/util/Observable.html → 클래스
> - https://docs.oracle.com/javase/8/docs/api/index.html?java/util/Observer.html → 인터페이스

```java
import java.util.ArrayList;
import java.util.List;

interface Subject {
    public void register(Observer obj); // 옵저버를 등록하는 메서드
    public void unregister(Observer obj); // 옵저버를 등록 해제하는 메서드
    public void notifyObservers(); // 모든 등록된 옵저버에게 알림을 보내는 메서드
    public Object getUpdate(Observer obj); // 특정 옵저버에서 주체의 업데이트를 가져오는 메서드
}

interface Observer {
    public void update(); // 주체로부터의 업데이트를 받아 처리하는 메서드
}

// 주체이자 객체인 Topic
class Topic implements Subject {
    private List<Observer> observers; // 옵저버들의 리스트
    private String message; // 주체의 상태를 나타내는 메세지
														// "amumu is op champion!!"

    public Topic() {
        this.observers = new ArrayList<>();
        this.message = "";
    }

    @Override
    public void register(Observer obj) {
        if (!observers.contains(obj)) observers.add(obj); // observers컬렉션에 존대하지 않을때만 컬렉션에 추가
    }

    @Override
    public void unregister(Observer obj) {
        observers.remove(obj); 
    }

    @Override
    public void notifyObservers() {   
        this.observers.forEach(Observer::update); // Observer인터페이스의 update메소드를 가리킴
																									// 모든 옵저버의 update 메소드를 호출
																									// 람다 표현식의 더 간단한 형태
			//this.observers.forEach(observer -> observer.update()) -> 이거랑 똑같음
    }

    @Override
    public Object getUpdate(Observer obj) {
        return this.message;
    } 
    
    public void postMessage(String msg) { // msg="amumu is op champion!!"
        System.out.println("Message sended to Topic: " + msg);
        this.message = msg; 
        notifyObservers(); // 변경알리만 전달하고 실제 데이터는 전달하지 않
    }
}

class TopicSubscriber implements Observer {
    private String name;
    private Subject topic;

    public TopicSubscriber(String name, Subject topic) {
        this.name = name;
        this.topic = topic;
    }

    @Override
    public void update() {
        String msg = (String) topic.getUpdate(this); 
        System.out.println(name + ":: got message >> " + msg); 
    } 
}

public class HelloWorld { 
    public static void main(String[] args) {
        Topic topic = new Topic(); 
        Observer a = new TopicSubscriber("a", topic);
        Observer b = new TopicSubscriber("b", topic);
        Observer c = new TopicSubscriber("c", topic);
        topic.register(a);
        topic.register(b);
        topic.register(c); 
   
        topic.postMessage("amumu is op champion!!"); 
    }
}
/*
Message sended to Topic: amumu is op champion!!
a:: got message >> amumu is op champion!!
b:: got message >> amumu is op champion!!
c:: got message >> amumu is op champion!!
*/
```

> push 방식, pull방식 옵저버
>- push방식 옵저버: 주제가 일방적으로 옵저버에게 데이터를 보냄 → update(value)와 같이 매개변수를 가지고 있음
>- pull 방식 옵저버 : 옵저버가 주제로부터 필요한 데이터만 당겨옴 → update()만 호출하고 갱신받을 데이터를 설정함

❓**상속이란?**

❗상속(extends)은 자식 클래스가 부모 클래스의 메서드 등을 상속받아 사용하며 자식 클래스에서 추가 및 확장을 할 수 있는것을 말함. 이로인해 재사용성, 중복성의 최소화가 이루어짐

❓**구현이란?**

❗구현(implements)은 부모 인터페이스(interface)를 자식 클래스에서 재정의 하여 구현하는 것을 할함. 상속과는 달리 반드시 부모 클래스의 메서드를 재정의하여 구현해야함

❓**상속과 구현의 차이점?**

❗상속은 일반 클래스, abstract클래스를 기반으로 구현하며, 구현은 인터페이스를 기반으로 구현