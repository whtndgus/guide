# 도커 사용법

## 도커 설치

### 기존 설치 제거

`$ sudo apt remove docker docker-engine docker.io containerd runc`

기존에 저장된 도커 오브젝트(images, containers, volumes, network) 제거
`$ sudo rm -rf /var/lib/docker`
`$ sudo rm -rf /var/lib/containerd`

### 도커 설치 스크립트 다운로드

`$ sudo apt-get update`
`$ sudo apt-get install curl`
`$ curl https://get.docker.com > docker-install.sh`
`$ chmod 755 docker-install.sh`

### 도커 설치

`$ sudo ./docker-install.sh`

## 도커 컨테이너 다루기

### 도커 컨테이너 생성 및 실행

우분투 14.04 컨테이너 생성

- `$ sudo docker run -i -t ubuntu:14.04`
- `-i` : 상호 입출력 하겠다고 설정
- `-t` : tty를 활성화해서 bash 셸을 사용하도록 설정

컨테이터 나가기 및 종료

- `$ exit` 또는 Ctrl + D : 배시셸을 종료함으로써 컨테이너를 정지시킨다.
- Ctrl + P 다음에 Ctrl + Q(Ctrl + P, Q): 단순히 컨테이너의 셸만 빠져나온다.

### 도커 컨테이너 실행 세부 단계

도커 이미지 내려받기

- `$ sudo docker pull centos:7`

도커 이미지 목록 확인하기

- `$ sudo docker images`

도커 이미지로 컨테이너 생성하기

- `$ sudo docker create -i -t --name mycentos centos:7`

도커 컨테이너 실행하기

- `$ sudo docker start mycentos`
- `$ sudo docker start 해시아이디일부분`

도커 컨테이너에 들어가기

- `$ sudo docker attach mycentos`

정리

- `docker run -i -t`
  - 이미지 없으면 `docker pull`
  - `docker create -i -t`
  - `docker start`
  - `docker attach` : -i -t 옵션을 사용했을 때
- `docker create`
  - 이미지 없으면 `docker pull`
  - `docker create -i -t`

### 도커 컨테이너 목록 확인

실행 중인 컨테이너 목록 보기

- `$ sudo docker ps`

모든 컨테이너 목록 보기

- `$ sudo docker ps -a`
  - CONTAINER ID: 컨테이너에 자동 할당되는 고유한 ID.
    - `$ sudo docker inspect 컨테이너이름 | grep Id` : 컨테이너 ID 전체 보기
  - IMAGE: 컨테이너 이미지 이름
  - COMMAND: 컨테이너가 시작될 때 실행될 명령
  - CREATED: 컨테이너가 생성된 후 흐른 시간
  - STATUS: 컨테이너 상태. Up(실행중), Exited(종료), Pause(중지)
  - PORTS: 컨테이너가 개방한 포트와 호스트에 연결한 포트. 외부에 노출하도록 설정하지 않았다면 출력 내용 없음.
  - NAMES: 컨테이너의 고유한 이름. --name 옵션으로 설정한 이름. 설정하지 않으면 형용사와 명사를 사용해 무작위 생성.
    - `$ sudo docker rename 무작위이름 새이름`

### 도커 컨테이너 삭제

사용하지 않는 컨테이너 삭제하기

- `$ sudo docker rm epic_dubinsky`

실행 중인 컨테이너 삭제하기

- `$ sudo docker stop mycentos` : 실행 중인 컨테이너 정지시키기
- `$ sudo docker rm mycentos` : 컨테이너 삭제하기

또는 `-f` 옵션을 사용하여 종료와 삭제를 한 번에 처리하기

- `$ sudo docker rm -f mycentos`

정지된 모든 컨테이너 삭제하기

- `$ sudo docker container prune`

실행 중인 컨테이너 모두 정지 후 삭제하기

- `$ sudo docker stop $(sudo docker ps -a -q)`
- `$ sudo docker rm $(sudo docker ps -a -q)`

### 도커 컨테이너를 외부에 노출하기

도커가 설치된 호스트에서만 접근 가능

- `$ sudo docker run -i -t --name network_test ubuntu:14.04`
  - NIC 확인: `# ifconfig`

컨테이너를 노출 시키기

