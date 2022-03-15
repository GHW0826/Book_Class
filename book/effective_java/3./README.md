
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

 - Cloneable은 복제해도 되는 클래스 명시 인터페이스. but Cloneable 구현으로 clone메서드 호출할 수 없다.
 - 일반적으로 Cloneable을 구현한 클래스는 clone 메서드를public으로 제공. -> 생성자 호출 안해도 객체 생성 가능.
 - Clone 명세
```java
 x.clone() != x
 x.clone().getClass() == x.getClass()
 x.clone().equals(x) // 일반적으로 참, 필수는 아님
```
 - clone은 사실상 생성자와 같은 효과. (원본 객체에 해를 끼치지 않고 동시에 복제된 객체의 불변식 보장)
 - 가변 상태 참조 클래스용 clone은 해당 필드가 final이 아닐때 사용 가능. final 제거해야 할 수 있다.
 - 해시 테이블의 경우 버킷 복제해도 같은 연결 리스트를 참조하여 예기치 못할 가능성이 생김.
 - deepCopy 메서드 재귀 호출 보단 반복자를 써서 순회하는게 나음.
 - 생성자와 마찬가지로 clone에서도 가상함수 호출하면 안됨.
 - Cloneable을 구현한 모든 클래스는 clone을 재정의해야 한다. 접근 재한자는 public, 반환값으 클래스 자신으로 변경.
 - 이후 깊은 복사가 필요한 부분은 clone을 재귀적으로 호출해 주는게 일반적이지만 항상 맞는건 아님.
```java
 @Override public HashTable clone() {
   try {
     HashTable result = (HashTable) super.clone();
     result.buckets = new Entry[buckets.length];
     for (int i = 0; i < buckets.length; ++i)
       if (buckets[i[ != null)
         result.buckets[i] = buckets[i].deepCopy();
     return result;
   } catch (CloneNotSupportedException e) {
     throw new AssertionError();
   }
 }
```
 - clone보다 복사 생성자, 복사 팩터리 패턴이 낫다.

## 14. Comparable을 구현할지 고려하라

 - compareTo는 Object의 메서드가 아님. 동치성 비교, 순서 비교 가능, 제네릭함.
 - 구현시 컬렉션 관리를 편하게 할 수 있다.
 - compareTo의 일반 규약
```
 객체와 주어진 객체의 순서를 비교.
 작으면 음수, 같으면 0, 크면 양수 반환. 비교 불가능 객체시  ClassCastException을 던짐.
 sgn(부호 함수) : 음수, 0, 양수 -> -1, 0, 1 를 반환.
 sgn(x.compareTo(y)) == -sgn(y.compareTo(x))
 
 추이성을 보장해야 한다. 
 x.compareTo(y) > 0 && y.compareTo(z) > 0 -> x.compareTo(z) > 0
 x.compareTo(y) == 0 -> sgn(x.compareTo(z)) == sgn(y.compareTo(z))
 
 꼭은 아닌데 지키자.
 compareTo로 수행한 동치성 결과가 equals의 결과와 같아야 한다.
 (x.compareTo(y) == 0) == (x.equals(y))
```
 - 타입이 다른 객체는 그냥 예외를 던지면 된다. but 다른 타입 비교도 허용 가능.
 - equals <=> hashCode 관계와 마찬가지로 compareTo 잘못 구현시 TreeSet, TreeMap, 검색, 정렬 알고리즘이 작동 잘 안할 수 있다.
 - compareTo는 제네릭 인터페이스이므로 컴파일타임에 인수 탕비이 정해짐.
 - Comparable을 구현하지 않은 필드나 표준이 아닌 순서로 비교 해야 한다면 비교자를 대신 사용한다.
 - equals, clone과 비슷하게 재귀적인 방식으로 구현.
 - compareTo에서 부등호 쓰지말고, 박싱된 기본 타입 클래스가 제공하는 정적 compare 메서드나 Comparator가 제공하는 비교자 생성 메서드를 사용하자.
  
