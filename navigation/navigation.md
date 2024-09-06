# Navigation
* Routing
  * 앱에서 사용자의 요청에 따라 페이지를 전환하거나 표시하는 과정을 말한다. Flutter에서는 라우팅을 통해 앱 내에서 페이지를 이동하고, 각 페이지 간의 전환을 관리한다.
  * 종류
    * Navigator와 MaterialApp
    * CupertinoApp의 routes

* 페이지 전환 패턴
  * Navigator
    * 가장 기본적인 라우팅 방법이다. push와 pop을 통해서 페이지를 전환한다.
    * 주요 메서드
      * push: 새로운 화면을 현재 화면위에 쌓는다.
      * pop: 현재 화면을 제거하고 이전 화면으로 돌아간다.
      * pushReplacement: 현재 화면을 새로운 화면으로 교체한다.
      * pushAndRemoveUntil: 스택에 있는 화면을 모두 제거하고 새로운 화면으로 이동한다.
    * 예시
        ```dart
        MaterialApp(
            title: "Navigator",
            home: HomeScreen(),
            routes: {
                '/home': (context) => HomeScreen(),
                '/detail': (context) => DetailScreen(),
            },
        );
        // HomeScreen
        Navigator.pushNamed(context, '/detail');
        // DetailScreen
        Navigator.pop(context);
        ```
        * Named Routes를 통해서 간단하게 구현할 수 있다. enum을 통해서 보다 간결하고, 가독성있게 작성할 수 도 있다.
  * Named Routes
    * MaterialApp의 routes 속성에 경로와 위젯을 설정하여 사용한다.
  * Router
    * URL 기반의 라우팅 방법이다.
    * RouteInformationParser가 URL을 파싱하여 애플리케이션의 상태를 생성하고, RouterDelegate는 이 상태를 기반으로 네비게이션을 관리하고, Navigator를 통해 화면에 렌더링 한다.
    * 이를 사용하는 대표적인 라이브러리로 go_router가 있다.
      * 라우트 초기화
        ```dart
            void main() => runApp(
                const ProviderScope(
                    child: RiverpodApp()
                ),
            );
            
            // GoRouter를 선언하고 MaterialApp의 생성자를 통해 라우터를 주입하기
            final GoRouter _router = GoRouter(
                routes: <RouteBase>[
                    GoRoute(
                    path: '/',
                    builder: (context, state) => const UserScreen(),
                    routes: <RouteBase>[
                        GoRoute(
                            path: 'details',
                                builder: (context, state) {
                                    final userInfo = state.extra as User;
                                    return UserDetailPage(userInfo: userInfo);
                                },
                            ),
                        ],
                    ),
                ],
            );
            
            class RiverpodApp extends StatelessWidget {
                const RiverpodApp({super.key});
                
                @override
                Widget build(BuildContext context) {
                        // MaterialApp의 생성자를 통해서 config 처리
                return MaterialApp.router(routerConfig: _router,);
                }
            }
                
            class UserScreen extends StatelessWidget {
                const UserScreen({super.key});
            
                @override
                Widget build(BuildContext context) {
                    return const UserListPage();
                }
            }
        ```
      * 라우팅 + 데이터 전달
        ```dart
        class UserListPage extends ConsumerWidget {
            const UserListPage({super.key});
            
            @override
            Widget build(BuildContext context, WidgetRef ref) {
                final userState = ref.watch(userViewModelProvider);
                return Scaffold(
                appBar: AppBar(
                    title: const Text('User List'),
                ),
                body: userState.when(
                    data: (users) => ListView.builder(
                        itemCount: users.length,
                        itemBuilder: (context, index) {
                            final user = users[index];
                            return ListTile(
                            title: Text(user.name),
                            subtitle: Text(user.email),
                            // 데티어를 전달하려면 extra를 통해서 전달하면 된다.
                            onTap: () => context.go('/details', extra: user),
                            );
                        },
                    ),
                    loading: () => const Center(
                        child: CircularProgressIndicator(),
                    ),
                    error: (err, stack) => Center(
                        child: Text('Error: $err'),
                    ),
                ),
                floatingActionButton: FloatingActionButton(
                        onPressed: () => ref.read(userViewModelProvider.notifier).fetchUsers(),
                        child: const Icon(Icons.refresh),
                    ),
                );
            }
        }
        ``` 
  * Tab Navigation
    * 탭 전환 구현에 사용된다.
  * Drawer Navigation
    * 사이드 메뉴를 통해 화면 간 전환에 사용된다.
