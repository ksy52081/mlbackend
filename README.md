# mlbackend
kafka-spring-argoflow-triton-pytorch MLplatform
# Manifest docs for local test

## Docker install ubuntu OS

- https://docs.docker.com/engine/install/ubuntu/
- https://minikube.sigs.k8s.io/docs/drivers/docker/
  - minikube와 호환되는 docker 버전으로 설치 필요함

## Minikube setting

### minikube install

```
# install kubectl
$ curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.19.14/bin/linux/amd64/kubectl
$ chmod +x ./kubectl
$ sudo mv ./kubectl /usr/local/bin/kubectl

# disable kubectl auto update
$ sudo apt-mark hold kubectl

# install minikube
$ curl -LO https://storage.googleapis.com/minikube/releases/v1.24.0/minikube-linux-amd64
$ sudo install minikube-linux-amd64 /usr/local/bin/minikube
$ sudo apt-get install virtualbox

# insecure registry for team harbor and QA registry
$ minikube start \
--cpus=8 --memory=8192 --disk-size=100g \
--driver=virtualbox \
--host-only-cidr=192.168.99.1/24 \
--kubernetes-version=v1.19.14 \
--insecure-registry=192.168.122.128:31234 \
```

- minikube version에 따라 subnet 구성이 달라짐 -> v1.24.0 버전  설치 권장
- image-registry 추가 필요
- minikube resource (cpu, memory, disk) setting은 local PC 상황에 맞게 설정
- minikube start 옵션 변경 시 저장된 profile 삭제 후 위 명령어 수정하여 실행

## Base component install for MLPlatform

### Helm install

```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3
$ chmod 700 get_helm.sh
$ ./get_helm.sh

# remove redundant file
$ rm get_helm.sh
```

### Docker insecure registry setting

- Remote Image registry를 사용하기 위해서는, /etc/docker/daemon.json에 insecure-registry로 등록해야함
- 추가 다른 registry 접근 필요시 추가 설정 필요

```
$ vi /etc/docker/daemon.json

{
    "insecure-registries": ["192.168.122.128:31234"]
}

$ service docker restart
$ docker login 192.168.122.128:31234
Username: admin
Password: Harbor12345
WARNING! Your password will be stored unencrypted in /home/mansu/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```

### install all components

```
# 학습 workflow 생성을 위해 argo,
# 배포 작업을 위해 cert-manager, istio, knative-serving, kserve를 설치합니다.
#
$ bash local_install.sh
```