- `$ sudo docker run -i -t --name mywebserver -p 80:80 ubuntu:14.04`
  - `-p 호스트포트:컨테이너포트`
  - 여러 개의 포트 노출: `-p` 옵션을 여러 개 삽입
  - `-p 컨테이너포트` : 호스트의 임의 포트 번호랑 컨테이너 포트를 연결한다.

컨테이너에 아파치 웹 서버 설치 및 시작시키기

- `root@xxx# apt-get update`
- `root@xxx# apt-get install apache2 -y`
- `root@xxx# service apache2 start`

컨테이너를 실행하고 있는 호스트로 접속하기

- `http://vagrant리눅스IP/`
  - vagrant ssh 밖에서 실행할 것

호스트와 바인딩된 포트번호 확인하기

- `$ sudo docker port 컨테이너이름`
- `$ sudo docker port mywebserver`

### Detached 모드 컨테이너의 내부 셸을 사용하기

상호 입출력 가능한 상태로 접속하기

- `$ sudo docker exec -i -t 컨테이너이름 /bin/bash`

컨테이너 내부의 실행 결과만 확인하기

- `$ sudo docker exec 컨테이너이름 ls`

## 도커 컨테이너 활용

### 데이터베이스 컨테이너와 웹서버 컨테이너 만들기

데이터베이스 컨테이너 만들기

- `$ sudo docker run -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7`

도커 이미지를 다운로드 받을 때 플랫폼 이름을 명시하지 않으면
현재 사용하는 OS에 맞는 이미지를 찾는다.
macOS 플랫폼에 맞는 MySQL 이미지를 찾을 수 없다.
macOS에서 이미지를 다운로드 받고 싶다면 플랫폼 이름을 명시하라!

- `$ sudo docker run --platform linux/amd64 -d --name wordpressdb -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress mysql:5.7`

워드프레스 기반 블로그 서비스 만들기

- `$ sudo docker run -d --name wordpress -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password --link wordpressdb:mysql -p 80 wordpress`

접속 확인

- `http://vagrant리눅스IP:자동설정된포트번호/`
  - vagrant ssh 밖에서 실행할 것

도커 컨테이너 run 옵션

- `$ sudo docker run -d ... -e ... --link ...`
  - `-d` : Detached 모드로 컨테이너 실행. 컨테이너에서 백그라운드에서 동작하는 애플리케이션을 실행할 때 사용.
    - 이 모드에서는 컨테이너가 실행하는 프로그램이 없으면 자동 종료된다.
      - 테스트: `$ sudo docker run -d --name detach_test ubuntu:14.04`
      - `$ sudo docker ps -a` 로 확인해보면 컨테이너가 실행 즉시 종료되었음을 확인할 수 있다.
  - `-e 환경변수명=값` : 컨테이너 내부의 환경변수 설정. 컨테이너에서 실행되는 애플리케이션이 이 환경 변수를 사용한다.
    - Detached 모드 컨테이너의 내부 환경 변수 확인하기
      - 셸 접속: `$ sudo docker exec -i -t wordpressdb /bin/bash`
      - 환경 변수 확인: `# echo $MYSQL_ROOT_PASSWORD`
  - `--link 컨테이너명:별명` : 내부 IP를 알 필요 없이 컨테이너 별명으로 접근하도록 설정
    - 도커 엔진은 컨테이너에게 내부 IP를 172.17.0.2, 3, 4, ... 와 같이 순차적으로 할당.
    - 테스트: `$ sudo docker exec wordpress curl mysql:3306 --silent`

### 도커 볼륨 다루기

#### 호스트 볼륨 공유하기

- `$ sudo docker run -d --name wordpressdb_hostvolume -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=wordpress -v /home/wordpress_db:/var/lib/mysql mysql:5.7 `
  - `-v 호스트의공유디렉토리:컨테이너의공유디렉토리`
  - 호스트의 공유 디렉토리가 없으면 도커가 자동 생성한다.
- `$ sudo docker run -d --name wordpress_hostvolume -e WORDPRESS_DB_HOST=mysql -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=password --link wordpressdb_hostvolume:mysql -p 80 wordpress`

컨테이너 삭제 후에도 데이터가 보존되는 것을 확인하기

