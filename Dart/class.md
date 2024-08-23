# 정리
### Class
* 생성자
    ```dart
    class Person {
        String name;
        int age;

        Person(this.name, this.age);
    }
    ```
    * 축약된 형태의 생성자
* 초기화 리스트
  * 생성자가 Body를 실행하기 이전에 인스턴스 변수를 초기화하고자 할 떄 사용한다. 특히, final과 같은 상수 생성자를 초기화 할 때 많이 사용된다.
    ```dart
    class Person {
        final String name;
        final int age;

        Person(String name, int age): name = name + "님", age = age - 1;
    }
    ```
  * Super의 형태로 부모 클래스를 초기화할 수도 있다.
    ```dart
    class Jane extends Person {
        final String hobby;
        Jane(String name, int age, this.hobby): super(name, age);
    }
    ```
  * Flutter 에서는 위젯을 처음 생성할 때, 부모 위젯 클래스에 키값을 초기화하는 패턴을 제공하는데 초기화 리스트는 이 패턴 이외에는 잘 사용되지 않는다.
* 접근 제어자
  * Private와 Public 두 가지만 존재한다.
    ```dart
    class Person {
        String _name;
        int _age;
        
        Person(this._name, this._age);
    }
    ```
    * 기본적으로 public
    * private 키워드를 사용하지 않고 언더스코어를 사용하여 표현한다.
* getter와 setter
  * getter
    ```dart
    class Person {
        String _name;
        int _age;

        Person(this._name, this._age);

        String get name => _name;
    }

    Person person = Person('gyl', 20);
    print(person.name); // gyl
    ```
  * setter
    ```dart
    class Person {
        String _name;
        int _age;

        Person(this._name, this._age);

        String get name => _name;
        // setName('')이 아닌 setName = ''로 사용
        set setName(String name) {
        _name = name;
        }
    }

    Person person = Person('gyl', 20);
    print(person._name); // gyl

    person.setName = 'lyg';
    print(person._name); // lyg
    ```
* extends
  ```dart
  class Person {
    String _name;
    int _age;

    Person(this._name, this._age);

    void introduce() {
        print("Hi, my name is $_name and I am $_age years old.");
    }
  }

  class Jane extends Person {
    String _hobby;

    Jane(String name, int age, this._hobby) : super(name, age);

    void showHobby() {
        print("My hobby is $_hobby.");
    }
  }
  ```
  * 만약 다중 상속과 같은 특성을 원한다면 with 키워드를 사용하면 된다.
    ```dart
    class A extends B with C, D {
        // 생략
    }
    ```
  * 혹은 Mixin을 사용할 수 있다.
    * 여러 클래스에 공통 기능을 추가할 때 사용할 수 있고, 이를 통해서 다중 상속의 문제를 피할 수 있다.
    * 예제를 통해 알아보자.
      ```dart
      mixin Runner {
          void run() {
              print("Running fast!");
          }
      }

      mixin Swimmer {
          void swim() {
              print("Swimming smoothly!");
          }
      }

      class Person {
          String name;

          Person(this.name);
      }

      class Athlete extends Person with Runner, Swimmer {
          Athlete(String name) : super(name);

          void showSkills() {
              run();
              swim();
          }
      }

      Athlete athlete = Athlete("John");
      athlete.showSkills();   
      // Running fast!
      // Swimming smoothly!
      ```
* abstract class와 implements
  * abstract class는 추상 클래스로 정의만 하고 구현은 하지 않는다. 즉, 인스턴스화 할 수 없다.
  * implements를 통해서 abstrcat class를 상속 받아 정의된 메서드나 프로퍼티를 구현해 사용한다.
  * 혹은 extends를 통해 상속 받아 의무적으로 재정의해야하는 추상 함수만 재정의할 수 있다.
  * 예제 코드를 살펴보자. ( implements )
    ```dart
    abstract class Animal {
        void makeSound();
    }

    class Dog extends Animal {
        @override
        void makeSound() => print("Bark!");
    }

    class Bird extends Animal {
        @override
        void makeSound() => print("Chirp!");
    }

    abstract class Flyable {
        void fly();
    }

    class Sparrow extends Bird implements Flyable {
        @override
        void fly() => print("Fly!");
    }

    Dog().makeSound();       // Bark!
    Sparrow().makeSound();   // Chirp!
    Sparrow().fly();         // Fly!
    ```