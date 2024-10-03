# Socket
* WebSocket
  * web_socket_channel 패키지를 사용하여 구현할 수 있다.
  * 연결
    ```dart
    final channel = WebSocketChannel.connect (
        Uri.parse('wss://...'),
    );
    ```
    * connect 메서드를 통해서 웹소켓 서버와 연결하는 WebsSocket Channel을 생성
  * 수신
    * StreamBuilder 위젯을 사용해 수신한 메세지를 화면에 표시할 수 있다. 즉, 이벤트를 수신할 때마다 다시 빌드하여 화면에 표시한다.
        ```dart
        StreamBuilder(
            stream: channel.stream,
            builder: (context, snapshot) {
                return Text(snapshot.hasData ? "${snapshot.data}" : '');
            },
        )
        ```
  * 송신
    * 서버로 데이터를 보내려면 sink에 add 메서드로 추가해 보낼 수 있다.
        ```dart
        channel.sink.add('Hello!');
        ```
  * 종료
    ```dart
    channel.sink.close();
    ```
  
* Socket.io
  * socket_io_client 패키지를 사용해 구현할 수 있다.
  * 연결
    ```dart
    import 'package:socket_io_client/socket_io_client.dart' as IO;
    socket = IO.io('wss://...', <String dynamic> { 
        'transports': ['websocket'],
        'autoConnect': false,
    });
    socket.connect();
    ```
    * 연결에 필요한 필드들을 작성하고, connect 메서드를 통해서 연결
  * 수신
    * on 메서드를 활용하여 이벤트 리스너를 설정해 수신한 데이터를 가지고 이벤트를 처리할 수 있다.
    ```dart
    socket.on('connect', (_) {
        print("Connected");
    });

    socket.on('message', (msg) {
        setState((){
            messages.add(msg);
        });
    });
    ```
  * 송신
    * emit 메서드를 활용해 이벤트를 전송할 수 있다.
        ```dart
        sendMessage() {
            if (textController.text.isNotEmpty) {
                socket.emit('message', textController.text);
                textController.clear();
            }
        }
        ```
  * 종료
    * dispose 메서드를 통해 연결 종료
        ```dart
        @override
        void dispose() {
            socket.dispose();
            super.dispose();
        }
        ``` 