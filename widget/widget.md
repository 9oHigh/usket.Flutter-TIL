# Widget
* 개념
  * Flutter에서 Widget은 UI의 구성 요소다. 화면의 모든 구성 요소를 생성하고 배치하는데 사용된다.
* Widget의 종류
  * MaterialApp / CupertinoApp
    * 앱의 초기화면을 구성하는 Widget으로 MaterialApp은 구글의 Material Design을 사용한 앱을 개발할 때 사용하고, CupertinoApp은 애플의  Design을 사용한 앱을 개발할 때 사용한다. 다만, 반드시 사용해야하는 것은 아니다.
    * main 함수의 runApp의 인수로 전달되어 사용된다.
        ```dart
            void main() {
              runApp(
                MaterialApp(
                  home: Text('앱 이름'),
                )
              );
            }
        ```
  * Scaffold
    * 기본적인 material design의 디자인 레이아웃 구조를 구현하는데 사용하는 위젯이다.
    * 위에서 소개했던 코드를 실행한다면 검정 화면 그리고 좌측 상단에 '앱 이름'이라는 텍스트만 보이는 것을 확인할 수 있다. 하지만 Scaffold로 감싼 위젯을 home에 전달하면 기본적인 구조를 가져갈 수 있다.
      * 다양한 하위 위젯을 가지고 있는데 대표적으로 appBar와 body가 있으며, floating button, bottomNavigationBar (TabBar) 등이 있다.
        ```dart
          void main() {
            runApp(
              MaterialApp(
                home: Scaffold(
                  appBar: AppBar(
                    title: const Text('앱 이름'),
                  ),
                  body: Center(
                    child: const Text('앱 이름'),
                  ),
                )
              )
            );
          }
          ```
          * 실행해보면 appBar와 화면 중앙에 '앱 이름'이라는 텍스트가 위치한 것을 확인할 수 있다.
    * 이 외에도
      * Text, TextField, Button ( Text, Elevated, Outline ), Row, Column 등이 있다.
    
* Widget의 분류
  * State를 가지는지의 여부에 따라 두 가지로 나눈다. 
    * Stateless Widget
      * 데이터의 변화와 같은 인터랙션에도 상태의 변경이 필요없는 위젯을 말한다.
      * 다시 말해 정적으로 보여주는 위젯에 사용하면 된다.
    * Stateful Widget
      * 상황에 따른 상태의 변경이 필요한 위젯을 말한다.
      * Stateless 위젯의 경우, build 이후, 변경이 필요없기 때문에 다른 화면으로 넘어가거나 더이상 화면에 보이지 않을 경우, 위젯 트리에서 제거가 되게 된다. 이에 반해 stateful 위젯은 생명 주기를 가지고 있어 주기에 맞게 생성, 빌드, 비활성, 파괴 등이 이루어진다.
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

     3. initState()
          * State 객체 생성시 생성자가 호출되어 State를 초기화 시키는 함수이다. Stateful Widget이 생성될 때, 단 한 번만 호출된다. 이 함수를 다시 호출하기 위해서는 Stateful Widget 을 파괴한 이후 다시 생성해야 한다. controller를 설정하거나 listener를 설정하는데 사용한다.
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
          * 가령 업데이트 되는 위젯을 상속한 경우에 API 호출과 같은 경우가 있을 수 있다.

     5. build()
          * 이 메서드를 통해 위젯이 그려진다. State 클래스에서 반드시 오버라이딩 되어야 하며 Widget을 반환 해야한다.

     6. didUpdateWidget(Widget oldWidget)
          * 부모 위젯이 변경되어 이 위젯이 갱신될 때 호출된다. Flutter 에서는 State를 재사용하기 때문에 initState() 에서처럼 일부 데이터를 다시 초기화 해야 한다.
          * 이 함수 호출 후, build() 를 호출한다. 따라서 didUpateWidget() 내에서 setState() 호출시 build가 중복 호출이 된다.

     7. setState()
          * 상태가 변경되었을 때 호출하는 메서드다. setState 를 호출하면 build() 가 다시 실행된다. 이 때, 모든 위젯이 변경 된다. (Futurebuild 같은 데이터를 불러오는 것도 다시 불러오게 된다. 따라서 setState은 최대한 지양하고, statelessWidget을 사용하는 것을 권장한다고 한다.)

     8. deactivate()
          * 위젯 트리에서 State가 제거될 때 호출된다.

     9. dispose()
          * 화면이 종료될 때 호출되어 State 객체가 영구히 제거된다. 보통 controller를 종료할 때 사용한다.

     10. mounted is false
           * 이 상태에서는 state 객체가 다시 mount 되지 않으며, setState() 호출시 에러가 발생한다.