- `$ sudo docker stop wordpress_hostvolume wordpressdb_hostvolume`
- `$ sudo docker rm wordpress_hostvolume wordpressdb_hostvolume`

호스트 디렉토리를 컨테이너의 존재하는 디렉토리와 연결할 때

- `$ sudo docker run -i -t --name volume_dummy alicek106/volume_test`
  - 컨테이너에 존재하는 디렉토리 확인: `# ls /home/testdir_2/`
- `$ sudo docker run -i -t --name volume_override -v /home/wordpress_db:/home/testdir_2 alicek106/volume_test`
  - 컨테이너에 존재하는 디렉토리 확인: `# ls /home/testdir_2/`
    - 기존의 디렉토리를 호스트 디렉토리로 대체한다.

#### 볼륨 컨테이너 공유하기

volume_override 컨테이너의 볼륨을 공유하기

- `$ sudo docker run -i -t --name volumes_from_container --volumes-from volume_override ubuntu:14.04`
  - 컨테이너에 추가된 디렉토리 확인: `# ls /home/testdir_2/`

#### 도커 볼륨 사용하기

도커 볼륨 생성하기

- `$ sudo docker volume create --name myvolume`

도커 볼륨 조회하기

- `$ sudo docker volume ls`

도커 볼륨 사용하기

- `$ sudo docker run -i -t --name myvolume_1 -v myvolume:/root/ ubuntu:14.04`
  - `# echo Hello, volume! >> /root/test`

도커 볼륨 공유하기

- `$ sudo docker run -i -t --name myvolume_2 -v myvolume:/root/ ubuntu:14.04`
  - `# cat /root/volume`

도커 볼륨의 실제 위치 알아내기

- `$ sudo docker inspect --type volume myvolume`
  - `docker inspect` 명령: 컨테이너, 이미지, 볼륨 등 도커의 모든 구성 단위의 정보를 확인할 때 사용
    - `--type [image|volume|container]`

도커 볼륨 자동 생성하기

- `$ sudo docker run -i -t --name volume_auto -v /root/ ubuntu:14.04`

컨테이너가 사용하는 도커 볼륨 확인하기

- `$ sudo docker container inspect volume_auto`

사용하지 않는 볼륨을 자동으로 삭제하기

- `$ sudo docker volume prune`

#### mount 옵션

mount 옵션으로 도커 볼륨 연결하기

- `$ sudo docker run -i -t --name mount_option_1 --mount type=volume,source=myvolume,target=/root/ ubuntu:14.04`

mount 옵션으로 호스트 디렉토리를 컨테이너에 연결하기

- `$ sudo docker run -i -t --name mount_option_2 --mount type=bind,source=/home/wordpress_db,target=/root/ ubuntu:14.04`

## 도커 네트워크 다루기

도커 호스트의 가상 이더넷 카드

- `$ ifconfig`
  - 실행 중인 컨테이너 개수 만큼 `vethxxxx` 가상 이더넷 카드가 생성된 것을 확인 할 수 있다.

### 도커 네트워크

### 컨테이너의 네트워크

도커에서 기본적으로 쓸 수 있는 네트워크 확인하기

- `$ sudo docker network ls`
  - bridge: 컨테이너를 생성할 때 자동으로 연결되는 docker0 브리지를 활용하도록 설정됨
    - 172.17.0.x IP 대역을 컨테이너에 순차적으로 할당한다.

도커 엔진의 네트워크 목록 조회

- `$ sudo docker network ls`

특정 네트워크의 상태 조사하기

- `$ sudo docker network inspect 네트워크이름`
- `$ sudo docker network inspect bridge`

### 브릿지 네트워크

#### 브릿지 바인딩 조회

브릿지 바인딩 정보 조회를 위한 도구 설치

- `$ sudo apt-get install bridge-utils`

브릿지 바인딩 정보 조회하기

- `$ brctl show docker0`

#### 브릿지 네트워크 생성

새 브릿지 네트워크 생성하기

- `$ sudo docker network create --driver bridge mybridge`

새 컨테이너에 브릿지 네트워크 연결하기

- `$ sudo docker run -it --network="mybridge" --name network1 ubuntu:14.04`

브릿지 네트워크 상세 정보 보기

- `$ sudo docker network inspect mybridge`

#### 컨테이너에 브릿지 네트워크를 붙이기/떼기

