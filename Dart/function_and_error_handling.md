# 정리
### 1. Function
### Basic Format

- Dart의 경우, 반환타입을 앞에 명시
    
    ```dart
    반환타입 함수명(매개변수 타입 매개변수명) {
    	바디 ( 반환 )
    }
    
    // 예시 코드
    int sum(int a, int b) {
    	return a + b;
    }
    ```
    
    - Dart에서는 기본적으로 명시된 모든 파라미터를 작성해야한다. 
    - 기본값이 필요하다면 아래와 같이 작성하면 된다.
    
        ```dart
        // 예시 코드
        int sum(int a, {int b = 10}) {
            return a + b;
        }
        
        // 위와 같이 기본값이 있을 경우에는 기본값이 있는 것을 가장 뒤에 배치해야한다.
        // 만약 아래와 같이 작성한다면 오류가 발생한다.
        int sum({int a = 10}, int b) {
            return a + b;
        }
        ```
    

### Named Parameter

- Swift 코드를 살펴보자.
    
    ```dart
    // Swift에서는 아래와 같이 (파라미터 명 : 타입)을 인수로 작성한다.
    func setName(name: String) {
    	myName = name
    }
    
    setName(name: "홍길동")
    ```
    
- Dart 코드를 살펴보자.
    
    ```dart
    // 호출시 인수를 작성하지 않음
    void setName(String name) {
    	myName = name
    }
    setName("호랑이");
    
    // 중괄호로 감싼다면 파라미터명을 작성해야한다. ( 비로소 Named Parameter가 된다. )
    void setName({String name}) {
    	myName = name
    }
    setName(name: "호랑이");
    ```
    
- Dart에서는 중괄호를 통해서 Named Parameter를 만들어줄 수 있다. 하지만 이대로 사용한다면 바디의 로직에서 Null-Safty하지 않다는 컴파일 에러를 볼 수도 있다. 따라서 두 가지 해결방법이 있다.
  1. 초기값을 부여하는 방법
  2. Required 키워드를 사용하는 방법을 통해 사전에 null이 될 수 있는 가능성을 없앨 수 있다.
    
        ```dart
        void setName({required String name, required int age}) {
            myName = name
        }
        setName(name: "호랑이", age: 21);
        ```
    
  3.  nullable로 표현하기
    
        ```dart
        void setName({String? name, int? age}) {
            myName = name
        }
        setName(name: "호랑이"); 
        ```
    
    - ?를 통해서 null일 수 있음을 표현할 수 있다. 또한, null일 수 있기 때문에 인수에 작성하지 않아도 상관없다.

### Lambda

- 화살표를 통해서 함수의 반환을 간단하게 작성할 수 있다. ( 람다식 )
    
    ```dart
    String sayHello(String name) => "Hello, $name!";
    sayHello(name: "jay"); // Hello, Jay!
    ```
    
- 삼항연산자를 활용해 볼 수도 있다.
    
    ```dart
    String sayHello(String? name) => name != null ? "Hello, $name!" : "Hello, John Doe !";
    sayHello(name: null); // Hello, John Doe!
    ```
    
- 이도 귀찮다면 ?? 키워드를 사용하면된다.
    
    ```dart
    String toUpper(String? str) => str?.toUpperCase() ?? "str is null";
    toUpper(null); // str is null
    toUpper("hello"); // HELLO
    ```
    
- ?? 키워드 사용하기
      
  ```dart
  String name;
  name ??= "바보"; // 바보
  name = null;
  name ??= "멍청이"; // 멍청이
  name ??= "말미잘"; // 멍청이가 출력된다. 즉, null일 경우에만 값을 할당하게 된다.
  ```
        

### 2. Error Handling

- Try
    
    ```dart
    try {
    	// do somthing
    } 
    ```
    
- Try - On
    
    ```dart
    try {
        // do somthing
    } on 예외클래스 {
    	// handle error
    }
    ```
    
- Try - Catch
    
    ```dart
    try {
    	// do somthing
    } catch(e) {
    	print(e);
    }
    ```
    
- Try - on - catch
    
    ```dart
    try {
    	// do somthing
    } on 예외클래스 catch(e) {
    	print(e); // 해당 예외 클래스가 출력된다.
    } on 예외클래스 catch(e) {
        // 원하는 만큼 예외클래스를 작성해갈 수 있다.
    }
    ```
    
- Finally
    
    ```dart
    try {
    	// do somthing
    } on 예외클래스 catch(e) {
    	print(e); // 해당 예외 클래스가 출력된다.
    } on 예외클래스 catch(e) {
     // 이렇게 계속해서 작성해갈 수 있다.
    } finally {
    	print("모든 작업이 완료되었습니다.");
    }
    // 가장 마지막에 작성하며 
    // try - catch 구문이 종료되었을 때 동작하게 된다.
    ```