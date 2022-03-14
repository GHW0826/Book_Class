
## 10. equals는 일반 규약을 지켜 재정의하라

 - equals 재정의시 주의할 상황이 있다.
```
 @ 재정의 하지 않을 떄.
 1. 인스턴스가 고유하다.
  동작하는 개체를 표현하는 클래스가 해당 ex) thread.
  
 2. 인스턴스의 동치성을 검사할 일이 없다.
  싱글톤, enum의 경우도 마찬가지 인거 같다.
  
 3. 상위 클래스에서 재정의한 equals가 하위 클래스에도 딱 맞는다.
  대부분 set 구현체는 AbstractSet이 구현한 equals를 상속받아 쓴다.. 이런 형태등이 있다.
 
 4. 클래스가 private이거나, package-private(default)이고 equals 메서드를 호출할 일이 없다.
 
 
 @ 재정의가 필요할 떄
 1. 객체 식별성이 아니라 논리적 동치성을 확인해야하는데, 사우이 클래스의 equals가 논리적 동치성을 비교하도록 안되있을때.
    (주로 값 클래스가 이런다. Integer, String... 이 작업을 해줘야 Map의 키, Set의 원소로 사용 가능한듯.)
```
 - equals 메서드 재정의시 일반 규약을 따라야 한다.
```
 1. 반사성 : null이 아닌 모든 참조 값 x에 대해 x.equals(x)는 true.
 2. 대칭성 : null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)는 true시 y.equals(x)도 true.
 3. 추이성 : null이 아닌 모든 참조 값 x, y, z에 대해 x.equals(y)가 true면 y.equals(z), x.equals(z)는 true.
 4. 일관성 : null이 아닌 모든 참조 값 x, y에 대해 x.equals(y)는 true시 항상 true여야 한다. (false는 false)
 5. null-아님 : null이 아닌 모든 참조 값 x에 대해 x.equals(null)은 false.
```
 - equlas 메서드 구현 단계.
```
 1. ==연산자를 사용해 자기 참조 확인.
 2. instanceof 연산자로 입력이 올바른 타입인지 확인.
 3. 입력을 올바른 타입으로 형변환한다.
 4. 입력 객체와 자기 자신의대응되는 핵심 필드들이 모두 일치하는지 검사한다.
```
 - equlas를 재정의할 떈 hashCode도 재정의 반드시 하자.

## 11. equals를 재정의하려거든 hashCode도 재정의하라
## 12. toString을 항상 재정의하라
## 13. clone 재정의는 주의해서 진행하라
## 14. Comparable을 구현할지 고려하라

 
  
