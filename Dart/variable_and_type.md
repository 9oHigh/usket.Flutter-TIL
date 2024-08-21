# 변수와 타입
- var과 dynamic
    - var의 경우, 추론된 타입이 한 번 정해진 상태에서 다른 타입의 값을 할당했을 때 컴파일 에러가 발생한다.
    - 하지만 dynamic의 경우, 컴파일 타임에 다른 타입의 값을 할당하더라도 문제가 발생하지 않는다. 런타임 에러가 발생할 확률이 있으므로 타입을 명시적으로 작성하는 것이 좋다.
- final과 const
    - 공통점
        - 한 번 값을 할당하면 변경이 불가능하다.
    - 차이점
        - final의 경우, 런타임에 값이 결정이 된다. 즉, 소스코드를 실행 중일 때, 값이 설정이되고 이후에 재할당이 불가능하다는 것이다.
        - const의 경우, 컴파일 타임에 값이 결정된다. 즉, 소스 코드 실행 중에는 재할당이 불가능하다는 것을 의미한다.

    - 왜 final이 아닌 const를 사용하는 것일까?
        - 최적화 때문이다. 앞서 말했듯이 Const의 경우, 컴파일 타임에 결정이 되기 때문에 앱을 시작할 때, 한 번만 할당이 된다. 따라서 특정한 화면에서 리랜더링이 진행되더라도 항상 같은 내용을 표시해주는 경우라면 const로 정의하여 사용했을 때, 재할당을 하지 않게 되어 내부 리소스 낭비를 방지할 수 있다.
- 기본 자료형 ( int, double, String )
    - int, double의 경우 num이라는 상위 객체가 존재한다.
- Map
    - 선언시 중괄호를 통해서 선언할 수 있다.
        
        ```dart
        Map<String, dynamic> myMap = {'name': 'gyl'};
        ```
        
    - 삽입 및 변경
        
        ```dart
        myMap['age'] = 12
        ```
        
    - 삭제
        
        ```dart
        // 특정한 key-value를 삭제할 경우
        myMap.remove('age');
        // 모든 key-value를 삭제할 경우
        myMap.clear();
        ```
        
    - 특정한 키 혹은 값을 가지고 있는지 확인하기
        
        ```dart
        myMap.containsKey(찾고자하는 키);
        myMap.containsValue(찾고자하는 밸류);
        ```
        
    - 이외의 속성들
        
        ```dart
        myMap.keys // 키들을 iterable 객체로 반환
        myMap.values // 값들을 iterable 객체로 반환
        myMap.entries // 키-값 쌍(엔트리)들을 iterable 객체로 반환
        myMap.isEmpty
        myMap.isNotEmpty
        myMap.length
        ```
        
- Set
    - 선언
        
        ```dart
        Set<String> mySet = ['우갸갸갸'];
        var mySet = Set();
        ```
        
    - 추가 및 제거
        
        ```dart
        mySet.add('우갸갸');
        mySet.remove('우갸갸');
        ```
        
    - 값이 있는지 확인하기
        
        ```dart
        mySet.contains('우갸갸'); // true
        ```
        
- List
    - 추가시에 add를 사용한다.
    - 제거시 범위를 설정할 수 있다.
        
        ```dart
        List<String> myList = ['ㄱ', 'ㄴ', 'ㄷ', 'ㄹ'];
        myList.removeRange(a,b);
        // a부터 b이전까지의 범위의 값을 제거할 수 있다.
        // 모두 제거를 원한다면 clear를 사용하면된다.
        ```
        
    - 변형
        
        ```dart
        myList.toSet(); // Set<E> 로 타입이 변경된다.
        myList.toString(); // String 타입으로 변경된다.
        myList.shuffle(); // 무작위로 리스트 원소를 섞는다.
        myList.replaceRange(start, end, [replacements]); 
        // 시작 지점부터 끝 지점까지 대체값들로 변경
        myList.join(separator); 
        // separator를 각 원소사이에 넣어 문자열로 반환하는 함수로 파이썬과 동일
        ```
        
- Records
    - 대부분의 언어에서는 tuple이라고 불리며 점점 사용성이 없어져 사라지는 추세지만 희안하게도 dart에서는 최근에 생겼다고 한다.
        - 간단한 예제
        
            ```dart
            (String, int) myRecord = ('gyl', 21);
            print(myRecord); // ('gyl', 21)
            print(myRecord.$1); // 'gyl'
            print(myRecord.$2) // 21
            
            // named parameter를 사용하여 변수로 사용할 수 도 있다.
            (String name, int age) myRecord = ('gyl', 21);
            ```
        
        - 할당
            
            ```dart
            (String, int) userInfo(Map json) => (json['name'] as String, json['age'] as int);
            ```
            
        - 사용
            
            ```dart
            var info = userInfo(json);
            var name = info.$1;
            var age = info.$2;
            ```
            
- Pattern Matching
    
    ```dart
    var (name, age) = userInfo(json);
    ```