# 2주차 내용 정리

4월 원티드 프리 온보딩 챌린지 - docker 뿌시기 2번째 수업 정리

## 1. 로컬 환경에서 도커를 활용하자

---

- 도커 컴포즈란 ?

```
- Docker docs

Compose 파일은 Docker 응용 프로그램의 서비스, 네트워크 및 볼륨을 정의하는 YAML 파일입니다.
구성 파일 형식의 최신 권장 버전은 구성 사양에 의해 정의됩니다.
구성은 레거시 2.x 및 3.x 버전을 병합하여 이러한 형식의 속성을 집계하며 구성 1.27에 의해 구현됩니다
```

- 도커 컴포즈 설정 파일 ([컴포즈 예제](https://github.com/drum-grammer/docker-pro-wanted/blob/main/local-infra.yml, "docker hub Link"))

- 도커 컴포즈를 활용하여 워드 프레스 서비스 실행하기

## 1-1 컴포즈를 뜯어보자

---

- `Service`
  - 실행하려는 컨테이너들을 정의하는 역할
  - 이름, 이미지, 포트 매핑, 환경 변수, 볼륨 등을 포함
  - 해당 정보를 가지고 컨테이너를 생성하고 관리
- image : 컨테이너를 생성할 때 쓰일 이미지 지정
- build: 정의된 도커파일에서 이미지를 빌드해 서비스 컨테이너를 생성하도록 설정
- environment: 환경 변수 설정, docker run 명령어의 --env, -e 옵션과 돌일
- commnad: 컨테이너가 실행될 때 수행할 명령어, docker run 명령어의 마지막에 붙이는 컨대므와 동일
- depends_on: 컨테이너 간의 의존성 주입, 명시된 컨테이너가 먼저 생성되고 실행
- ports: 개방할 포트 지정, docker run 명령어의 -p와 동일 (포트 포워딩)
- expose: 링크로 연계된 컨테이너에게만 공개할 포트 설정
- volumes: 컨테이너에 볼룸을 마운트함
- restart: 컨테이너가 종료될 때 재시작 정의

- `Service`
  - db: 우리는 mysql:latest를 사용할 것
    - volume: 해당 로컬에 해당 파일의 볼륨을 사용함
    - restart: 재시작은 always(항상) 옵션
    - enviroment: DB의 환경변수
  - app
    - deponds_on: db가 먼저 실행 된 후 실행될수 있도록 설정
    - volume: 동일
    - port: 포트 매핑
    - restart: 동일
    - enviromnet: word process 환경변수 설정

## 2. docker 컴포즈 컨테이너 관리하기

---

network create

```
docker ps
docker network ls
docker network create wordpress_net
docker network prune
```

mysql create

`````
docker \
run \
    --name "db" \
    -v "$(pwd)/db_data:/var/lib/mysql" \
    -e "MYSQL_ROOT_PASSWORD=root_pass" \
    -e "MYSQL_DATABASE=wordpress" \
    -e "MYSQL_USER=docker_pro" \
    -e "MYSQL_PASSWORD=docker_pro_pass" \
    --network wordpress_net \
mysql:latest
````w
`````

위 명령어들 대신에 compose 파일을 실행시키도록 함

```
docker-compose -f docker-compose.yml up --build
```

dokcer container all stop

```
docker stop $(docker ps -aq)
```
