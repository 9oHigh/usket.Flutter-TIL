# GetX
* 상태관리 라이브러리 중 가장 단순하고, 사용하기 쉽다는 평가를 받고 있다.
* 기능
  * 상태관리 : GetXController와 GetBuilder를 사용하고, 반응형 상태 관리를 위해 Rx 변수와 GetX를 사용한다.
  * 라우팅 : 네비게이션을 간단하게 처리할 수 있도록 도와준다.
  * 종속성 주입: 종속성을 쉽게 주입 및 관리할 수 있게 도와준다.
* GetX Controller
  * 상태 관리, 비동기 연산 등과 같은 로직을 관리한다. 이를 상속받아서 비즈니스 로직을 작성하고, 상태를 관리하는 방법을 제공한다.
  * 상태관리
    ```dart
    class CounterController extends GetXController {
        var count = 0.obs;

        void increment() => count++;
        // ...
    }
    ```
    * 상태는 변수로 선언되며, Rx 타입으로 지정되어야 자동 업데이트 기능이 동작한다. 
    * obs는 변수를 반응형 상태로 만들기 위해서 사용한다.
  * 상태 업데이트
    ```dart
    Obx(() => Text("${controller.count}")),

    GetBuilder<CounterController>(builder: (controller) {
        return Text("${controller.count}");
    },)
    ```
    * GetX, Obx, GetBuilder 위젯을 통해 UI에서 Controller의 상태를 자동으로 업데이트
  * 종속성 관리
    ```dart
    Get.put(CounterController());
    ```
    * GetX Controller는 종속성 주입과 관리 기능을 제공하여 앱의 의존성을 쉽게 관리할 수 있다.
    * 종속성은 Get.put()을 통해 주입되며, 이 메서드를 사용하면 특정화면이 시작될 때 자동으로 컨트롤러를 생성하고 사용 종료 시 자동으로 해제된다.
    * 메서드
      * Get.put()
        * 인스턴스를 생성하고, 그 인스턴스를 전역에서 사용할 수 있도록 하는데 사용
      * Get.find()
        * 등록된 객체를 GetX의 의존성 관리 시스템에서 찾아 반환하는 메서드
        * 뷰에서 컨트롤러나 서비스에 접근할 때 사용한다.
        * 싱글톤으로 관리하므로, 애플리케이션 내에서 동일한 인스턴스를 공유
  * 장단점
    * 장점
      * 단순하고 직관적이다. 보일러플레이트 코드가 거의 없다.
      * 상태 변경시 전체 위젯트리가 아닌, 특정 위젯만 리빌드되기 때문에 성능 최적화에 유리
      * 효율적인 종속성 관리가 가능
      * 상태 관리뿐만 아니라 라우팅, 종속성 주입, 유효성 검사 등 다양한 기능을 제공하는 통합 솔루션
    * 단점
      * 과도한 사용 위험
        * 전역 상태 관리 기능을 무분별하게 사용하면 유지보수에 어려움을 겪을 수 있음
* 예제
  * 버튼을 클릭하면 리스트에서 명언을 랜덤으로 가져와 보여주는 예제
    ```dart
    // GetX Controller 정의
    class QuoteController extends GetxController {
        final List<String> quotes = [
            "인생은 우리가 다른 계획을 세우느라 바쁠 때 일어난다.",
            "가장 큰 영광은 결코 넘어지지 않는 것이 아니라, 넘어질 때마다 다시 일어서는 것이다.",
            "시작하려면 말을 멈추고 행동해야 한다.",
            "당신의 시간은 한정되어 있으니, 남의 인생을 살며 낭비하지 마라.",
            "삶이 예측 가능하다면, 더 이상 삶이 아니고 무의미할 것이다.",
        ];

        var currentQuote = ''.obs;

        void getRandomQuote() {
            final randomIndex = Random().nextInt(quotes.length);
            currentQuote.value = quotes[randomIndex];
        }
    }

    class QuoteScreen extends StatelessWidget {
        const QuoteScreen({super.key});

        @override
        Widget build(BuildContext context) {
            // GetX Controller 초기화
            final QuoteController quoteController = Get.put(QuoteController());

            return Scaffold(
                appBar: AppBar(title: const Text("랜덤 명언 생성기")),
                body: Center(
                    child: Column(
                        mainAxisAlignment: MainAxisAlignment.center,
                        children: [
                            // 명언을 실시간으로 업데이트하는 텍스트
                            Obx(() => Text(
                                quoteController.currentQuote.value.isEmpty
                                    ? '버튼을 눌러 명언을 확인해보세요!'
                                    : quoteController.currentQuote.value,
                                textAlign: TextAlign.center,
                                style: const TextStyle(fontSize: 24),
                                )),
                            const SizedBox(height: 20),
                            ElevatedButton(
                                onPressed: () {
                                    quoteController.getRandomQuote();
                                },
                                child: const Text('랜덤 명언 생성'),
                            ),
                        ],
                    ),
                ),
            );
        }
    }

    ```