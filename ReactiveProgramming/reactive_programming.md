# Reactive Programming
* 개념
  * 데이터의 변화를 감지하고 이에 따라 자동으로 업데이트되도록 하는 프로그래밍 패턴을 말한다.

* 데이터 스트림과 비동기 데이터 처리
  * Dart에서는 Stream과 StreamController를 사용해 반응형 프로그래밍을 구현할 수 있다. 즉, 데이터 변경 사항을 구독하고, 이벤트를 처리하며, 이에 따른 UI를 업데이트할 수 있다.
  * StreamController
    * Stream을 제어하는 도구로 sink, stream 두 개의 object를 가진다.
    * sink: 스트림에 이벤트를 추가하기 위한 입력 인터페이스로 add, addError, close 메서드를 사용하여 수동으로 이벤트를 추가하거나 스트림을 닫을 수 있다.
    * stream: 스트림을 반환한다. 리스너가 이 스트림을 구독하여 이벤트를 수신한다.
  * 예시
    ```dart
    final StreamController<int> streamController = StreamController<int>();

    streamController.stream.listen((data) {
        print("Received: $data")
    });

    streamController.sink.add(1);
    streamController.add(4);

    streamController.close();
    ```
* RxDart
  * Dart의 Stream API를 확장하여 더 많은 연산자와 기능을 제공하여, 반응형 프로그래밍을 더 쉽게 구현할 수 있게 한다.
  * Streams
    * 기본 스트림을 확장하여 다양한 연산을 수행
  * Subject
    * 스트림의 여러 특징을 결합한 타입의 스트림으로 PublishSubject, BehaviorSubject 등이 있다.
        ```dart
        final publishSubject = PublishSubject<int>();
        final behaviroSubject = BehaviorSubject<int>();

        publishSubject.add(1); // 구독 이전이므로 무시

        publishSubject.listen((value) {
            print("publishSubject:  $value");
        });

        behaviorSubject.add(1);
        behaviorSubject.add(2); // 가장 최신값을 기억

        behaviorSubject.listen((value) {
            print("behaviorSubject:  $value");
        });

        behaviorSubject.add(3); // 구독 이후 추가되었으므로 최신값 다음으로 방출
        publishSubject.add(2); // 구독한 상태이므로 방출

        /*
        behaviorSubject:  2
        behaviorSubject:  3
        publishSubject:  2
        */

        ```
      * PublishSubject: 구독한 시점의 항목만 받는다.
      * BehaviorSubject: 최신 항목을 가지고 있으며, 구독 시점에서 이 최신 항목부터 시작한다.
      * ReplaySubject: 버퍼 크기만큼의 항목을 가지고, 구독 시점부터 버퍼에 있는 모든 항목을 재생한다.
      * AsyncSubject: 스트림이 완료될 때까지 대기했다가 마지막 항목만 내보낸다.
    * Stream + StreamController의 역할을 Subject가 할 수 있다. 즉, 데이터 소스이자 스트림의 소비자 역할을 동시에 수행하여 이벤트를 추가하고, 구독할 수 있다.
  * Operators
    * 스트림의 데이터를 변환하거나 결합하는데 사용하는 메서드를 말한다.
    * map, filter 등은 동일하게 사용 가능
    * combineLatest
      * 여러 스트림의 최신 값을 결합하여 새로운 스트림을 생성한다.
        ```dart
        final subject1 = BehaviorSubject<int>();
        final subject2 = BehaviorSubject<String>();

        // combineLatest로 두 스트림의 최신 값을 결합
        Rx.combineLatest2(subject1, subject2, (int a, String b) {
            return '$a - $b';
        }).listen((value) {
            print('Combined: $value');
        });

        subject1.add(1);
        subject2.add('Hello'); // Combined: 1 - Hello
        subject1.add(2); // Combined: 2 - Hello
        subject2.add('World'); // Combined: 2 - World
        ```
    * 이 외에도 다양한 operator 들이 존재한다.

* RxDart와 Widget
  * StreamBuilder를 사용해 데이터를 처리하고, 이에 맞는 UI를 동적으로 업데이트하는데 사용되는 위젯이다. 스트림을 구독하고, 스트림 데이터의 상태가 변경됨에 따라 빌더 함수를 호출하여 UI를 다시 그리는 원리.
  * 예시 코드 ( 카운터 앱 )
    ```dart
    final BehaviorSubject<int> _counterSubject = BehaviorSubject<int>.seeded(0);

    void _incrementCounter() {
        // 현재 값에 1을 더해 스트림에 전송
        _counterSubject.add(_counterSubject.value + 1);
    } 

    // ...

    @override
    Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(
                title: Text('카운터 앱'),
            ),
            body: Center(
                child: StreamBuilder<int>(
                stream: _counterSubject.stream,
                initialData: _counterSubject.value,
                builder: (context, snapshot) {
                    // snapshot.data는 현재 스트림에서 전송된 값
                    return Text(
                    'Counter: ${snapshot.data}',
                    style: TextStyle(fontSize: 30),
                    );
                },
                ),
            ),
            floatingActionButton: FloatingActionButton(
                onPressed: _incrementCounter,
                child: Icon(Icons.add),
            ),
        );
    }

    // ...
    ```