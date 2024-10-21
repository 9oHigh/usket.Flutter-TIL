# SharedPreference
* 개념
  * Flutter 애플리케이션에서 간단한 데이터를 앱이 삭제되기 이전까지 영구적으로 저장하는 데 사용되는 키-값 저장소이다.
  * 주로 사용자 설정이나 간단한 상태 정보 등을 저장한다.
  * Flutter에 기본적으로 내장되어있지는 않기 때문에 설치가 필요하다.

* 사용 방법
  * getInstance를 통해 인스턴스를 불러와 데이터를 저장하기
    ```dart
    Future<void> saveName(String name) async {
        final SharedPreference pref = await SharedPreference.getInstance();
        // int, double, bool, string, List<String> 등을 저장할 수 있다.
        await pref.setString('name', name); 
    }
    ```
    * 비동기적으로 동작하기 때문에 await 키워드를 사용해야한다.
      * Future : 미래에 완료될 작업을 나타내는 객체
      * Async : 함수가 비동기 작업을 수행할 것임을 표현하는 키워드
      * Await : 비동기 작업이 완료될 때까지 기다리는 키워드
    * 다시 말해, Future<void>를 반환하는 메서드인 saveName은 비동기(async) 작업을 한다는 것을 알 수 있고, SharedPreference의 인스턴스를 가지고 오는 비동기 작업이 완료될 때까지 기다린다는 의미이다.

  * 데이터 가져오기
    ```dart
    final SharedPreference pref = await SharedPreference.getInstance();
    String name = pref.getString('name') ?? 'Unknown';
    ```
    * SharedPreference에서 get 메서드들은 기본적으로 Nullable한 값을 반환하기 때문에 Null Safety를 활용해 기본값을 설정할 수 있다.

  * 데이터 삭제하기
    ```dart
    final SharedPreference pref = await SharedPreference.getInstance();
    await pref.remove('name');
    ```