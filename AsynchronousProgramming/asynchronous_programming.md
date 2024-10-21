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
* Stream
  * 비동기 이벤트의 시퀀스로 비동기 Iterable이라고 생각하면 된다.
  * 이벤트 수신
    * for을 사용
      * await 키워드와 함께 사용해 데이터들을 비동기적으로 처리할 수 있다.
        ```dart
        Future<int> sumStream(Stream<int> stream) async {
            int sum = 0;
            await for (var value in stream) {
                sum += value;
            }
            return sum;
        }

        ```
  * 구독
    * listen 키워드를 이용해 스트림을 구독하고 이벤트를 처리할 수 있다.
        ```dart
        Stream<int> stream = Stream.periodic(Duration(seconds: 1), (count) => count + 1).take(4);

        stream.listen((data) {
            print('Data: $data');
        });
        ```
    * 기본적으로 모두 Sigle-subscription이므로, 한 번에 하나의 리스너만 허용한다. 즉, 한 번 구독되면 Stream이 완료될 때까지 다른 리스너가 구독할 수 없다.

  * StreamSubscription
    * listen 메서드의 반환 값이 StreamSubscription이다.
    * 스트림의 구독을 취소, 정지, 재개, 처리 작업을 할 수 있다.
    * 예시 코드
        ```dart
        Stream<int> stream = Stream.periodic(Duration(seconds: 1), (count) => count + 1).take(100);

        StreamSubscription<int> mySubscription = stream.listen((data) {
                print('Data: $data');
            },
            onDone: () => print("Stream completed")
        );

        Future.delayed(Duration(seconds: 3), () {
            mySubscription.pause(); // 3초 뒤에 정지

            Future.delayed(Duration(seconds: 3), () {
                mySubscription.resume(); // 다시 3초 뒤에 재개

                Future.delayed(Duration(seconds: 3), () {
                    mySubscription.cacel(); // 다시 3초 뒤에 취소
                });
            });
        });
        ```
  * Stream 변환하기
    * 기존 Stream의 이벤트를 기반으로 새로운 Stream을 만들어 사용할 수 있다. 대표적으로 map, where, asyncExpand를 사용할 수 있다.
    * map
      * 이름 그대로 요소들을 변환하거나 조작하는 등 매핑해 사용하는 메서드로 각각의 요소들을 변환하여 새로운 컬렉션을 반환한다.
        ```dart
        Stream<int> stream = Stream.periodic(Duration(seconds: 1), (count) => count + 1).take(100);

        stream.map((data) => data * 2).listen((data) {
            print("Mapped Data: $data");
        });
        ```
    * where
      * 컬렉션의 요소 중에서 특정 조건에 부합하는 요소들만 필터링하여 새로운 컬렉션을 반환하는데 사용한다.
        ```dart
         Stream<int> stream = Stream.periodic(Duration(seconds: 1), (count) => count + 1).take(100);

        stream.where((data) => data % 2 == 0).listen((data) {
            print("Filtered Data: $data");
        });
        ```
    * asyncExpand
      * 각 요소들을 비동기적으로 처리하여 여러 개의 요소를 생성할 수 있는 스트림으로 확장할 수 있다. 

* 제너레이터
  * Stream을 생성하는데 사용된다.
    * Synchronous generator, sync*
      * sync* 키워드로 정의하고, Iterator를 반환한다.
      * 예시
        ```dart
        Iterable<int> generateNumbers(int maxValue) sync* {
            for (int i = 0; i <= maxValue; i++) {
                yield i; // 함수 내에서 값을 반환할 때 사용한다.
            }
        }

        Iterable<int> myNumbers = generateNumbers(4);

        for (int num in myNumbers) {
            print(num); // 0, 1, 2, 3, 4
        }
        ```
    * Asynchronous generator, async*
      * async* 키워드로 정의하고, Stream을 반환한다.
      * 예시
        ```dart
        Stream<int> generateNumbers(int maxValue) async* {
            for (int i = 0; i <= maxValue; i++) {
                await Future.delayed(Duration(seconds: 1));
                yield i;
            }
        }

        Stream<int> myNumbers = generateNumbers(4);

        for (int num in myNumbers) {
            print(num); // 0, 1, 2, 3, 4 ( 1초 간격으로 )
        }
        ```
    * yield*
      * 다른 제너레이터를 위임할 때 사용한다.
      * 예시
        ```dart
        Iterable<int> generateNumbers(int maxValue) sync* {
            for (int i = 0; i <= maxValue; i++) {
                yield i; // 함수 내에서 값을 반환할 때 사용한다.
            }
        }

        Iterable<int> addNumber(int maxValue) sync* {
            yield* generateNumbers(maxValue);
            yield maxValue + 1;
        }
        ```