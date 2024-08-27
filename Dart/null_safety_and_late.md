# Null-Safety and Late
* 한계 (2.12 버전 미만)
  * if 문의 한계
    * 여러 조건을 중첩하게 되면서 null 체크를 반복적으로 작성해야 했기에 가독성이 떨어지고, 중복된 코드가 다수 발생할 수 있다.
        ```dart
            String? name = 'gyl';
            int? age = 25;
            String? city = null;

            if (name != null) {
                if (age != null) {
                    if (city != null) {
                        // ...
                    } else {
                      // ...
                    }
                } else {
                  // ...
                }
            } else {
              // ...
            }    
        ```
  
* null-safety의 등장 (2.12 버전)
  * 개념
    * null과 관련된 안전성 즉, 관련 오류를 방지하기 위한 기능이다. 컴파일 타임에 null이 될 수 있는 변수와 그렇지 않은 변수를 명확히 구분해 null 참조 예외를 예방할 수 있다.
* nullable과 non-nullable
  * nullable 타입으로 선언하기 위해서는 '?'를 사용하여 표현한다.
    ```dart
        // nullable
        String? name;
        // non-nullable
        String name = 'gyl';
    ```
  * 기본적으로 모든 변수는 non-nullable 타입이다. 즉, null 값을 가질 수 없으며 할당시에 컴파일 오류가 발생한다.
  
* null-safety 연산자
  * '!'는 nullable 변수를 null이 아니라고 명시적 표현하기 위해 사용하는 연산자이다.
    ```dart
        String? name = 'gyl';
        String myName = name!;
    ```
  * '?.'는 객체가 null이 아닌 경우, 객체의 멤버에 접근하기 위한 연산자이다.
    ```dart
      String? name;
      print(name?.length); // null

      String? name = 'gyl';
      print(name?.length); // 3
    ```
  * '??'은 해당 nullable 변수가 null일 경우, 우변의 값을 반환하게 하는 연산자이다.
    ```dart
      String? name;
      print(name ?? 'Unknown'); // Unknown
    ```
  * '??='는 변수가 null일 경우, 우변의 값을 할당하는 연산자이다.
    ```dart
      String? name;
      name ??= 'gyl';
      print(name); // gyl

      // 만약 이미 할당된 값이 있다면 할당되지 않는다.
      String? name = 'gyl';
      name ??= 'lgy';
      print(name); // gyl
    ```
* null 체크
  * dart에서는 null 체크 이후에는 non-nullable로 간주된다.
    ```dart
      printNameLength(String? name) {
          if (name == null) {
              print(0);
          } else {
              // if 문에서 null 체크를 했기 때문에 else에서는 non-nullable로 취급된다.
              // 따라서 '?'와 같은 연산자를 사용하지 않아도 된다.
              print(name.length);
          }
      }
    ```
<br/>

# Late
  * 변수를 선언하되 초기화하지 않고, 후에 초기화하고자 할 때 사용한다.
    1. 변수 초기화에 필요한 데이터가 객체 생성 시점에 준비되지 않았을 경우
    2. 초기화에 시간이 오래 걸리거나 비용이 많이 드는 작업을 미뤄야 하는 경우
 * 주의 사항
   * late 변수를 사용하기 전에는 반드시 초기화가 되어있어야한다. 그렇지 않은 경우, 런타임 오류가 발생.  
 * 이점
   * 성능 최적화 : 필요한 시점에 초기화하여 초기화 비용을 절감할 수 있다.
   * 구글 공식 문서
     * 'late는 개발자가 null을 실수로 사용하는 것을 막아준다.'라고 설명하기도 했다. 
     * late 대신 nullable로 선언할 경우, 개발자가 다른 사람에게 코드를 위임했을 때, null 이라는 값이 의미있는 값(null이어도 되는 변수)이라고 오해할 수 있다. 따라서 late 키워드를 사용해 null 이 의미가 없음을 명시해야한다.

 * 예시 코드
   * 아래의 코드에서는 오류가 발생한다. _temperature에 대한 초기화를 진행하지 않았기 때문이다.
      ```dart
        class Tea {
          String _temperature; // error

          void heat() { _temperature = 'hot'; }
          void chill() { _temperature = 'iced'; }

          String serve() => _temperature + ' tea';
        }

        main() {
          var tea = Tea();
          tea.heat();
          print(tea.serve());
        }
      ```
    * 생성자를 만들어 hot 혹은 iced로 초기화를 진행할 수도 있지만 late 키워드를 통해서 변수의 초기화를 지연할 수 있다. (heat, chill을 호출함과 동시에 초기화하기 위함)
      ```dart
        class Tea {
          late String _temperature;
          // ...
        }

         main() {
          var tea = Tea();
          tea.heat();
          print(tea.serve()); // hot tea
        }
      ```
  
  * 참고 블로그
    * [붕어사랑님의 블로그](https://lucky516.tistory.com/185)