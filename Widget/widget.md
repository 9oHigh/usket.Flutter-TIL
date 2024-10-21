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