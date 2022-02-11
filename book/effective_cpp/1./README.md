## 1.C++를 언어들의 연합체로 바라보는 안목은 필수
  - 객체 지향, 템플릿, STL, C언어를 계승

## 2.#define을 쓰려거든 const, enum, inline을 떠올리자
  - #define은 해당 기호식이름을 전부 바꿔버림. 기호 테이블에 기호식 이름이 들어가지 않아. 에러 찾기 어려움.
  - 상수 사용시 컴파일러 에러메세지에 나와 디버깅이 편함. 또한 코드 크기도 절약
  - 클래스 내부 상수는 static으로 사용하자 (선언: hpp, 정의:cpp), 클래스 상수를 #define 사용시 캡슐화도 불가능.
  - 오래된 컴파일러는 (선언: hpp, 정의:cpp)문법이 안될때가 있다. 이럴떈 enum hack 사용
```cpp
class Test1 {
  private:
    enum { ENUM1 = 5 };
    
    int scores[ENUM1];
    ...
};
```

## 3.낌새만 보이면 const를 들이대 보자!
  - const 의미적인 제약 : const 키워드가 붙은 객체는 외부 변경을 불가능하게 한다. 컴파일러가 지켜주기 때문에 편함.
  - * 왼쪽 const는 포인터가 가리키는 대상이 상수, const 가 * 오른쪽에 있으면 포인터가 상수. 기준이 * 이다. (const T * 와 T const * )는 타입이 같음.
  - STL에선  ( T * const, const std::vector<T>::iterator ), (const T * , std::vector<T>::const_iterator) 해당 쌍 끼리 비슷하게 동작.
  - const 사용시 해당 클래스의 인터페이스 파악이 쉬움. 상수 객체 참조자를 사용하여 실행 성능을 높임.
  - mutable: 상수성 제거 키워드.
```cpp
  T* p = test1;               //  비상수 포인터, 비상수 데이터
  const T* p = test2;         //  비상수 포인터, 상수 데이터
  T* const p = test3;         // 상수 포인터, 비상수 데이터
  const T* const p = test4;   // 상수 포인터, 상수 데이터
```
  
  - operator* 재정의시 상수 객체를 반환 안하면 대입이 될 수 있음.
```cpp
  Class1 a, b, c;
  (a * b) = c;
  
  if (a * b = c) { // 비교하려고 했는데 대입됨.
    ...
```
  
  - 상수 멤버, 비상수 멤버 함수가 기능적으로 똑같게 구현되어있으면, 코드 중복을 피하기 위해 비상수 버전이, 상수 버전을 호출하도록 하자.
```cpp
  class TEST {
  public:
    const char& operator[](std::size_t pos) const
    {
      ...
    }
  
    char& operator[](std::size_t pos)
    {
      return const_cast<char&>(stat_cast<const TEST>(*this)[pos]);
    }
 }
```
  
  
## 4.객체를 사용하기 전에 반드시 그 객체를 초기화하자