컨테이너에 브릿지 네트워크 떼기

- `$ sudo docker network disconnect mybridge network1`
- `$ sudo docker attach network1`
- `# ifconfig`
  - eth0 네트워크 인터페이스가 제거된 것을 확인할 수 있다.

컨테이너에 브릿지 네트워크 붙이기

- `$ sudo docker network connect bridge network1`
- `$ sudo docker attach network1`
- `# ifconfig`
  - eth0가 생성된다.
  - bidge 네터워크의 주소가 컨테이너에 연결된 순서대로 설정된다.(예: 172.17.0.\*)

## 컨테이너 로깅

### json-file 로그 사용

## 도커 이미지 다루기

도커 허브라는 중앙 이미지 저장소에서 도커 이미지 검색하기

- `$ sudo docker search 키워드`

### 도커 이미지 생성

이미지 목록 조회

- `$ sudo docker images`

이미지로 만들 컨테이너 준비

- `$ sudo docker run -i -t --name commit_test ubuntu:14.04`
  - 컨테이너 변경: `# echo test_first! >> first`

컨테이너를 이미지로 만들기

- `$ sudo docker commit 옵션 컨테이너명 REPOSITORY:TAG`
- `$ sudo docker commit -a "alicek106" -m "my first commit" commit_test commit_test:first`
  - `-a author` : 이미지 작성자에 대한 정보를 이미지에 포함시킨다.
  - `-m 커밋메시지` : 이미지에 포함될 부가 설명을 설정한다.

만든 이미지로 컨테이너 만들기

- `$ sudo docker run -i -t --name commit_test2 commit_test:first`
  - 컨테이너 변경: `# echo test_second! >> second`

변경한 컨테이너로 새 이미지 만들기

- `$ sudo docker commit -a "alicek106" -m "my second commit" commit_test2 commit_test:second`

### 도커 이미지 정보 조회

이미지 정보 살펴보기

- `$ sudo docker inspect ubuntu:14.04`
- `$ sudo docker inspect commit_test:first`
  - Layers = ubuntu:14.04 Layers + 추가사항 I
- `$ sudo docker inspect commit_test:second`
  - Layers = commit_test:first Layers + 추가사항 II
  - Layers = ubuntu:14.04 Layers + 추가사항 I + 추가사항 II

이미지 레이어 구조에 대한 변경 내역 확인하기

- `$ sudo docker history 도커이미지명`
- `$ sudo docker history commit_test:first`

### 도커 이미지 삭제

이미지 삭제하기

- `$ sudo docker rmi commit_test:first`
  - 이미지를 사용 중인 컨테이너가 있을 경우 삭제할 수 없다.
  - 컨테이너를 먼저 삭제한 후 이미지를 삭제해야 한다.
    - `$ sudo docker stop commit_test2 && sudo docker rm commit_test2`

댕글링(dangling) 이미지 다루기

- 컨테이너 생성
  - `$ sudo docker run -i -t --name dangle_test commit_test:second`
  - 컨테이너 확인: `$ sudo docker ps -a`
- 컨테이너가 사용한 이미지 강제 삭제
  - `$ sudo docker rmi -f commit_test:second`
  - 컨테이너 확인: `$ sudo docker ps -a`
    - 이미지 이름이 변경되어 있다.
- 삭제된 이미지 확인
  - `$ sudo docker images -f dangling=true`

### 도커 이미지 추출

도커 이미지를 한 개의 파일로 추출하기

-`$ sudo docker save -o ubuntu_14_04.tar ubuntu:14.04`

도커 이미지 로드하기

- `$ sudo docker load -i ubuntu_14_04.tar`

### 도커 이미지 배포

#### 도커 허브(<https://hub.docker.com/>)에 회원 가입 및 로그인

#### 이미지 저장소 생성

- _Create a Repository_ 클릭
- 저장소명: hello-docker
- 설명: 테스트용
- Visibility: private

#### 테스트용 이미지 생성

- `$ sudo docker run -i -t --name mycontainer ubuntu:14.04`
  - 컨테이너 변경: `# echo my first push >> test`

#### 테스트용 이미지 커밋하기

- `$ sudo docker commit mycontainer hello-docker:0.1`

#### 이미지에 태깅하기

