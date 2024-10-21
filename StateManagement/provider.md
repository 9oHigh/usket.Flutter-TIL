# Provider
* 단순하고 효율적인 상태 관리를 제공한다.
* 구글에서 권장하는 상태 관리 패키지로 InheritedWidget을 기반으로 한 상태 관리 방법이다.
* UI와 앱의 상태를 중앙 집중식으로 관리하기 좋음. 즉, 전역 상태 관리에 좋음.
* InheritedWidget
    * 위젯 트리 내에서 데이터를 효율적으로 전달하기 위해 사용되는 클래스로, 일반적으로 위젯 트리의 상위에서 하위로 상태나 데이터를 전달하는데 사용되며, 하위 위젯이 상위 위젯의 상태가 변경될 때만 다시 빌드되도록 해준다.
    * 특징
    * 트리 구조 위쪽에 있는 위젯들이 하위 위젯들에 데이터를 전달할 수 있음
    * 데이터가 변경되면 그 데이터를 참조하는 하위 위젯만 다시 빌드
    * 일반적으로 직접 사용하기 보다는 상속받는 클래스를 만들어 사용
    * 예시 코드
        ```dart
        // InheritedWidget을 **상속받아** 구현
        class MyInheritedWidget extends InheritedWidget {
            final int count;
            final Widget child;

            MyInheritedWidget({required this.count, required this.child}) : super(child: child);

            // 데이터를 참조하는 위젯이 변경되었는지 확인하는 메서드
            @override
            bool updateShouldNotify(covariant MyInheritedWidget oldWidget) {
                return count != oldWidget.count;
            }

            // 상위 위젯에서 InheritedWidget을 찾고 데이터를 가져오는 메서드
            static MyInheritedWidget? of(BuildContext context) {
                return context.dependOnInheritedWidgetOfExactType<MyInheritedWidget>();
            }
        }

        class MyApp extends StatefulWidget {
            @override
            _MyAppState createState() => _MyAppState();
        }

        class _MyAppState extends State<MyApp> {
            int _count = 0;

            void _incrementCounter() {
                setState(() {
                    _count++;
                });
            }

            @override
            Widget build(BuildContext context) {
                return MaterialApp(
                    home: Scaffold(
                        appBar: AppBar(title: Text('InheritedWidget')),
                        body: MyInheritedWidget(
                            count: _count,
                            child: CounterDisplay(),
                        ),
                        floatingActionButton: FloatingActionButton(
                            onPressed: _incrementCounter,
                            child: Icon(Icons.add),
                        ),
                    ),
                );
            }
        }

        class CounterDisplay extends StatelessWidget {
            @override
            Widget build(BuildContext context) {
                // InheritedWidget에서 count 데이터를 가져와 사용
                final int count = MyInheritedWidget.of(context)?.count ?? 0;
                return Center(
                        child: Text(
                        'Count: $count',
                        style: TextStyle(fontSize: 24),
                        ),
                    );
            }
        }
        ```
* 특징
  * 상태 공유 - 여러 위젯 트리에서 상태를 쉽게 공유할 수 있다. (의존성 주입)
  * 컨텍스트 사용 - BuildContext를 통해 상태를 찾고 제공 받을 수 있다.
  * 리액티브 프로그래밍 - 상태 변화에 따라 UI를 자동으로 업데이트
  * 재사용 가능성 - 상태와 로직을 분리하여 더 재사용 가능하고 테스트 가능한 코드를 작성할 수 있다.
* 개념
  * ChangeNotifier: 상태가 변경될 때 UI를 업데이트할 수 있도록 도와주는 클래스
  * ChangeNotifierProvider: ChangeNotifier를 사용해 상태관리를 도와준다. 상태를 관리하고, 하위 위젯에 상태를 주입해주는 역할.
  * Consumer: Provider로부터 상태를 가져와 UI를 업데이트하는 위젯
  * Selector: 상태의 일부만 구독하여 필요한 부분만 재빌드 할 수 있다.
    * 상태 객체의 일부만 선택하여 위젯을 빌드하고, 선택된 부분이 변경될 때만 리빌드를 트리거하는 데 사용한다. 이를 통해서 불필요한 위젯의 리빌드를 방지하여 성능을 최적화 할 수 있다.
