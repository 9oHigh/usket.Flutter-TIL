# Asynchronous Programming

* 표현
    ```dart
    // Dart에서는 Future<Type>으로 리턴해 사용한다.
    Future<String> getName() async {
        // ...
    }
    ```
  * Future는 미래에 완료될 작업을 나타내는 객체 즉, 단일 비동기 작업의 결과를 의미한다.
* Async와 Await
  * Async는 함수가 비동기 작업을 포함하고 있고, Future를 반환한다고 표시하는 키워드이다.
  * Await은 비동기 작업이 완료될 때까지 기다리는 키워드로 async 함수 내에서만 사용이 가능하다.
  * 예시 코드
      ```dart
      Future<String> getName() async {
          await Future.delayed(Duration(seconds: 2));
          return "myName";
      }     
      ```
* Then과 CatchError
  * then을 사용해 Future가 완료된 후 실행할 콜백을 지정할 수 있다.
  * catchError을 사용해 오류를 처리할 수 있다.
  * 예시 코드
    ```dart
    Future<String> getName() async {
        await Future.delayed(Duration(seconds: 2));
        return "myName";
    } 

    getName().then((_) {
        print("Get Name Success");
    }).catchError((error) {
        print("Error : $error");
    });
    ```
* Wait
  * Future 객체를 동시에 실행하고, 모든 Future가 완료될 때까지 기다리는 기능을 제공한다. 모든 Future가 완료되면 결과 리스트를 반환하거나, 하나의 Future라도 오류가 발생하면 오류를 반환하게 된다.
  * 예시 코드
    ```dart
    Future<void> printResults() async {
        Future<int> future1 = Future.delayed(Duration(seconds: 2), () => 1);
        Future<int> future2 = Future.delayed(Duration(seconds: 3), () => 2);
        Future<int> future1 = Future.delayed(Duration(seconds: 4), () => 3);

        List<Future<int>> results = await Future.wait([future1, future2, future3]);
        print(results); // [1, 2, 3]
    }
    ```
* Timeout
  * 비동기 작업이 일정 시간 내에 완료되지 않으면 작업을 취소하고, TimeoutException을 에러로 던진다.
  * 예시 코드
    ```dart
    Future<void> timeExceptionTest() async {
        Future<int> futureInt = Future.delayed(Duration(seconds: 5), () => 4);

        try {
            int number = await futureInt.timeout(Duration(seconds: 3));
            print(number);
        } catch (e) {
            print("Timeout Error: $e");
        }
    }
    ```
    * futureInt가 5초 후에 반환되지만 timeout을 3초로 설정했기 때문에 TimeoutException이 발생한다.
* whenComplete
  * Future가 완료된 후, 결과에 상관없이 항상 실행되는 콜백 함수이다.
  * 예시 코드
    ```dart
    Future<String> getName() async {
        await Future.delayed(Duration(seconds: 2));
        return "myName";
    } 

    getName().then((_) {
        print("Get Name Success");
    }).catchError((error) {
        print("Error : $error");
    }).whenComplete((_) {
        print("Completed");
    });
    // myName
    // Completed
    ```