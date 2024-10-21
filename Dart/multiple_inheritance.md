# Multiple Inheritance
### Dart
* Dart에서는 다이아몬드 문제와 같은 여러 문제로 인해 다중 상속을 허용하지 않는다.
* 따라서, 이에 대한 대안으로 Mixin과 Extension을 제공하고 있다.

### Mixin
* 여러 클래스 간에 코드를 재사용할 수 있게 해주는 방법으로 상속을 하지 않고, 특정 클래스의 기능을 다른 클래스에 추가할 수 있다.
* 주의 사항
  * 클래스처럼 직접 인스턴스화 할 수 없으며, 상속을 지원하지 않음
  * 정의된 메서드 중 필요한 메서드만 오버라이드해서 사용 가능
* 예시 코드
    ```dart
    mixin Person {
        String name = '';
        int age = 0;

        introduce() {
            print("안녕하세요. 제 이름은 $name이고, 나이는 $age세 입니다.");
        }
    }

    class FootballPlayer with Person {
        FootballPlayer(String playerName, int playerAge) {
            name = playerName;
            age = playerAge;
        }
    }

    FootballPlayer sonny = FootballPlayer('손흥민', 31);
    sonny.introduce();
    // 안녕하세요. 제 이름은 손흥민이고, 나이는 31세 입니다.
    ```
    * Mixin 안에는 메서드와 속성을 정의할 수 있고, 클래스에서 with 키워드를 사용하여 적용할 수 있다.
* 특정 클래스로 제한하기
  * On 키워드를 화룡해 해당 Mixin을 적용할 수 있는 클래스를 제한할 수 있다.
    ```dart
    mixin Person on FootballPlayer {
        // ...
    }

    class FootballPlayer with Person { 
        // ...
    }

    class BaseballPlayer with Person { // 에러!
        // ...
    }
    ```

### Extension
* Dart에서 Extension은 기존 크래스에 새로운 기능을 추가할 수 있게 해준다. 다시 말해, 기존 클래스의 소스 코드 수정 없이 메서드를 추가하는 등의 기능 확장의 역할을 가지고 있다.
* On 키워드와 함께 사용하여 정의하며, 새로운 메서드만 추가가 가능하고, 기존의 메서드를 오버라이딩 할 수는 없다.
* 예시 코드
    ```dart
    class Person {
        String name;

        Person(this.name);
    }

    extension Greeting on Person {
        sayHello() {
            print("Hello, my name is $name.");
        }
    }

    Person person = Person("lki");
    person.sayHello();
    // Hello, my name is lki.
    ```
  
### Mixin과 Extension
* 사용 목적
  * Mixin : 여러 클래스에 공통 기능 주입
  * Extension : 기존 클래스에 메서드 추가 ( 확장 )
* 포함 가능한 요소
  * Mixin : 메서드, 속성
  * Extension : 메서드