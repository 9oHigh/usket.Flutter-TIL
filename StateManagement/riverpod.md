# Riverpod
* 단순하고 효율적인 상태 관리를 제공한다.
* UI와 앱의 상태를 중앙 집중식으로 관리하기 좋음. 즉, 전역 상태 관리에 좋음.
* 특징
  * 안정성: Dart의 강력한 타입 시스템을 활용해 컴파일 시점에 오류를 방지하고, 의존성 관리와 상태의 생명주기를 자동으로 관리한다.
  * 독립성: Flutter 프레임워크에 독립적이어서, Dart 콘솔 애플리케이션에서도 사용할 수 있다.
  * 간편한 디버깅: 개발 도중에 상태를 쉽게 추적하고 디버깅할 수 있다.
* Provider와 Riverpod의 차이점
  * Provider
    * 간단한 상태 관리와 의존성 주입을 지원하는 패키지로, Flutter의 기본적인 상태 관리 방법을 제공하지만, 복잡한 상태 관리에는 한계가 있다.
    * 상태의 스코프를 직접적으로 제공하지 않으며, 일반적으로 상태의 수명 주기는 이를 이용하는 위젯의 트리에 따라 결정된다.
  * Riverpod
    * Provider의 발전된 형태로, 상태 관리, 비동기 작업, 의존성 주입을 더욱 강력하고 유연하게 지원하며, 더 높은 성능과 안정성을 제공한다.
    * 상태의 스코프를 제공
    * 상태의 수명 주기를 더욱 세밀하게 조절할 수 있으며, 더 복잡한 상태 관리에 유용하게 사용할 수 있다.
* 개념
  * Ref 객체
    * Provider에서 종속성을 관리하고, 상태를 접근하고 갱신할 수 있는 객체
  * Provider
    * 상태를 관리하고, 의존성을 주입하는 기본 요소
  * ConsumerWidget
    * 상태 변화를 감지하여 UI를 빌드하는 위젯
* Provider의 종류
  * Provider: 단순한 값을 제공하는 가장 기본적인 형태이다.
  * StateProvider: 상태를 제공하고 갱신한다.
  * ChangeNotifierProvider: ChangeNotifier를 통해 상태를 제공하고 갱신한다.
  * StateNotifierProvider: 상태 관리 패턴을 구현하기 위한 도구이다.
  * KeepAliveProvider: 위젯이 활성화 상태를 유지하도록 보장해주는 Provider이다.
    * Provider가 구독되지 않더라도 상태가 유지될 수 있게 해준다.
  * AutoDisposeProvider: 더 이상 필요하지 않은 경우 자동으로 상태를 폐기한다.
    * 이를 통해서 메모리 누수를 방지할 수 있어 성능 향상에 도움을 줄 수 있다. 
  * FutureProvider: 비동기 작업을 처리하고 해당 결과를 상태로 관리할 경우, 사용한다.
    * API 호출, 파일 읽기 등 한 번만 실행되며 결과를 기다리는 작업에 적합하다. Future를 사용하여 데이터를 가져오고, 이 데이터를 UI에 사용할 수 있다.
  * StreamProvider: 스트림을 사용하여 상태를 관리할 때 사용한다.
    * 데이터의 연속적인 흐름을 처리하는데 사용하며, 실시간 채팅, 데이터베이스 실시간 업데이트 등을 처리할 때 적합하다.
  * ScopeProvider: 특정 스코프 내에서 상태를 공유하는 데 사용한다.

* 쇼핑 카트 예제
  * Model & Provider
    ```dart
    class Product {
        final String name;
        final double price;

        Product({required this.name, required this.price});
    }

    final List<Product> products = [
        Product(name: '사과', price: 1.2),
        Product(name: '바나나', price: 0.8),
        Product(name: '포도', price: 1.5),
    ];

    // 쇼핑 카트의 상태를 관리하는 프로바이더
    final cartProvider = StateNotifierProvider<CartNotifier, List<Product>>((ref) {
        return CartNotifier();
    });

    // 총 금액을 계산하는 프로바이더
    final totalAmountProvider = Provider<double>((ref) {
        final cart = ref.watch(cartProvider);
        return cart.fold(0, (total, product) => total + product.price);
    });

    // 상태를 관리하는 StateNotifier
    class CartNotifier extends StateNotifier<List<Product>> {
        CartNotifier() : super([]);

        void addProduct(Product product) {
            state = [...state, product];
        }

        void removeProduct(Product product) {
            state = state.where((p) => p != product).toList();
        }

        void clearCart() {
            state = [];
        }
    }
    ```
  * UI
    ```dart
    class ShoppingCartApp extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return ProviderScope(
                child: MaterialApp(
                    home: Scaffold(
                        appBar: AppBar(title: Text('쇼핑 카트')),
                        body: Column(
                            children: [
                                Expanded(child: ProductListView()),
                                TotalAmount(),
                                CartView(),
                            ],
                        ),
                    ),
                ),
            );
        }
    }

    class ProductListView extends StatelessWidget {
        @override
        Widget build(BuildContext context) {
            return Consumer(
                builder: (context, watch, _) {
                    return ListView.builder(
                        itemCount: products.length,
                        itemBuilder: (context, index) {
                            final product = products[index];
                            return ListTile(
                                title: Text(product.name),
                                subtitle: Text('\$${product.price.toStringAsFixed(2)}'),
                                trailing: IconButton(
                                    icon: Icon(Icons.add_shopping_cart),
                                    onPressed: () {
                                        context.read(cartProvider.notifier).addProduct(product);
                                    },
                                ),
                            );
                        },
                    );
                },
            );
        }
    }

    class TotalAmount extends ConsumerWidget {
        @override
        Widget build(BuildContext context, WidgetRef ref) {
            final totalAmount = ref.watch(totalAmountProvider);
            return Padding(
                padding: const EdgeInsets.all(8.0),
                child: Text(
                    'Total: \$${totalAmount.toStringAsFixed(2)}',
                    style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
                ),
            );
        }
    }

    class CartView extends ConsumerWidget {
        @override
        Widget build(BuildContext context, WidgetRef ref) {
            final cart = ref.watch(cartProvider);

            return Expanded(
                child: ListView.builder(
                    itemCount: cart.length,
                    itemBuilder: (context, index) {
                        final product = cart[index];
                        return ListTile(
                                title: Text(product.name),
                                subtitle: Text('\$${product.price.toStringAsFixed(2)}'),
                                trailing: IconButton(
                                icon: Icon(Icons.remove_shopping_cart),
                                onPressed: () {
                                    context.read(cartProvider.notifier).removeProduct(product);
                                },
                            ),
                        );
                    },
                ),
            );
        }
    }
    ```