
# [Effective C++](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791195444946)

### [목차]

## [Chapter 1 C++에 왔으면 C++의 법을 따릅시다](./1.)
  1. C++를 언어들의 연합체로 바라보는 안목은 필수
  2. #define을 쓰려거든 const, enum, inline을 떠올리자
  3. 낌새만 보이면 const를 들이대 보자!
  4. 객체를 사용하기 전에 반드시 그 객체를 초기화하자

## [Chapter 2 생성자, 소멸자 및 대입 연산자](./2.)
  5. C++가 은근슬쩍 만들어 호출해 버리는 함수들에 촉각을 세우자
  6. 컴파일러가 만들어낸 함수가 필요 없으면 확실히 이들의 사용을 금해 버리자
  7. 다형성을 가진 기본 클래스에서는 소멸자를 반드시 가상 소멸자로 선언하자
  8. 예외가 소멸자를 떠나지 못하도록 붙들어 놓자
  9. 객체 생성 및 소멸 과정 중에는 절대로 가상 함수를 호출하지 말자
  10. 대입 연산자는 * this의 참조자를 반환하게 하자
  11. operator=에서는 자기대입에 대한 처리가 빠지지 않도록 하자
  12. 객체의 모든 부분을 빠짐없이 복사하자

## [Chapter 3 자원 관리](./3.)
  13. 자원 관리에는 객체가 그만!
  14. 자원 관리 클래스의 복사 동작에 대해 진지하게 고찰하자
  15. 자원 관리 클래스에서 관리되는 자원은 외부에서 접근할 수 있도록 하자
  16. new 및 delete를 사용할 때는 형태를 반드시 맞추자
  17. new로 생성한 객체를 스마트 포인터에 저장하는 코드는 별도의 한 문장으로 만들자

## [Chapter 4 설계 및 선언](./4.)
  18. 인터페이스 설계는 제대로 쓰기엔 쉽게, 엉터리로 쓰기엔 어렵게 하자
  19. 클래스 설계는 타입 설계와 똑같이 취급하자
  20. '값에 의한 전달'보다는 '상수객체 참조자에 의한 전달' 방식을 택하는 편이 대개 낫다
  21. 함수에서 객체를 반환해야 할 경우에 참조자를 반환하려고 들지 말자
  22. 데이터 멤버가 선언될 곳은 private 영역임을 명심하자
  23. 멤버 함수보다는 비멤버 비프렌드 함수와 더 가까워지자
  24. 타입 변환이 모든 매개변수에 대해 적용되어야 한다면 비멤버 함수를 선언하자
  25. 예외를 던지지 않는 swap에 대한 지원도 생각해 보자

## [Chapter 5 구현](./5.)
  26. 변수 정의는 늦출 수 있는 데까지 늦추는 근성을 발휘하자
  27. 캐스팅은 절약, 또 절약! 잊지 말자
  28. 내부에서 사용하는 객체에 대한 "핸들"을 반환하는 코드는 되도록 피하자
  29. 예외 안전성이 확보되는 그날 위해 싸우고 또 싸우자!
  30. 인라인 함수는 미주알고주알 따져서 이해해 두자
  31. 파일 사이의 컴파일 의존성을 최대로 줄이자

## [Chapter 6 상속, 그리고 객체 지향 설계](./6.)
  32. public 상속 모형은 반드시 "is-a(...는 ...의 일종이다)"를 따르도록 만들자
  33. 상속된 이름을 숨기는 일은 피하자
  34. 인터페이스 상속과 구현 상속의 차이를 제대로 파악하고 구별하자
  35. 가상 함수 대신 쓸 것들도 생각해 두는 자세를 시시때때로 길러 두자
  36. 상속받은 비가상 함수를 파생 클래스에서 재정의하는 것은 절대 금물!
  37. 어떤 함수에 대해서도 상속받은 기본 매개변수 값은 절대로 재정의하지 말자
  38. "has-a(...는 ...를 가짐)" 혹은 "is-implemented-in-terms-of(...는 ...를 써서 구현됨)"를 모형화할 때는 객체 합성을 사용하자
  39. private 상속은 심사숙고해서 구사하자
  40. 다중 상속은 심사숙고해서 사용하자

## [Chapter 7 템플릿과 일반화 프로그래밍](./7.)
  41. 템플릿 프로그래밍의 천릿길도 암시적 인터페이스와 컴파일 타임 다형성부터
  42. typename의 두 가지 의미를 제대로 파악하자
  43. 템플릿으로 만들어진 기본 클래스 안의 이름에 접근하는 방법을 알아 두자
  44. 매개변수에 독립적인 코드는 템플릿으로부터 분리시키자
  45. "호환되는 모든 타입"을 받아들이는 데는 멤버 함수 템플릿이 직방!
  46. 타입 변환이 바람직할 경우에는 비멤버 함수를 클래스 템플릿 안에 정의해 두자
  47. 타입에 대한 정보가 필요하다면 특성정보 클래스를 사용하자
  48. 템플릿 메타프로그래밍, 하지 않겠는가?

## [Chapter 8 new와 delete를 내 맘대로](./8.)
  49. new 처리자의 동작 원리를 제대로 이해하자  
  50. new 및 delete를 언제 바꿔야 좋은 소리를 들을지를 파악해 두자
  51. new 및 delete를 작성할 때 따라야 할 기존의 관례를 잘 알아 두자
  52. 위치지정 new를 작성한다면 위치지정 delete도 같이 준비하자

## [Chapter 9 그 밖의 이야기들](./9.)
  53. 컴파일러 경고를 지나치지 말자
  54. TR1을 포함한 표준 라이브러리 구성요소와 편안한 친구가 되자
  55. Boo子有親! 부스트를 늘 여러분 가까이에 
 
  - 부록 A 'Effective C++'이후의 이야기들 
  - 부록 B 2판 및 3판의 항목 대응표
