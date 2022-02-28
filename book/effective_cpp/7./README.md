
## 41. 템플릿 프로그래밍의 천릿길도 암시적 인터페이스와 컴파일 타임 다형성부터
  
  - 객체지향 기본은 명시적 인터페이스, 런타임 다형성.
  - 템플릿, 일반화 프로그래밍 기본은 암시적 인터페이스, 컴파일 타임 다형성.
  - 암시적 인터페이스는 템플릿 안에서 몇 몇 표현식이 유효해야 한다는 것.
```cpp
  template<typename T>
  void execute(T& obj)
  {
    if (obj.size() > 10 && obj != something()) {    // size 함수 지원, >, != 부등 비교 지원
      T tmp(obj);                                   // 복사생성자 지원
      tmp.normalize();                              // normalize 함수 지원
      tmp.swap(obj);                                // swap 함수 지원
    }
  }
```
  - 위 함수에서 operator>, operator!= 가 호출될떄 해당 호출을 성공시키기 위해 템플릿의 인스턴스화가 일어남 (컴파일 도중) -> 컴파일 타임 다형성
  - 템플릿 매개변수의 경우, 인터페이슨느 암시적, 유효 표현식에 기반을 두어 구성. 다형성은 컴파일 중에 템플릿 인스턴스화와 함수 오버로딩 모호성 해결을 통해 나타난다.

## 42. typename의 두 가지 의미를 제대로 파악하자

  - 템플릿 선언문 내에서 class, typename의 차이점은 없다
```cpp
  template<class T>
  template<typename T>
```
   
    - 템플릿 내의 이름중 템플릿 매개변수에 종속된 것 : 의존 이름. 
    - 의존이름이 중첩되어 있는 경우 : 중첩 의존 이름
    - 템플릿 매개변수와 상관없는 타입 이름 : 비의존 이름
```cpp
  // 그냥 예시
  template<typename T>
  void getSecondObj(const T& container)
  {
    if (container.size() >= 2) {
      T::const_iterator iter(container.begin());    // T::const_iterator는 중첩 의존 이름
      ++iter;
      int value = *iter;              // int 는 비의존 이름
      std::cout << value;       
    }
  }
```
  - 위 예제에서 T에 const_iterator라는 이름을 가진 정적 데이터 멤버가 있을수 있다. but..
  - c++ 구문 분석기는 템플릿 안에서 중첩 의존 이름을 만나면 개발자가 타입이라고 알려주지 않는 한 그 이름이 타입이 아니라고 가정하게 되어있다. (타입으로 지정하는 방법 : 객체앞에 typename 지정)
```cpp
    template<typename T>
  void getSecondObj(const T& container)
  {
    if (container.size() >= 2) {
      typename T::const_iterator iter(container.begin()); 
      ...
    }
  }
```

- 중첩 의존 타입 이름을 식별하는 용도에는 반드시 typename을 사용한다. 중첩 의존 이름이 기본 클래스 리스트에 있거나 멤버 초기화 리스트 내의 기본 클래스 식별자있을 때는 예외.
- 너무 길면  typedef로 줄이기 가능 (ex. typedef typename std::iterator_traits<T>::value_type value_type; )
  
## 43. 템플릿으로 만들어진 기본 클래스 안의 이름에 접근하는 방법을 알아 두자
## 44. 매개변수에 독립적인 코드는 템플릿으로부터 분리시키자
## 45. "호환되는 모든 타입"을 받아들이는 데는 멤버 함수 템플릿이 직방!
## 46. 타입 변환이 바람직할 경우에는 비멤버 함수를 클래스 템플릿 안에 정의해 두자
## 47. 타입에 대한 정보가 필요하다면 특성정보 클래스를 사용하자
## 48. 템플릿 메타프로그래밍, 하지 않겠는가?