- `$ sudo docker tag local-image:tagname new-repo:tagname`
- `$ sudo docker tag hello-docker:0.1 eomjinyoung/hello-docker:0.1`

#### 도커 허브에 로그인 하기

- `$ sudo docker login`

#### 저장소에 이미지 올리기

- `$ sudo docker push new-repo:tagname`
- `$ sudo docker push eomjinyoung/hello-docker:0.1`

도커 허브 사이트의 Tags 탭에서 확인할 것!

#### 저장소에 업로드한 이미지 가져오기

- `$ sudo docker pull eomjinyoung/hello-docker:0.1`

#### 가져온 이미지로 컨테이너 생성, 실행 및 접속

도커 이미지 목록 확인하기

- `$ sudo docker images`

도커 이미지로 컨테이너 생성하기

- `$ sudo docker create -i -t --name hello1 eomjinyoung/hello-docker:0.1`

도커 컨테이너 실행하기

- `$ sudo docker start hello1`

도커 컨테이너에 들어가기

- `$ sudo docker attach hello1`

### 웹 애플리케이션 배포 - 직접 컨테이너 구성 및 배치하기

#### MariaDB 컨테이너 생성

MariaDB 도커 이미지 가져오기

- `$ sudo docker pull mariadb`

MariaDB 컨테이너 생성 및 실행

- `$ sudo docker run -p 3306:3306 --detach --name mariadb --env MARIADB_USER=study --env MARIADB_PASSWORD=1111 --env MARIADB_ROOT_PASSWORD=1111  mariadb`

MariaDB 컨테이너 접속

- `$ sudo docker exec -it mariadb /bin/bash`

MariaDB 버전 확인

- `# mysql --version`

MariaDB 클라이언트 실행 및 root 사용자로 서버 접속

- `# mysql -u root -p`

웹애플리케이션의 DB 환경 구축

- `studydb` 데이터베이스 생성
  - `CREATE DATABASE studydb CHARACTER SET utf8 COLLATE utf8_general_ci`
- `study` 사용자에게 `studydb` 데이터베이스 사용권한 부여
  - `GRANT ALL ON studydb.* TO 'study'@'%'`
- `study` 사용자로 로그인
  - `# mysql -u study -p`
- `study` 사용자가 사용할 수 있는 데이터베이스 확인
  - `show databases`
- 웹 애플리케이션 테이블 생성
  - myapp/app-server/doc/
    - ddl.sql 실행
    - ddl2.sql 실행
    - ddl3.sql 실행
    - ddl4.sql 실행
    - data4.sql 실행

#### myapp 웹 애플리케이션 컨테이너 생성

Ubuntu 22.04 컨테이너 생성 및 실행

- `$ sudo docker run -p 80:80 -it --name myapp ubuntu`

ifconfig 등 네트워킹 관련 프로그램 추가

- `# apt update`
- `# apt install net-tools`

nano 에디터 설치

- `# apt install nano`

JDK 17 설치

- `# apt install openjdk-17-jdk -y`

JAVA_HOME 환경 변수 설정

- `# echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' | tee /etc/profile.d/java17.sh`
  - `# source /etc/profile.d/java17.sh`
  - docker 컨테이너에서 적용 안됨.
- `# echo 'export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64' | tee -a /etc/bash.bashrc`
  - `# source /etc/bash.bashrc`
  - docker 컨테이너에서 적용됨
  - Apple silicon에서 실행하는 macOS 에서 JDK 설치하면 디렉토리 이름이 다른다.
    - `/usr/lib/jvm/java-17-openjdk-arm64`

git 설치

- `# apt install git -y`

wget 설치

- `# apt install wget -y`

unzip 설치

- `# apt install unzip -y`

gradle 다운로드 및 설치

- `# VERSION=8.0.2`
- `# wget https://services.gradle.org/distributions/gradle-${VERSION}-bin.zip -P /tmp`
- `# unzip -d /opt/gradle /tmp/gradle-${VERSION}-bin.zip`
- `# ln -s /opt/gradle/gradle-${VERSION} /opt/gradle/latest`
- `# echo 'export GRADLE_HOME=/opt/gradle/latest' | tee -a /etc/bash.bashrc`
- `# echo 'export PATH=${GRADLE_HOME}/bin:${PATH}' | tee -a /etc/bash.bashrc`
- `# source /etc/bash.bashrc`