* Provider의 종류
  * Provider: 단순한 값을 제공하는 가장 기본적인 형태이다.
  * FutureProvider: 비동기 작업을 처리하고 해당 결과를 상태로 관리할 경우, 사용한다.
    * API 호출, 파일 읽기 등 한 번만 실행되며 결과를 기다리는 작업에 적합하다. Future를 사용하여 데이터를 가져오고, 이 데이터를 UI에 사용할 수 있다.
  * StreamProvider: 스트림을 사용하여 상태를 관리할 때 사용한다.
    * 데이터의 연속적인 흐름을 처리하는데 사용하며, 실시간 채팅, 데이터베이스 실시간 업데이트 등을 처리할 때 적합하다.
  * ScopeProvider: 특정 스코프 내에서 상태를 공유하는 데 사용한다.
  * MultiProvider: 여러 개의 Provider를 동시에 사용하는 방식으로 상태 관리와 의존성 주입을 간단하고 효율적으로 관리하기 위해 사용한다. 특히, 복잡한 애플리케이션에서 여러 객체나 서비스를 위젯 트리의 다른 부분에서 공유할 필요가 있을 때 유용하다.
    * 예시
        ```dart
        MultiProvider(
            providers: [
                Provider<CounterService>(
                    create: (context) => CounterService(),
                ),
                ChangeNotifierProvider<SettingsModel>(
                    create: (context) => SettingsModel(),
                ),
                Provider<ApiClient>(
                    create: (context) => ApiClient(),
                ),
            ],
            child: MyApp(),
        );
        ```
        * 만약, 설정된 프로바이더들 사이에 의존성이 존재할 경우, 순서를 적절히 설정하여 상위 Provider의 컨텍스트에서 하위 Provider를 생성하게 만들면 된다. (ProxyProvider 사용)
* 장단점
  * 장점 
    * 간편한 사용방법
    * 높은 성능
    * Flutter의 철학과 잘 맞는 리액티브 프로그래밍
  * 단점
    * 대규모 애플리케이션에서는 복잡해질 수 있다.
* 예시 코드
    ```dart
    import 'package:provider/provider.dart';

    // ChangeNotifier를 상속받은 카운터 클래스
    class Counter with ChangeNotifier {
        int _count = 0;

        int get count => _count;

        void increment() {
            _count++;
            notifyListeners(); // 상태가 변경되었음을 알림
        }
    }

    void main() {
        runApp(
            // ChangeNotifierProvider를 사용하여 Counter 클래스를 제공
            ChangeNotifierProvider(
                create: (context) => Counter(),
                child: MyApp(),
            ),
        );
    }

    class MyApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return MaterialApp(
                home: Scaffold(
                    appBar: AppBar(title: Text('Provider Example')),
                    body: Center(
                        child: Column(
                            mainAxisAlignment: MainAxisAlignment.center,
                            children: <Widget>[
                                Text('count:'),
                                // Consumer를 사용하여 상태에 반응하는 위젯을 만듦
                                Consumer<Counter>(
                                    builder: (context, counter, child) {
                                        return Text(
                                            '${counter.count}',
                                            style: TextStyle(fontSize: 48),
                                        );
                                    },
                                ),
                            ],
                        ),
                    ),
                    floatingActionButton: FloatingActionButton(
                        // 상태에 접근하여 카운트를 증가시키는 메서드 호출
                        onPressed: () => Provider.of<Counter>(context, listen: false).increment(),
                        child: Icon(Icons.add),
                    ),
                ),
            );
        }
    }
    ```