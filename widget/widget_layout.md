# Widget Layout
* Flutter는 다양한 형태의 레이아웃을 지원하며, 각 레이아웃은 Single-child layout widgets, Multi-child layout widgets, Sliver widgets로 나뉜다.

### Single-child layout widgets
* Text, Image, Icon 등의 단일 위젯을 배치하기 위해 Center, Container 등이 사용될 수 있다.
  * 예시 코드 ( Center )
    ```dart
    Center(
        child: Text('안녕'),
    ),

    Center(
        child: Image.asset(
            'images/plus.jpg',
            fit: BoxFit.cover,
        ),
    ),

    // ...
    ```

### Layout multiple widgets vertically and horizontally
#### Multi-child layout widgets
* Row
  * 가로 방향으로 위젯을 배치할 수 있게 만들어 준다.
  * 예시 코드
    ```dart
    Row(
        children: [
            Text('이메일'),
            Text('abcde123@dart.com'),
            Column(
                children: [
                    Text('Column도 Row의 children에 포함할 수 있다.'),
                    // ...
                ]
            )
            // ...
        ]
    )
    ```
* Column
  * 세로 방향으로 위젯을 배치할 수 있게 만들어 준다.
  * 예시 코드
    ```dart
        Column(
            children: [
                Text('이메일'),
                Text('abcde123@dart.com'),
                Row(
                    children: [
                        Text('Row도 Column의 children에 포함할 수 있다.'),
                        // ...
                    ]
                )
                // ...
            ]
        )
    ```
* Aligning widgets

    ![alt text](https://velog.velcdn.com/images/sucream/post/0c162cca-01bf-4e87-a2e9-7acfb03a6fe5/image.png)

  * 위와 같이 Row, Column 등 정렬을 지정할 수 있는 위젯들은 Main, Cross Axis를 지정할 수 있으며 자신의 Children이 배치되는 방향을 Main Axis로 생각하면 된다.
  * 종류
    * Start: Main Axis의 시작 지점을 기준으로 정렬한다.
    * End: Main Axis의 끝 지점을 기준으로 정렬한다.
    * Center: Main Axis의 중앙 지점을 기준으로 정렬한다.
    * SpaceBetween: Children 의 첫 번째 위젯과 마지막 위젯을 양 끝에 배치하고, 나머지 공간에 동일한 간격으로 배분한다. 
    * SpaceAround: 첫 번째 위젯과 마지막 위젯의 끝과의 간격을 children 상이의 공간의 절반으로 배치한다.
    * SpaceEvenly: Children 사이의 공간을 모두 동일하게 배분해 정렬한다.
    * Space 정렬 이미지
        ![alt text](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FUi4pY%2Fbtqz7omPMo2%2FJQLXhUo42USHCnZjCVPkL0%2Fimg.png)

### Sizing widgets
  * Expanded
    * 하위 위젯을 상위에 확장하여 채워주는 위젯으로, Column, Row, Flex 만 상위 위젯이 될 수 있다. 다시 말해, 축이 있는 위젯에서만 사용이 가능하다는 것이다.
    * 상위의 Main Axis 를 기준으로 자식을 늘려 채워주게 되는데, 여러개의 Expanded가 존재할 경우, flex 속성을 통해서 하위 위젯들의 비율을 정할 수 있다.
  * 예시 코드
    ```dart
        Row(
            children: <Widget>[
                Expanded(
                    child: Container(
                        color: Colors.red,
                    ),
                ),
                Expanded(
                    child: Container(
                        color: Colors.amber,
                    ),
                ),
            ],
        ),
    ```

### Packing widgets
* mainAxisSize
  * Row, Column 과 같은 flex 레이아웃 위젯에서 주축 방향의 크기를 결정하는데 사용되는 속성이다.
  * MainAxisSize.min: 하위 위젯들이 차지하는 공간만큼만 상위 위젯이 크기를 결정한다.
    * 가령 Column에서 mainAxisSize를 min으로 설정하면 하위 위젯들이 차지하는 세로 공간만큼만 Column 이 크기를 가지게 된다. 즉, Column 이 화면 전체 높이를 사용하지 않고, 하위 위젯들이 필요한 만큼만 공간을 사용한다.
  * MainAxisSize.max: 주축 방향으로 가능한 모든 공간을 사용한다.
    * 예를 들어 Row에서 mainAxisSize를 max로 설정하면, Row 는 가로 방향으로 가능한 모든 공간을 차지하려고 한다. 즉, 화면의 전체 너비를 사용하게 된다.
* 예시 코드
    ```dart
        Column(
            mainAxisSize: MainAxisSize.min,
            children: <Widget>[
                Container(
                    color: Colors.red,
                    height: 100,
                    width: 100,
                ),
                Container(
                    color: Colors.blue,
                    height: 100,
                    width: 100,
                ),
            ],
        ),
    ```