nodejs 및 npm 설치

- `# apt install nodejs -y`
- `# apt install npm -y`

myapp git 저장소 가져오기

- `# mkdir git`
- `# cd git`
- `# git clone https://github.com/eomjinyoung/bitcamp-study`

myapp 자바스크립트 라이브러리 설치

- `# cd ~/git/bitcamp-study/myapp/app-server/src/main/webapp`
- `~/git/bitcamp-study/myapp/app-server/src/main/webapp# npm install`

myapp 빌드

- `# cd ~/git/bitcamp-study/myapp`
- `~/git/bitcamp-study/myapp# gradle build`

ping 설치

- `# apt-get install iputils-ping -y`

mariadb 접속 확인

- `# ping 172.17.0.2`

myapp 실행

- `~# java -jar ~/git/bitcamp-study/myapp/app-server/build/libs/app-server-0.0.1-SNAPSHOT.jar`

## Dockerfile 다루기

### 스프링부트 웹 프로젝트를 도커 컨테이너로 실행하기

#### Docker 파일 작성

.../myapp/app-server/Dockerfile

```
FROM openjdk:17

ARG JAR_FILE=build/libs/app-server-0.0.1-SNAPSHOT.jar

COPY ${JAR_FILE} app.jar

ENTRYPOINT [ "java", "-jar", "app.jar" ]
```

#### Dockerfile로 이미지 생성하기

```
$ docker build -t eomjinyoung/myapp:0.0.2 .
```

#### 컨테이너 생성 및 실행하기

```
$ docker run -d -p 80:80 -v /mnt/c/Users/bitcamp/webapp-upload:/root/webapp-upload --name myapp eomjinyoung/myapp:0.0.2
```


<br>
<br>
<br>


---------------------------------------------------------------------------------------------------------------------------------------------------------------------


<br>
<br>
<br>



# Jenkins 사용법

## Jenkins 설치

### 네트워크 브릿지 생성

젠킨스 도커 컨테이너에서 사용할 브릿지 네트워크를 준비한다.

```
# docker network ls
# docker network create jenkins
# docker network ls
```

### 젠킨스 도커 컨테이너 생성 및 실행

#### 젠킨스 도커 이미지 가져오기

```
# docker pull jenkins/jenkins:lts-jdk17
# docker image ls
```

#### 도커 이미지 만들기: 젠킨스 + JDK17 + 도커 클라이언트

작업 디렉토리 생성

```
# mkdir jenkins
# cd jenkins
```

install-docker.sh 파일 생성

```
# vi install-docker.sh
```

install-docker.sh 파일 내용

```shell
#!/bin/sh

apt-get update

apt-get -y install apt-transport-https \
     apt-utils \
     ca-certificates \
     curl \
     gnupg2 \
     zip \
     unzip \
     acl \
     software-properties-common

curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey

add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
   $(lsb_release -cs) \
   stable" && \

apt-get update

apt-get -y install docker-ce
```

도커 빌드 파일 생성

```
# vi Dockerfile
```

Dockerfile 내용

```shell
FROM jenkins/jenkins:lts-jdk17

USER root

COPY install-docker.sh /install-docker.sh
RUN chmod +x /install-docker.sh
RUN /install-docker.sh

RUN usermod -aG docker jenkins
RUN setfacl -Rm d:g:docker:rwx,g:docker:rwx /var/run/

USER jenkins
```

도커 이미지 생성

```
# docker build -t eomjinyoung/hello-docker:1.0 .
```

도커 이미지를 도커 허브 사이트에 업로드 하기

```
# docker login
# docker push eomjinyoung/hello-docker:1.0
```

컨테이너 생성 및 실행하기(DooD 방식)

```
# docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v jenkins_home:/var/jenkins_home -p 8080:8080 -p 50000:50000 --restart=on-failure --network="jenkins" --name docker-jenkins eomjinyoung/hello-docker:1.0
# docker container ls
```

젠킨스 컨테이너를 재생성 할 때, 기존 젠킨스 볼륨을 제거하기

```
# docker volume ls
# docker volume rm jenkins_home
```

### 젠킨스 설정

젠킨스 잠금을 풀기 위해 관리자 암호 찾아서 넣기

