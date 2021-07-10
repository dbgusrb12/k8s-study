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


`kubectl` 을 이용하여 간단한 에코 서버를 동작 시켜보겠다.

우선, echoserver 라는 이름의 파드를 생성한다.

```zsh
~ > kubectl run echoserver --image="k8s.gcr.io/echoserver:1.10" --port=8080
pod/echoserver created
```

그 이후, 쿠버네티스의 파드들의 접근할 때 필요한 echoserver 라는 이름의 서비스를 생성한다.

```zsh
~ > kubectl expose po echoserver --type=NodePort
service/echoserver exposed
```

파드가 정상적으로 생성되었는지 확인을 한다.

`kubectl get` 은 쿠버네티스 내부의 자원 상태를 확인할 때 많이 쓰이는 명령으로,   
`get` 뒤에 확인하려는 자원 이름과 옵션을 설정할 수 있다.

파드를 확인하려면 `kubectl get pods` 를 입력하면 된다.

```zsh
~ > kubectl get pods
NAME         READY   STATUS    RESTARTS   AGE
echoserver   1/1     Running   1          25h
```

실행 결과에 대한 항목은 다음과 같다.

- NAME : 파드의 이름을 표시한다.
- READY : 숫자/숫자 형태로 되어있으며, 파드의 준비 상태를 표시한다.
- STATUS : 파드의 현재 상태를 나타낸다. `Running` 은 현재 실행중이라는 뜻이고,   
  그 외의 상태는 `Terminating`, `ContainerCreating` 등이 있다.
- RESTARTS : 해당 파드가 몇 번 재시작했는지 표시한다.
- AGE : 파드 생성 후 얼마나 시간이 지났는지 나타낸다.

그 이후 서비스가 정상적으로 생성되었는지 확인을 한다.

서비스를 확인하려면 `kubectl get services` 를 입력하면 된다.

```zsh
~ > kubectl get services
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
echoserver   NodePort    10.105.121.59   <none>        8080:30038/TCP   15m
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP          25h
```

실행 결과에 대한 항목은 다음과 같다.

- NAME : 서비스의 이름을 표시한다.
- TYPE : 서비스 타입을 의미한다.
- CLUSTER-IP : 현재 클러스터 안에서 사용되는 IP를 나타낸다.
- EXTERNAL-IP : 클러스터 외부에서 접속할 때 사용하는 IP를 나타낸다.
- PORT(S) : 해당 서비스의 접속하는 포트를 표시한다.
- AGE : 서비스 생성 후 얼마나 시간이 지났는지 나타낸다.


그 이후, 에커 서버에 접근 할 수 있도록 로컬 컴퓨터로 포트포워딩을 한다.

```zsh
~ > kubectl port-forward svc/echoserver 8080:8080
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080

```

이렇게 실행을 하고 `curl http://localhost:8080` 명령을 실행하면,

```zsh
~ > curl http://localhost:8080


Hostname: echoserver

Pod Information:
	-no pod information available-

Server values:
	server_version=nginx: 1.13.3 - lua: 10008

Request Information:
	client_address=127.0.0.1
	method=GET
	real path=/
	query=
	request_version=1.1
	request_scheme=http
	request_uri=http://localhost:8080/

Request Headers:
	accept=*/*
	host=localhost:8080
	user-agent=curl/7.64.1

Request Body:
	-no body in request-
```

이런 식으로 잘 실행 되는 것을 볼 수 있다.

해당 서버의 로그를 수집 할 때는 `kubectl logs -f [파드이름]` 명령으로   
로그를 볼 수 있다.

```zsh
~ > kubectl logs -f echoserver
Generating self-signed cert
Generating a 2048 bit RSA private key
........+++
..........................................+++
writing new private key to '/certs/privateKey.key'
-----
Starting nginx
127.0.0.1 - - [09/Jul/2021:03:04:05 +0000] "GET / HTTP/1.1" 200 1104 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36"
127.0.0.1 - - [09/Jul/2021:03:04:05 +0000] "GET /favicon.ico HTTP/1.1" 200 1042 "http://localhost:8080/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.114 Safari/537.36"
127.0.0.1 - - [09/Jul/2021:03:05:07 +0000] "GET / HTTP/1.1" 200 416 "-" "curl/7.64.1"
```

로그를 보면 시간, HTTP 버전, 웹 브라우저와 운영체제 버전 등의 정보를 확인 할 수 있다.

파드와 서비스를 삭제 하기 위해서는 우선 로그 수집과 서버 실행을 중지하고,   
`kubectl delete pod [파드이름]`, `kubectl deleteservice [서비스이름]`   
으로 삭제 할 수 있다.

```zsh
~ > kubectl delete pod echoserver
pod "echoserver" deleted
~ > kubectl delete service echoserver
service "echoserver" deleted
```

삭제 한 후 `kubectl get` 명령을 통해 정상적으로 삭제되었는지 확인 한다.

```zsh
~ > kubectl get pods
No resources found in default namespace.
~ > kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   26h
```

이렇게 나온다면 정상적으로 삭제 된 것이다.