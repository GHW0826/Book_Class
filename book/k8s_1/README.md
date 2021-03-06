# [컨테이너 인프라 환경 구축을 위한 쿠버네티스/도커](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&barcode=9791165215743)

- virtual box   [[다운로드](https://www.virtualbox.org/wiki/Downloads)]
- vagrant       [[Docs](https://www.vagrantup.com/docs)] [[Cloud](https://app.vagrantup.com/boxes/search)] [[다운로드](https://www.vagrantup.com/downloads)]
- k8s           [[Docs](https://kubernetes.io/ko/docs/home/)]
- ruby          [[Docs](https://ruby-doc.org)]
- putty         [[다운로드](https://www.chiark.greenend.org.uk/~sgtatham/putty/)]
- superputty    [[다운로드](https://github.com/jimradford/superputty/releases)]


### [목차]
## 1장 새로운 인프라 환경이 온다
1. 컨테이너 인프라 환경이란
2. 컨테이너 인프라 환경을 지원하는 도구
3. 새로운 인프라 환경의 시작

## 2장 테스트 환경 구성하기
1. 테스트 환경을 자동으로 구성하는 도구
2. 베이그런트로 테스트 환경 구축하기
3. 터미널 프로그램으로 가상 머신 접속하기

## [3장 컨테이너를 다루는 표준 아키텍처, 쿠버네티스](./3.)
1. 쿠버네티스 이해하기
2. 쿠버네티스 기본 사용법 배우기
3. 쿠버네티스 연결을 담당하는 서비스
4. 알아두면 쓸모 있는 쿠버네티스 오브젝트

## [4장 쿠버네티스를 이루는 컨테이너 도우미, 도커](./4.)
1. 도커를 알아야 하는 이유
2. 도커로 컨테이너 다루기
3. 4가지 방법으로 컨테이너 이미지 만들기
4. 쿠버네티스에서 직접 만든 컨테이너 사용하기

## [5장 지속적 통합과 배포 자동화, 젠킨스](./5.)
1. 컨테이너 인프라 환경에서 CI/CD
2. 젠킨스 설치를 위한 간편화 도구 살펴보기
3. 젠킨스 설치 및 설정하기
4. 젠킨스로 CI/CD 구현하기
5. 젠킨스 플러그인을 통해 구현되는 GitOps

## 6장 안정적인 운영을 완성하는 모니터링, 프로메테우스와 그라파나
1. 컨테이너 인프라 환경 모니터링하기
2. 프로메테우스로 모니터링 데이터 수집과 통합하기
3. PromQL로 메트릭 데이터 추출하기
4. 그라파나로 모니터링 데이터 시각화하기
5. 좀 더 견고한 모니터링 환경 만들기

## 부록
1. kubectl을 더 쉽게 사용하기
2. 쿠버 대시보드 구성하기
3. kubespray로 쿠버네티스 자동 구성하기
4. 컨테이너 깊게 들여다보기
