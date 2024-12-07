# Platform_channel
* Flutter와 네이티브(Android, iOS) 간의 통신을 위한 메커니즘을 말한다. 채널을 통해 데이터를 주고 받으며 네이티브 기능을 Flutter에서 활용할 수 있게 된다.
* 카메라, GPS, 알림 등과 같은 것들이 네이티브 API로 구현하여야 한다.

### 주요 채널
  * MethodChannel
    * 네이티브로 메서드를 호출하고, 단일 값을 반환하는 방식으로 주로 비동기 작업에 사용된다.
  * BasicMessageChannel
    * 바이너리 메시지나 문자열 데이터를 주고받기 위한 채널로, 양방향 통신에 사용된다.
  * EventChannel
    * 연속적인 데이터 스트림을 전달하는 데 사용된다. 예를 들어 센서 데이터나 위치 정보 등의 실시간 이벤트를 처리할 때 유용하다.

### 예시
  * MethodChannel
    * invokeMethod를 통해서 네이티브 메서드를 호출하고, 데이터를 반환
        ```dart
        class PlatformService {
            static const platform = MethodChannel('com.example/platform')

            Future<String> getNativeData() async {
                try {
                    final String result = await platform.invokeMethod('getNativeData');
                    return result;
                } catch(e) {
                    return 'Failed to get native data: $e';
                }
            }
        }
        ```
    * Android에서 네이티브 코드 구현
        ```kotlin
        class MainActivity: FlutterActivity() {
            override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
                MethodChannel(flutterEngine.dartExecutor.binrayMessenger, CHANNEL).setMethodCallHandler { call, result ->
                    if (call.method == "getNativeData") {
                        result.success("Hello from Android Nativie!")
                    } else {
                        result.notImplemented()
                    }
                }
            }
        }
        ```
        * getNativedata 메서드가 호출되면 Android에서 데이터를 처리하여 Flutter로 반환한다.
    * iOS에서 네이티브 코드 구현
        ```swift
        @UIApplicationMain
        @objc class AppDelegate: FlutterAppDelegate {
            func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
                let controller = window?.rootViewController as! FlutterViewController
                let platformChannel = FlutterMethodChannel(name: "com.example/platform", binaryMessenger: controller.binaryMessenger)

                platformChannel.setMethodCallHanler { (call: FlutterMethodCall, result: @escaping FlutterResult) in 
                    if call.method == "getNativeData" {
                        result("Hello from iOS Native!")
                    } else {
                        result(FlutterMethodNotImplemented)
                    }
                }
                return super.applitcation(application, didFinishLaunchingWithOptions: launchOptions)
            }
        }
  * EventChannel
    * 네이티브에서 발생하는 이벤트 ( 센서 데이터 등)를 Flutter로 스트림으로 전달하는 방법
    * 센서 데이터나 실시간 위치 정보와 같은 연속적인 데이터를 플러터로 전달하는데 사용된다.
        ```dart
        class SensorService {
            static const EventChannel _eventChannel = EventChannel("com.example/sensor");

            Stream<dynamic> getSensorStream() {
                return _eventChannel.receiveBroadcastStream();
            }
        }
        ```
    * Android에서 EventChannel 처리
        ```kotlin
        public class MainActivity extends FlutterActivity {
            static final String SENSOR_CHANNEL = "com.example/sensor";
            private SensorService sensorService;
            private SensorEventListener sensorListener;

            @override
            public void configureFlutterEngine(@NonNull FlutterEngine flutterEngine) {
                new EventChannel(flutterEngine.getDartExcutor().getBinaryMessenger(), SENSOR_CHANNEL).setStreamHandler(new EventChannel.StreamHandler() {
                    @override
                    public void onListen(Object arguments, EventChannel.EventSink events) {
                        startSensorUpdates(events);
                    }

                    @override
                    public void onCancel(Object arguments) {
                        stopSensorUpdates();
                    }
                });
            }

            private void startSensorUpdates(EventChannel.EventSink events) {
                sensorService = (SensorService) getSystemService(SENSOR_SERVICE);
                Sensor accelerometer = sensorService.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
                sensorEventListener = new SensorEventListener() {
                    @override
                    public void onSensorChanged(SensorEvent event) {
                        events.success(event.values[0]);
                    }

                    @override
                    public void onAccuracyChanged(Sensor sensor, int accuracy) {

                    }
                };
                sensorService.registerListener(sensorEventListener, accelerometer, SensorService.SENSOR_DELAY_NOMAL);
            }

            privaste void stopSensorUpdates() {
                sensorService.unregisterListener(sensorEventListener);
            }
        }
        ```
    * iOS에서 EventChannel 처리(배터리 혹은 센서 데이터를 전달하는 방법)
        ```swift
        @UIApplicationMain
        @objc class AppDelegate: FlutterAppDelegate {
            override func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
                let controller = window?.rootViewController as! FlutterViewController
                let sensorChannel = FlutterEventChannel(name: "com.example/sensor", binaryMessenger: controller.binaryMessenger)

                sensorChannel.setStreamHandler(SensorStreamHandler())
                return super.application(application, didFinishLaunchingWithOptions: launchOptions)
            }
        }

        class SensorStreamHandler: NSObject, FLutterStreamHandler {
            func onListen(withArguments arguments: Any?, eventSink events: @escaping FlutterEventSink) -> FlutterError? {
                UIDevice.current.isBatteryMonioringEnabled = true
                events(UIDevice.current.batteryLevel * 100)
                return nil
            }

            func onCancel(withArguments arguments: Any?) -> FlutterError? {
                return nil
            }
        }
        ```

### 성능저하 방지
* 비동기 처리로 UI 블로킹 방지하기
* 불필요한 통신 최소화 및 대량의 데이터는 이벤트 스트림 사용하기