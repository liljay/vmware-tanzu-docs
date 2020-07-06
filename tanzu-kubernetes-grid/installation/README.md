![VMware Tanzu Logo](https://docs.vmware.com/en/VMware-Tanzu-Kubernetes-Grid/images/GUID-8546DDD9-718A-42F7-9EDB-0BCC3A316BB6-low.png)
# VMware Tanzu Kubernetes Grid on AWS 설치 가이드
## Bootstrap 환경 준비
Bootstrap 환경은 관리 클러스터를 생성 하기 위한 환경이며 Bootstrap 환경 및 Bootstrap 환경 내 아래의 도구들의 설치가 필요합니다.
- Ubuntu 16.04 LTS VM 준비
- Docker 설치 
- kubectl 설치
- Tanzu Kubernetes Grid CLI 설치
- clusterawsadm
- aws cli
- jq


### Ubuntu 16.04 LTS VM 준비
1. Amazon Web Services 웹 콘솔 접속 (https://console.aws.amazon.com) 및 로그인
2. 메인 화면 상단 내 서비스 클릭
3. 컴퓨팅 항목의 EC2 클릭
4. 좌측 인스턴스 메뉴 클릭
5. 인스턴스 시작 클릭
6. AMI 선택 단계 중 Ubuntu Server 16.04 LTS (HVM), SSD Volume Type - ami-078648cce0d33c256 (64비트 x86) / ami-0accc21f9774a85d7 (64비트 Arm) 선택
7. 인스턴스 유형 선택 단계 중 t2.large 선택
8. VPC 및 Public Subnet 선택
9. 스토리지 추가 단계 중 볼륨 유형은 범용 SSD(gp2) 선택 및 크기는 50GiB 입력
10. 태그 추가 단계 중 키가 Name이고 값이 jumpbox인 태그 추가
11. 보안 그룹 생성(jumpbox) 및 인바운드 내 아이피에 대한 SSH 허용 규칙 추가
12. 검토 단계에서 설정 확인 후 인스턴스 시작
### Docker 설치
#### 필요 도구 설치 및 Docker Repository 설정 
```
sudo apt-get update -y
sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common \
    jq \
    awscli
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -    
```
#### Repository apt-key fingerprint 확인
```
sudo apt-key fingerprint 0EBFCD88
```
##### 아래와 같이 출력 되면 정상
```
$ sudo apt-get fingerprint 0EBFCD88

pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid           [ unknown] Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

#### Docker Repository 추가
```
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```
#### Docker 패키지 설치
```
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```
### kubectl 설치
```
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```
#### kubectl 설치 확인
```
kubectl version --client
```

### Tanzu Kubernetes Grid (tkg) CLI 설치
1. https://www.vmware.com/go/get-tkg 접속
2. Select Version 1.1 확인 및 GO TO DOWNLOADS 클릭
3. VMware Tanzu Kubernetes Grid CLI 1.1 Linux 다운로드
4. 다운로드한 바이너리 압축 해제 및 이동
```
gunzip tkg-linux-amd64-v1.1.0-vmware.1.gz
mv ./tkg-linux-amd64-v1.1.0-vmware.1 tkg
chmod +x ./tkg
sudo mv ./tkg /usr/local/bin/tkg
tkg version
```


