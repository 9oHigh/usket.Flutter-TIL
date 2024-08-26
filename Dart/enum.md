# Enum
### 개념
* 특정한 값들의 집합을 정의할 때 사용한다.
* swift에서는 CaseIterable을 채택해야 순회가 가능하지만 dart에서는 각 enum 값은 고유의 인덱스를 가지고 있고, 0부터 시작해 리스트로 반환된 값(Enum.values)을 기본적으로 사용할 수 있다.

### 사용 방법
* case 키워드를 작성하지 않아도 되고, 값을 가져올 때는 온점을 사용한다.
    ```dart
    enum Fruit {
        apple,
        banana,
        cherry
    }

    Fruit fruit = Fruit.apple;
    ```
* 순회
  ```dart
  for (Fruit fruit in Fruit.values) {
    print(fruit);
  }

  // Fruit.apple
  // Fruit.banana
  // Fruit.cherry
  ```
* 확장 메서드
  * Swift의 Extension과 동일한 기능으로 기존 클래스에 새로운 메서드나 속성을 추가할 수 있게 해준다. 이를 Enum에 사용하면 Enum에서 기본적으로 제공하지 않는 기능을 확장할 수 있고, 객체처럼 취급할 수 있게 된다.
  * 예시 코드
    ```dart
    extension FruitDescription on Fruit {
        String get description {
            switch (this) {
                case Fruit.apple:
                    return "apple is delicious.";
                // ... 이하 생략
            }
        }
    }

    print(Fruit.apple.description);
    // apple is delicious.
    ```

### Dart v2.15
* 기존에 Enum에서는 rawValue를 통해 생성하는 것이 불가능했으나 2.15 버전이 되면서 byName 메서드와 name 프로퍼티를 추가해 생성이나 접근할 수 있게 수정되었다.
  * 예시 코드
    ```dart
        Fruit fruit = Fruit.values.byName('apple');
        print(fruit.name); // apple
    ```

### Dart v2.17
* Enum에 연관값이 도입이 되었다. 기존에는 단순 비교하는 수준에 머물러 있었다면 이제는 Enum 타입을 클래스처럼 사용할 수 있게 되었다.
  * 예시 코드
    ```dart
        enum Role {
            read,
            write,
            delete
        }

        enum UserRole {
            // 프로퍼티 및 연관값 정의
            admin(
                privileges: [Role.read, Role.write, Role.delete],
            ),
            editor(
                privileges: [Role.read, Role.write],
            ),
            viewer(
                privileges: [Role.read],
            );
            
            final List<Role> privileges;
            // 생성자 추가
            const UserRole({required this.privileges});
        }

        class User {
            final String name;
            final UserRole role;

            User({required this.name, required this.role});

            void printRoles() {
                String privilegesStr = role.privileges.map((role) => role.name).join(', ');
                print('My privileges are: $privilegesStr');
            }
        }

        User adminUser = User(name: 'Kim', role: UserRole.admin);
        User editorUser = User(name: 'Lee', role: UserRole.editor);
        User viewerUser = User(name: 'Park', role: UserRole.viewer);
        adminUser.printRoles(); 
        // My privileges are: read, write, delete
        editorUser.printRoles();
        // My privileges are: read, write
        viewerUser.printRoles();
        // My privileges are: read
    ```
