
kubectl  [[link](https://kubernetes.io/ko/docs/reference/kubectl/overview/)]

### 관리형
 - EKS, AKS, GKE

### 설치형
 - 수세의 Rancher, 레드햇의 OpenShift 등 플랫폼에서 제공

### 구성형
 - kubeadm, kops, KRIB, kubespray

-- 파드 배포 명령시 순서

- 0. kubectl : 쿠버네티스 클러스터에 명령을 내리는 역할. 바로 실행되는 바이너리로 배포됨.
- 1. API 서버 : 쿠버네티스 클러스터의 중심 통로 역할. 주로 상태 값 저장하는 etcd와 통신. 그 밖의 요소들 또한API 서버를 중심에 두고 통신.
- 2. etcd: 구성 요소들의 상태 값이 모두 저장되는 곳.여기만 백업돼 있으면 긴급 장애 상황에서도 쿠버네티스 클러스터 복구 가능. key-value 저장소.
- 3. 컨트롤러 매니저: 쿠버네티스 클러스터의 오브젝트 상태를 관리. 워커 노드 통신이 안되면, 상태 체크, 복구는 컨트롤러 매니저에 속한 노드 컨트롤러에서 이루어짐., 레플리카셋, 컨트롤러 매니저 포함됌.
