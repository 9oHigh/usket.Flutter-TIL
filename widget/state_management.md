# State Management

* 개념
  * 상태는 UI를 다시 빌드하는데 필요한 모든 데이터라고 할 수 있다. 가령 카운터 앱에서는 카운터 값(데이터)이 상태에 해당된다.
  * 카운트 값을 증가시키거나 감소시키는 등의 이벤트가 발생했을 때, 이를 관리하는 기법을 상태관리라고 한다.

### Stateless Widget과 Stateful Widget
* 이름 그대로 Stateless Widget은 상태관리 개념이 없는 위젯을 말한다. 반면에 Stateful Widget은 위젯의 데이터가 변경될 수 있으며, 이에 따라 UI가 다시 그려질 수 있는 위젯이다.
* Stateful Widget
  * Stateful Widget을 구현하기 위해서는 Stateful Widget 클래스와 State 클래스 두가지가 필요하다.
  * Stateful Widget 클래스는 immutable 하기에 상태를 직접 저장하거나 변경하지 않고, 화면에 표시할 레이아웃을 정의만 한다.
  * State 클래스는 mutable 하며, 상태를 저장 및 관리한다. 상태가 변경될 경우, 화면을 다시 그리기 위해 사용된다.

### 대표적인 위젯들
* Stateless Widget : Text, Row, Column, Icon, Container, Image, Scaffold, AppBar 등등
* Stateful Widget : TextField, Checkbox, Slider, ListView 등등

### 상태 관리
* 그렇다면 상태가 변경되었을 때, 어떻게 화면을 다시 그릴까? 바로 setState 메서드를 호출하면 된다.
* setState()
  * 위젯의 상태가 변경되었음을 알리는 메서드로, setState를 호출하면 해당 위젯이 다시 build 메서드를 호출하여 UI를 갱신한다.

### 상태 관리와 위젯의 생명 주기
* Stateful Widget의 lifecycle

  ![alt text](https://velog.velcdn.com/images/seoyeonn05/post/aa42b6f4-e975-499b-9516-38932abef8d5/image.png)
  1. 위젯의 상태를 생성하는 createState()
       * Stateful Widget을 빌드하라는 지시를 받으면 createState()를 호출해 상태를 생성한다. 이 함수를 통해 다른 생명주기 함수들이 포함된 State 객체를 생성해 위젯의 상태를 생성한다.
         ```dart
         class MyHomePage extends StatefulWidget {
           @override
           _MyHomePageState createState() => new _MyHomePageState();
         }
         ```
   2. mounted
        * 위젯에는 mounted 라는 속성이 있는데 현재 화면에 띄워져 있는지 확인하는 전역 변수다. createState()를 통해 상태를 생성하면 해당 상태에 buildContext 가 할당 된다. (buildContext는 위젯이 배치되는 위젯 트리에서의 위치다.) buildContext가 할당되면 mounted 는 true가 된다. (위젯이 unmounted일 때, setState를 호출하면 오류가 발생한다.)
        * 주로 비동기 작업을 수행할 때, 혹은 상태 변경 작업을 수행하기 전에 상태 객체가 여전히 휴효한지 확인할 때 사용한다.

   3. initState()
        * State 객체 생성시 생성자가 호출되어 State를 초기화 시키는 함수이다. Stateful Widget이 생성될 때, 단 한 번만 호출된다. 이 함수를 다시 호출하기 위해서는 Stateful Widget 을 파괴한 이후 다시 생성해야 한다. controller를 설정하거나 listener를 설정하는데 사용하며, 반드시 super.initState()를 호출해야 한다.
        * initState()가 정상적으로 호출었다면 mounted 는 true가 된다.
           ```dart
             @override
             void initState() {
               scrollController.addListener(() {
                 if (scrollController.offset >= 600) {
                   if (!_isVisibility) {
                     _isVisibility = true;
                   }
                 } else if (scrollController.offset < 600) {
                   if (_isVisibility) {
                     _isVisibility = false;
                   }
                 }
               });
               super.initState();
             }
           ```

   4. didChangeDependencies()
        * 위젯이 생성될 때, initState 메서드 호출 이후 바로 didChangeDependencies 가 호출된다. 또한, 위젯이 의존하고 있는 데이터가 호출될 경우에도 함께 호출된다.
        * InheritedWidget과 같은 종속성이 변경되었을 때 사용된다.
          * InheritedWidget
            * 상위 위젯에서 하위 위젯으로 데이터를 전달하는 데 사용되는 Widget이다. 이를 통해 데이터를 전달하면, 하위 위젯들은 상위 위젯에서 데이터가 변경될 때 자동으로 업데이트된다. ( 특정한 조건을 부합한 경우 )
                ```dart
                class FrogColor extends InheritedWidget {
                    const FrogColor({
                        super.key,
                        required this.color,
                        required super.child,
                    });

                    final Color color;

                    static FrogColor? maybeOf(BuildContext context) {
                        return context.dependOnInheritedWidgetOfExactType<FrogColor>();
                    }

                    static FrogColor of(BuildContext context) {
                        final FrogColor? result = maybeOf(context);
                        assert(result != null, 'No FrogColor found in context');
                        return result!;
                    }

                    @override
                    bool updateShouldNotify(FrogColor oldWidget) => color != oldWidget.color;
                }
                ```
                * 위젯 트리의 원하는 위치에 FrogColor를 생성하고, child를 포함한 모든 하위 Widget들은 of 메서드를 통해 FrogColor에 접근할 수 있게 되는 것이다.
                * updateShouldNotify는 oldWidget의 특정한 데이터와 현재 데이터를 비교해 UI를 변경할지 말지를 결정해 하위 위젯들에게 다시 build 할지 말지를 알려주는 메서드다. 즉, true 경우 하위 위젯이 다시 build 된다.

   5. build()
        * 이 메서드를 통해 위젯이 그려진다. State 클래스에서 반드시 오버라이딩 되어야 하며 Widget을 반환 해야한다.

   6. didUpdateWidget(Widget oldWidget)
        * 부모 위젯이 변경되어 이 위젯이 갱신될 때 호출된다. Flutter 에서는 State를 재사용하기 때문에 initState() 에서처럼 일부 데이터를 다시 초기화 해야 한다.
        * 이 함수 호출 후, build() 를 호출한다. 따라서 didUpateWidget() 내에서 setState() 호출시 build가 중복 호출이 된다.

   7. setState()
        * 상태가 변경되었을 때 호출하는 메서드다. setState 를 호출하면 build() 가 다시 실행된다. 이 때, 모든 위젯이 변경 된다. (Futurebuild 같은 데이터를 불러오는 것도 다시 불러오게 된다. 따라서 setState은 최대한 지양하고, statelessWidget을 사용하는 것을 권장한다고 한다.)
          * 만약 setState 가 호출되었을 때 상태 객체가 dirty 상태일 경우, Flutter의 스케줄러에서는 dirty 상태의 위젯들을 다시 빌드하도록 준비 상태에 있다가 다음 프레임이 렌더링 될 때, build 메서드를 호출하여 최신 상태를 반영한다.

   8. deactivate()
        * 위젯 트리에서 State 가 제거될 때 호출된다.

   9. dispose()
        * 화면이 종료될 때 호출되어 State 객체가 영구히 제거된다. 보통 controller를 종료할 때 사용한다.

   10. mounted is false
         * 이 상태에서는 state 객체가 다시 mount 되지 않으며, setState() 호출시 에러가 발생한다.