```
# docker logs docker-jenkins
```

젠킨스 플러그인 설치

```
"Install suggested plugins" 클릭
```

첫 번째 젠킨스 관리자 등록

```
계정명: admin
암호: 1111
암호확인: 1111
이름: admin
이메일주소: xxx@xxx.xxx
```

젠킨스 루트 URL 설정

```
http://서버주소:8080
```

젠킨스 사용 시작!

### 젠킨스 환경 설정

#### JDK 및 Gradle 설정

- Dashboard / Jenkins 관리
  - System Configuration / Global Tool Configuration
    - JDK
      - `Add JDK` 클릭
      - Name: OpenJDK-17
      - JAVA_HOME: /opt/java/openjdk
      - Apply 클릭
    - Gradle
      - `Add Gradle` 클릭
      - Name: Gradle 8.1.1
      - Install automatically: 체크
      - Version: 8.1.1 선택
      - Apply 클릭

#### Node.js 설정

- Dashboard / Jenkins 관리
  - 플러그인 관리
    - Available plugins 선택
      - `Nodejs` 검색
      - `NodeJS` 체크
      - `Install without restart` 클릭
  - System Configuration / Global Tool Configuration
    - NodeJS
      - `Add NodeJS` 클릭
      - Name: NodeJS 18.16.0
      - Install automatically: 체크
      - Version: 18.16.0 선택
      - Apply 클릭

### jenkins/jenkins:lts-jdk11 을 설치했을 경우

#### JDK 17 설치

root 사용자로 젠킨스 컨테이너에 접속하기

```
호스트# docker exec -itu 0 jenkins-jdk11 bash
컨테이너/# apt-get update
컨테이너/# apt-get install openjdk-17-jdk -y
```

#### 젠킨스에 JDK 17 경로 등록

- Jenkins 관리
  - Global Tool Configuration
    - JDK
      - `Add JDK` 클릭
        - Name: `OpenJDK-17`
        - JAVA_HOME: `/usr/lib/jvm/java-17-openjdk-amd64`
    - SAVE 클릭

## Jenkins에 프로젝트 등록 및 자동 배포하기

### github.com의 프로젝트 연동

Dashboard

- 새로운 Item
  - Enter an item name: `myapp`
  - `Freestyle project` 클릭
  - OK 클릭
- 설정
  - General
    - 설명: `myapp 빌드`
    - `GitHub project` 체크
      - Project url: `https://github.com/eomjinyoung/bitcamp-ncp-myapp.git`
  - 소스 코드 관리
    - `Git` 선택
      - Repository URL: `https://github.com/eomjinyoung/bitcamp-ncp-myapp.git`
      - Credentials: (push를 할 경우)
        - Add 버튼 클릭: `Add Jenkins` 선택
        - `Username with Password` 선택
          - Username: 깃허브 사용자이름
          - Password: 깃허브 토큰
          - 'Add' 클릭
        - `Username/토큰` 선택
      - Branch Specifier: \*/main
  - 빌드 유발
    - `GitHub hook trigger for GITScm polling` 선택
  - 빌드 환경
    - `Provide Node & npm bin/ foler to PATH` 체크
    - `NodeJS 18.16.0` 선택
  - Build Steps
    - `Invoke Gradle script` 선택
      - `Invoke Gradle` 선택
        - Gradle Version: Gradle 8.1.1 선택
      - Tasks
        - `clean build` 입력
  - 저장
- `지금 빌드` 클릭
  - Console Output 확인
  - `docker exec -itu 0 docker-jenkins bash` 접속

### github webhook 연동

- Repository/Settings/Webhooks
  - `Add webook` 클릭
    - Payload URL: `http://젠킨스서버주소:8080/github-webhook/`
    - Content type: `application/json`
    - 저장

### 스프링부트 애플리케이션 docker 이미지 생성 및 도커 허브에 push 하기

- Dashboard
  - `myapp` Freestyle 아이템 선택
    - `구성` 탭 선택
      - Build Steps
        - `Add build step` : Execute shell 클릭
          - `docker build -t [dockerHub UserName]/[dockerHub Repository]:[version] app/`
          - `docker login -u '도커허브아이디' -p '도커허브비번' docker.io`
          - `docker push [dockerHub UserName]/[dockerHub Repository]:[version]`
        - 저장

