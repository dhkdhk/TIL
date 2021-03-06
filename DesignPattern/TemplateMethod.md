# 템플릿 메서드 패턴


1. 템플릿 메서드란? 


템플릿 메서드는 이름에서 유추 할 수 있듯이 `템플릿`이라는 알고리즘의 `규격화된 양식`을 부모클래스에서 API로 제공하는 것을 말한다. 

>상속을 통해 슈퍼클래스의 기능을 확장할 때 사용하는 가장 대표적인 방법. 변하지 않는 기능은 슈퍼클래스에 만들어두고 자주 변경되며 확장할 기능은 서브클래스에서 만들도록 한다.   
– 토비의 스프링

[다이어그램]

![템플릿메서드](/DesignPattern/img/TemplateMethod.png)

UML을 보면 구현 클래스로 Avante, GenesisCoupe 클래스 두개가 있다.
부모 클래스인 Car는 추상클래스로 **공통으로 진행되는 사항** 에 대해서는 부모클래스에서 정의하고, 각각의 구현체별로 **재정의가 필요한 사항**에서는 추상메서드를 사용한다.

아래의 예제를 살펴보겠다.

~~~JAVA
public abstract class Car {

    public void turnOnEngine(){
        System.out.println("자동차 시동을 킵니다.");
    }

    abstract public void wheelDrive();

    //Hook 메서드
    public void turnOnHeating(){}

    public void turnOffEngine(){
        System.out.println("자동차 시동을 끕니다.");
    }   
}

public class Avante extends Car {

    @Override
    public void wheelDrive() {
        System.out.println("이 차는 전륜 구동으로 주행되고 있습니다");
    }

}

public class GenesisCoupe extends Car {

    @Override
    void wheelDrive() {
        System.out.println("이 차는 후륜 구동으로 주행되고 있습니다.");
    }

    @Override
    protected void turnOnHeating() {
        System.out.println("좌석에 난방을 켰습니다.");
    }
}
~~~

`turnOnEngine`과 `turnOffEngine`은 구현체 클래스 **모두 동일한 행동을 보장**하기 위하여(좀더 정확하게 표현하기 위해서는 메서드에 재정의가 불가능하도록 final키워드를 적어도 좋다) 최상위 부모클래스로 정의하고 wheelDrive 메서드는(전륜 구동, 후륜구동, 4륜구동을 의미) 각 구현체 클래스별로 구현이 다르기에 재정의 하도록 구현하였다.

추가로 부모클래스인 `Car`에서 `turnOnHeating` 메서드를 살펴보면 빈껍데기인 일반 메서드로 구현된걸 볼 수 있다. 일종의 `Hook 메서드`라고도 하는데, 이렇게 구현하면 **선택적**으로 재정의가 필요한 부분에서만 구현이 가능하다. 

위의 예제에서 살펴보면, 자동차별로 난방이라는 옵션이 붙어있는 차량도 있고 아닌 차량도 있다. 난방옵션을 모든 차량이 가지고 있는 것도 아니여서 빈껍데기로 제공함으로써 **옵션이 달린 차량**만 해당 기능을 수행하기 위하여 `Hook 메서드`를 사용하곤 한다. 


2. 주의사항  

템플릿이라는 양식을 제공하는 API로 템플릿메서드 패턴을 사용할 때에는 구현체인 자식클래스로 접근을 하면 안되고 한단계 캡슐화된 고수준의 부모 클래스로 접근해야 각 클래스간 의존성을 낮출 수 있다. 


~~~JAVA
public class Client {
    public static void main(String[] args){
        Driver driver = new Driver(new Avante());
        driver.execute();

        System.out.println("================");

        Driver driver1 = new Driver(new GenesisCoupe());
        driver.execute();
    }
}


public class Driver {

    private Car car;

    public Driver(Car car){
        this.car = car;
    }

    public void execute(){
        car.turnOnEngine();
        car.wheelDrive();
        car.turnOnHeating();
        car.turnOffEngine();
    }
}
~~~

예제코드에서 객체간의 관계설정은 Client에서 이루어진 것을 확인해볼 수 있다. Driver는 Client로 전달받은 추상클래스인 Car 클래스가 GenesisCoupe인지 Avante 구현클래스인지 알지못한다. 그저 Car라는 고수준 추상클래스로 접근하여 수행할 뿐이다. 


3. 정리
 공통된 행위에 대한 정의는 부모클래스에서 정의함으로써 중복된 코드를 감소시킬 수 있으며, 호출 API는 동일하나 구체적인 알고리즘은 자식클래스에서 재정의할 수 있어 확장성에서도 용이하다. 
 
 단점을 생각해보자면, 다중상속이 불가능하다는 점에서 슈퍼클래스에 대한 `양식` (고수준 템플릿)이 엄격한 기준에 맞춰줘야 된다는 생각이 든다. 만약 추가적인 기능이 계속적으로 들어갈 경우, 부모와 자식 클래스간의 복잡성이 증가가 되어 관리하기가 힘들어질 것이다. 그래서 개인적인 내 생각에는 쉽게 변하지 않고 일반적인 알고리즘에 대한 양식을 갖춘 객체에 대해서 확장성을 넓힐 때 템플릿메소드 패턴을 사용하는 것이 좋다라는 생각이 든다. 

 [TemplateMethod 예제](https://github.com/dhkdhk/DesginPattern/tree/master/src/templateMethod)

