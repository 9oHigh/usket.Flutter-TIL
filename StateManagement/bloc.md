# BLoC
* Business Logic Component
* 비즈니스 로직을 UI 코드에서 분리하는 것을 목표로 하는 Flutter의 상태 관리 패턴
* 개념
  * 단방향 데이터 흐름: 이벤트와 상태를 통해서 데이터를 처리하고 UI를 업데이트한다.
  * 비동기 프로그래밍: Stream과 Sink를 사용하여 비동기적으로 상태 관리가 가능하다.
  * 리액티브 프로그래밍: 상태가 변경될 때마다 UI가 자동으로 업데이트 된다.
* 장단점
  * 장점
    * 명확한 구조: 이벤트와 상태의 명확한 분리로 인해 코드의 가독성과 유지 보수성이 향상
    * 테스트 용이: 비즈니스 로직이 UI에서 분리되어 있어 단위 테스트 작성이 용이
    * 확장성: 대규모 애플리케이션에서도 일관성 있는 구조 유지 가능
  * 단점
    * 초기 설정의 복잡성
    * 코드 양 증가: 작은 기능을 구현하더라도 많은 파일과 코드가 필요하게 된다.
* 구성 요소
  * Event: 사용자의 행동이나 앱 내에서 발생하는 액션들의 정의해 사용한다.
  * State: 현재의 상태
  * Bloc: 이벤트를 받고 비즈니스 로직을 처리하여 새로운 상태를 출력하는 중재자로서의 역할을 한다.
* 주요 메서드 및 Stream
  * emit
    * BLoC 패턴에서 상태를 업데이트 할 떄 사용되는 메서드
    * Bloc 내에서 호출되며, 새로운 상태를 스트림으로 방출하여 상태를 변경한다.
    * 상태가 변경되면, 이 상태를 구독하고 있는 위젯들이 변경된 상태를 다시 렌더링한다.
  * add
    * 이벤트를 추가하여 이를 처리하고, 새로운 상태를 방출한다.
  * stream
    * 비동기 데이터를 다루는 Dart의 중요한 개념 중 하나로 상태 변경을 스트림을 통해서 처리한다.
    * 이벤트와 상태 간의 데이터를 비동기적으로 전달하는 역할
    * 즉, 상태를 스트림으로 전달하여 UI 컴포넌트들이 이를 구독하고 상태 변화에 반영한다.
* 위젯
  * BlocBuilder
    * Bloc 상태의 변화를 감지하여 UI를 리빌드하는 데 사용한다.
    * 예시
        ```dart
        BlocBuilder<CounterBloc, CounterState> (
            builder: (context, state) {
                if (state is CounterInitial) {
                    return Text("초기 상태");
                } else if (state is CounterIncremented) {
                    return Text("Counter: ${state.counterValue}");
                } else {
                    return CircularProgressIndicator();
                }
            }
        );
        ```
  * BlocProvider
    * Bloc 객체를 위젯 트리에 제공하고, 하위 위젯에서 이를 사용할 수 있게한다.
      * 예시
        ```dart
        BlocBuilder(
            create: (context) => CounterBloc(),
            child: CounterPage(),
        );
        ```
  * BlocSelector
  * BolocConsumer
  * 이외에도 많은 위젯을 제공한다.

* 간단한 예제
    ```dart
    // Event 정의
    abstract class ColorEvent {}

    class ChangeColor extends ColorEvent {}

    // State 정의
    class ColorState {
        final Color color;
        ColorState(this.color);
    }

    // Bloc 정의
    class ColorBloc extends Bloc<ColorEvent, ColorState> {

        ColorBloc() : super(ColorState(Colors.white)) {
            on<ChangeColor>((event, emit) {
                emit(ColorState(_generateRandomColor()));
            });
        }

        Color _generateRandomColor() {
            return Color.fromRGBO(
                Random().nextInt(256),
                Random().nextInt(256),
                Random().nextInt(256),
                1,
            );
        }
    }

    class MyApp extends StatelessWidget {
        const MyApp({super.key});

        @override
        Widget build(BuildContext context) {
            return MaterialApp(
                title: '컬러 생성기',
                home: BlocProvider(
                    create: (context) => ColorBloc(),
                    child: const ColorChangerScreen(),
                ),
            );
        }
    }

    class ColorChangerScreen extends StatelessWidget {
        const ColorChangerScreen({super.key});

        @override
        Widget build(BuildContext context) {
            return Scaffold(
                appBar: AppBar(title: const Text("Color Changer")),
                body: BlocBuilder<ColorBloc, ColorState>(
                    builder: (context, state) {
                        return GestureDetector(
                            onTap: () {
                                context.read<ColorBloc>().add(ChangeColor());
                            },
                            child: AnimatedContainer(
                                duration: const Duration(milliseconds: 300),
                                color: state.color,
                                child: const Center(
                                    child: Text(
                                        'Tap anywhere to change color!',
                                        style: TextStyle(fontSize: 24, color: Colors.black),
                                    ),
                                ),
                            ),
                        );
                    },
                ),
            );
        }
    }
    ```