/var/run/docker.sock의 permission denied 발생하는 경우

```
호스트# chmod 666 /var/run/docker.sock
```

## 스프링부트 애플리케이션 컨테이너 실행하기

### 작업 디렉토리 만들기

```
root@bitcamp-jenkins:~# mkdir springboot
root@bitcamp-jenkins:~# cd springboot
root@bitcamp-jenkins:~/springboot#
```

### ssh key 생성

docker-jenkins 도커 컨테이너에서 실행

```
root@bitcamp-jenkins:~/springboot# docker exec -itu 0 docker-jenkins bash
root@젠킨스컨테이너:/# ssh-keygen -t rsa -C "docker-jenkins-key" -m PEM -P "" -f /root/.ssh/docker-jenkins-key
root@젠킨스컨테이너:/# ls ~/.ssh/
docker-jenkins-key  docker-jenkins-key.pub
root@젠킨스컨테이너:/# cat ~/.ssh/docker-jenkins-key
-----BEGIN RSA PRIVATE KEY-----
MIIG4wIBAAKCAYEAvHpMt5Pyqt6muGkF432CrlKaQ3qk78nf3xn/Jpo5LaG9cuK5
...
root@젠킨스컨테이너:/# cat ~/.ssh/docker-jenkins-key.pub
ssh-rsa AAAAB3NzaC1yc2E
...
```

SSH-KEY 개인키 파일을 젠킨스 홈 폴더에 두기

```
root@젠킨스컨테이너:/# cp ~/.ssh/docker-jenkins-key /var/jenkins_home/
root@젠킨스컨테이너:/# chmod +r /var/jenkins_home/docker-jenkins-key
```

docker-jenkins 컨테이너의 SSH-KEY 공개키 파일을 Host로 복사해오기

```
root@bitcamp-jenkins:~/springboot# docker cp docker-jenkins:/root/.ssh/docker-jenkins-key.pub ./docker-jenkins-key.pub
root@bitcamp-jenkins:~/springboot# cat docker-jenkins-key.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQA...

```

### 스프링부트 서버에 SSH-KEY 공개키 등록하기

```
root@bitcamp-springboot:~# mkdir .ssh
root@bitcamp-springboot:~# cd .ssh
root@bitcamp-springboot:~/.ssh# vi authorized_keys
Jenkins 컨테이너의 공개키 복사
```

### Jenkins에 Publish Over SSH 플러그인 설정

플러그인 설치

- Jenkins 관리
  - 플러그인 관리
  - `Available plugins` 탭
    - `Publish Over SSH` 플러그인 설치

플러그인 연동

- Jenkins 관리
  - 시스템 설정
    - Publish over SSH
      - Passphrase: 접속하려는 컨테이너의 root 암호 <=== 암호로 접속할 때
      - Path to key: docker-jenkins-key <=== SSH-KEY로 접속할 때(private key 파일 경로, JENKINS_HOME 기준)
      - Key: 개인키 파일의 내용 <=== SSH-KEY 개인키 파일의 내용을 직접 입력할 때
      - 추가 버튼 클릭
      - SSH Servers
        - Name: 임의의서버 이름
        - Hostname: 스프링부트서버의 IP 주소
        - Username: 사용자 아이디
        - `Test Configuration` 버튼 클릭
          - `Success` OK!

### Jenkins 서버에서 스프링부트 서버를 제어하여 스프링부트 컨테이너 실행하기

스프링부트 서버에서 docker pull 및 run

- Dashboard
  - `myapp` Freestyle 아이템 선택
    - `구성` 탭 선택
      - 빌드 환경
        - `Send files or execute commands over SSH after the build runs` 선택
          - SSH Server Name: Publish over SSH에 등록한 서버를 선택
          - Transfers
            - Exec command
              - `docker login -u '도커허브아아디' -p '도커허브비번' docker.io`
              - `docker pull [dockerHub UserName]/[dockerHub Repository]:[version]`
              - `docker ps -q --filter name=[containerName] | grep -q . && docker rm -f $(docker ps -aq --filter name=[containerName])`
              - `docker run -d --name [containerName] -p 80:80 [dockerHub UserName]/[dockerHub Repository]:[version]`
