쿠버네티스에서 컨테이너 실행하기
===

# `kubectl`

`kubectl` 은 쿠버네티스를 관리하는 기본 커맨드라인 인터페이스 (CLI) 이다.

`kubectl` 에서 지원하는 명령은 다음처럼 구분 할 수 있다.

- 쿠버네티스 자원들의 생성, 업데이트, 삭제 (`create`, `update`, `delete`)
- 디버그, 모니터링, 트러블 슈팅 (`log`, `exec`, `cp`, `top`, `attach`, ...)
- 클러스터 관리 (`cordon`, `top`, `drain`, `taint`, ...)

앞으로 나오는 명령어들은 기본적으로 알아야 할 내용만을 소개 할 것이고, 자세한 `kubectl` 의   
사용법은 쿠버네티스 공식문서의 [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet) 를 참고 하면 된다.

## `kubectl` 설치

`kubectl` 은 여러가지 방법으로 설치 할 수 있는데, 기본적으로 도커 데스크탑에서 쿠버네티스를    
설치하면 `kubectl` 도 자동으로 설치된다.

또한, 각 OS 별 패키지 매니저에서 최신 버전의 `kubectl` 을 설치 할 수 있는데, 패키지 매니저로    
설치하면 이전 버전의 `kubectl` 설치가 어렵다는 단점이 있다.

`kubectl` 의 버전과 클러스터의 버전이 다르면 예상치 못한 문제가 발생 할 수 있기 때문에   
클러스터의 버전과 일치하는 것을 설치하는게 좋다.

참고로 주요 클라우드 서비스 (AWS, GCP, AZURE) 에서는 클러스터 외부에 `kubectl` 을 설치하여   
사용하는 방법을 제공한다.

- Amazon EKS: [Installing kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)
- GKE: [셸 선택](https://cloud.google.com/kubernetes-engine/docs/quickstart)
- AKS: [Kubernetes CLI 설치](https://docs.microsoft.com/ko-kr/azure/aks/tutorial-kubernetes-deploy-cluster#install-the-kubernetes-cli)


자세한 내용은 [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl) 문서를 참고 하면 된다.

## `kubectl` 기본 사용법

`kubectl` 의 명령 작성은 다음과 같은 형식으로 이루어져있다.

```
kubectl [command] [TYPE] [NAME] [flags]
```

- command : 자원에 실행하려는 동작을 정의한다. `create`, `get`, `delete` 등이 그 예시이다.
- TYPE : 자원 타입을 정의한다. `pod`, `service`, `ingress` 등이 그 예시이다.
- NAME : 자원 이름을 정의한다.
- flags : 부가적인 옵션들을 정의한다.



