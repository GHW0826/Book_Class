
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
 
 - equals 재정의시 hashCode재정이ㅡ 하지 않으면, HashMap, HashSet 컬렉션 원소 사용시 문제가 발생
```
 1. equals 비교에 사용되는 정보가 변경되지 않았다면, 앱 실행동안 그 객체의 hashCode메서드는 몇번을 호출해도 같아야 함.
    앱 재실행시는 이 값이 달라져도 됨.
 2. equals.(Object)가 두 객체가 같다고 판단했다면, 두 객체의 hashCode는 똑같은 값을 반환해야 함.
 3. equals.(Object)가 두 객체가 다르다고 판단해도, 두 객체의 hashCode가 서로 다른 값을 반환할 필요는 없다. 
    but 다른 값을 반환해야 해시테이블의 성능이 좋아짐.
```
 - 좋은 hashCode 작성 요령
```
 1. int 변수 result를 선언한 후 c로 초기화 한다. c는 해당 객체의 첫번쨰 핵심 필드를 (2.a)방식으로 계산한 해시코드.
  (핵심 필드는 equals 비교에 사용되는 필드)
 2. 해당 객체의ㅣ 나머지 핵심 필드 f 각각에 대해 다음 작업을 수행.
  2.a 해당 필드의 해시코드 c를 계산.
   1. 기본 타입 필드라면, Type.hashCode(f)를 수행. 여기서 Type은 해당 기본 타입의 박싱 클래스
   2. 참조 타입 필드면서 이 클래스의 equals 메서드가 이 필드의 equals를 재귀적으로 호출해 비교한다면, 이 필드의 hashCode를 재귀적으로 호출.
     더 복잡해질 거 같으면, 필드의 표준형을 만들어 그 표준형의 hashCode를 호출한다. null이면 0사용.
   3. 필드가 배열이라면, 핵심 원소 각각을 별도 필드처럼 다룬다. 이 규칙을 재귀적으로 적용해 각 핵심 원소의 해시코드 계산후 2.b방식으로 갱신.
    배열에 핵심 원소가 하나도 없으면 단순 상수 사용(0 추천). 모든 원소가 핵심 원소라면 Arrays.hashCode사용.
  
  2.b 단계 2.a에서 계산한 해시코드 c로 result갱신.
   result = 31 * result + c;
 3. result 반환.
```
```java
 // 해시 비용이 크다면, 해시값 캐시해서 사용.
 @Override public int hashCode() {
  int result = hashCode;
  if (result == 0) {
   result = Short.hashCode(short_1);
   result = 31 * result + Short.hashCode(short_2);
   result = 31 * result + Short.hashCode(short_3);
   hashCode = result;
  }
  return result;
 }
```
 
## 12. toString을 항상 재정의하라

 - 기본적으로 toString 메서드는 적합한 문자열 반환하지 않음. (클래스_이름@16진수로 표시한 해시코드)
 - 잘 구현해 놓으면 print, 문자열 연결, assert 구문에서 디버깅 편함.
 - 포맷을 명시하든가 의도를 명확히 밝혀야 한다.
```java
 /*
   해당 문자열이 반환하는 양식, 포맷등을 설명.
 */
 @Override public String toString() {
  return String.format(...);
 }
```

## 13. clone 재정의는 주의해서 진행하라
## 14. Comparable을 구현할지 고려하라

 
  