# Animation
* 드로잉 기반 애니메이션
  * lottie, riv 와 같은 애니메이션을 의미한다.
  * lottie
    * 에어비앤비에서 개발한 오픈소스 라이브러리로 백터 기반의 애니메이션을 제공
    * JSON 기반의 애니메이션 파일 형식으로 실시간으로 애니메이션을 랜더링 할 수 있다.
* 코드 기반의 애니메이션
  * 속성의 변화에 따라서 애니메이션과 관련된 코드를 작성 및 정의하여 제어하는 방식을 말한다.
  * Flutter에서는 AnimationController, Tween, AnimateBuilder 등의 클래스를 사용하여 구현한다.
  * 종류
    * Implicit Animations ( 암시적 )
        ```dart
        @override
        Widget build(BuildContext context) {
            return Scaffold(
                body: Center(
                    child: Column(
                        children: <Widget> [
                            SizedBox(
                                width: 128,
                                height: 128,
                                child: AnimatedController(
                                    margin: EdgeInsets.all(margin),
                                    decoration: BoxDecoration(
                                        color: color,
                                        borderRadius: BorderRadius.circular(borderRadius),
                                    ),
                                    duration: _duration,
                                ),
                            ),
                            ElevatedButton(
                                child: const Text("GO!"),
                                onPressed: () => _go(),
                            )
                        ]
                    )
                )
            );
        }

        _go() {
            setState(() {
                color = randomColor();
                borderRadius = randomBorderRadius();
                margin = randomMargin();
            });
        }

        ```
      * 변화를 부드럽게 처리하기 위한 위젯
      * 위젯에 움직임을 추가하고, 시각적 효과를 만들 수 있음
      * 애니메이션을 자동으로 관리함
      * 상태와 무관하게 빌드 시점에 애니메이션을 시작한다.
      * AnimatedController 활용하기
        * 대상 값을 설정하여 위젯 속성을 애니메이션화 할 수 있으며, 값이 변경될 때마다 해당 속성을 이전 값에서 새로운 값으로 애니메이션화한다.
        * 애니메이션 위젯의 종류
          * AnimatedContainer - 크기, 색상, 테두리 등 다양한 속성을 애니메이션으로 변경할 수 있는 컨테이너
          * AnimatedOpacity - 위젯의 투명도를 애니메이션으로 변경
          * AnimatedPadding - 패딩을 애니메이션으로 변경
          * AnimatedAlign - 정렬을 애니메이션으로 변경
          * AnimatiedPositioned - 위젯의 위치를 애니메이션으로 변경. 주로 스택 위젯 내부에서 사용한다.
    * Explicit Animations ( 명시적 )
        ```dart

        AnimationController _controller;
        Animation<double> _animation;

        @override
        void initState() {
            super.initState();
            _controller = AnimationController(
                duration: Duration(seconds: 2),
                vsync: this,
            );

            _animation = Tween<double>(begin: 1.0, end: 2.0).animate(
                CurvedAnimation(
                    parent: _controller,
                    curve: Curves.elasticInOut,
                ),
            );

            _controller.addStatusListener((status) {
                if (status == AnimationStatus.completed) {
                    _controller.reverse();
                } else if (status == AnimtaionStatus.dismissed) {
                    _controller.forward();
                }
            });
        }

        @override
        Widget build(BuildContext context) {
            return AnimatedBuilder(
                animation: _animation,
                builder: (context, child) {
                    return Transform.scale(
                        scale: _animation.value,
                        child: Container(
                            width: 100.0,
                            height: 100.0,
                            color: Colors.blue,
                            child: Center(
                                child: ElevatedButton(
                                    onPressed: () {
                                        if(_controller.isAnimating) {
                                            _controller.stop();
                                        } else {
                                            _controller.forward();
                                        }
                                    },
                                    child: Text('Animation'),
                                )
                            )
                        )
                    )
                }
            )
        }
        ```
      * 암시적 애니메이션의 기능을 모두 수행가능하며 상태와 함께 사용되는 경우가 많아 Stateful Widget에서 사용된다.
      * 암시적 애니메이션과는 달리 애니메이션의 시작, 종료, 중간 상태 등을 직접 제어할 수 있다는 차이점이 있다.
        * 애니메이션 위젯의 종류
          * AnimationController - 실행 시간, 속도 등을 제어하는 클래스로, 상태를 관리하고 시작, 중지, 역방향 실행 등을 제어한다.
            * 주요 속성
              * duration - 실행 시간
              * lowerBound - 최소 범위, 기본값은 0.0
              * upperBound - 최대 범위, 기본값은 1.0
              * value - 현재 애니메이션의 값
              * status - 애니메이션의 현재 상태를 나타내는 속성
              * vsync - 애니메이션의 프레임을 동기화하기 위한 TickerProvider
            * 주요 메서드
              * forward - 정방향으로 시작하되 매개변수 from을 사용해 특정한 상태에서 시작할 수 있다.
              * reverse - 반대로 시작
              * repeat - 애니메이션 반복 실행
              * reset - 애니메이션 값을 lowerBound로 재설정
              * stop - 정지
              * dispose - 애니메이션 컨트롤러 정리
          * Animation - 애니메이션의 중간값을 계산
          * Tween - 시작과 끝 값을 정의하는 클래스
          * AnimatedBuilder - 애니메이션 상태에 따라 UI를 빌드하는 위젯으로 애니메이션이 변경될 때마다 화면을 다시 그린다.