# Sealed Class
### 개념
* Dart에서 Sealed Class는 클래스의 상속을 제한하는 기능을 제공한다.
* 특정 클래스 계층 구조를 정의하고 외부에서는 이를 확장하지 못하도록 한다. 즉, 다른 파일에서는 접근이 불가능하다.
* 추상 클래스 + 부분별한 상속을 제한

### 사용
* 제한 사항
  * Sealed 클래스를 상속받는 클래스는 반드시 sealed 클래스와 같은 파일 혹은 라이브러리에 정의되어야 한다. 또한, 클래스의 인스턴스를 직접적으로 생성할 수 없고, 하위 클래스를 통해서만 인스턴스화가 가능하다.

* 예시 코드
  ```dart
    sealed class ListViewState { }

    class LoadingState extends ListViewState { }

    class LoadedState extends ListViewState {
        final String data;
        LoadedState(this.data);
    }

    class ErrorState extends ListViewState {
        final String message;
        ErrorState(this.message);
    }

    void main() {
        final ListViewState state = LoadedState('로딩이 완료된 데이터');
        switch (state) {
            case LoadingState():
                print('로딩 중입니다.');
            case LoadedState(data: String data):
                print('로딩이 완료되었습니다.\nData: $data');
            case ErrorState(message: String errMsg):
                print('에러가 발생했습니다.\nError: $errMsg');
        }
        // 로딩이 완료되었습니다.
        // Data: 로딩이 완료된 데이터
    }
  ```
  * Switch Expression
    * 위의 switch 코드를 다음과 같이 작성할 수도 있다.
        ``` dart
        void main() {
            final ListViewState state = LoadedState('로딩이 완료된 데이터');
            final message = switch (state) {
                LoadingState() => '로딩 중입니다.',
                LoadedState(data: String data) => '로딩이 완료되었습니다.\nData: $data',
                ErrorState() => '에러가 발생했습니다.\nError: $errMsg'
            };
            print(message);
            // 로딩이 완료되었습니다.
            // Data: 로딩이 완료된 데이터
        }
        ```