#

## 도커란 ?

도커는 리눅스의 응용 프로그램들을 프로세스 격리 기술들을 사용하여 컨테이너로 실행하고 관리하는 오픈소스 프로젝트이다.

```bash
도커 컨테이너는 일종의 소프트웨어를 소프트웨어의 실행에 필요한 모든 것을 포함하는
완전한 파일 시스템 안에 감싼다. 여기에는 코드, 런타임, 시스템 도구, 시스템 라이브러리 등
서버에 설치되는 무엇이든 아우른다. 이는 실행 중인 환경에 관계 없이
언제나 동일하게 실행될 것을 보증한다.
```

![](https://i.imgur.com/4HHvyVt.png)

가상머신은 하이퍼바이저를 이용해 Guest OS를 만들어낸다. 예를 들어, 윈도우 운영체제를 메인으로 쓰고 있다면 이는 Host OS가 되는 것이고 이 위에 Ubuntu를 가상머신위에 구동시킨다면 이는 Guest OS가 되는 것이다. Guest OS를 구동시키려면 Host OS에서 자원을 일부 사용해야 한다. 따라서 Host OS도 느려지고, Guest OS도 성능이 그리 좋은 편은 아니다.

도커의 컨테이너에서 필요한 커널은 호스트의 커널과 공유해서 사용한다. 컨테이너 안에서 돌아가는 어플리케이션을 구동하는데 꼭 필요한 라이브러리나 실행파일만 존재하기 때문에 컨테이너를 이미지로 만들경우 용량이 줄어들게 된다.

가상머신과 도커 컨테이너 비교에 대한 논문

[https://arxiv.org/pdf/1807.01842.pdf](https://arxiv.org/pdf/1807.01842.pdf)

**도커 구성요소**

**도커 엔진**

- doker 는 서버/클라이언트의 구조로 이루어짐
- 서버: doker daemon process
- 클라이언트: doker command
- 위둘은 별도의 프로세스이기 때문에 통신을 위해 내부 적으로 rest api 를 사용한다.

```java
예를들어
docker ps 명령시 내부적으로는 http GET 'doker daemon process' /api-version/containers
와같은 RestApi 를 호출한다.
```

**도커 이미지**

- 도커 컨테이너를 생성하기 위한 명령들을 가진 템플릿이다.(컨테이너를 실행하기 위한 설정값등을 포함하고 있다.)
- 여러 이미지들을 Layer 쌓아서 원하는 형태의 이미지를 만드는 것이 일반적이다.
- 예를 들어 우분투 이미지에 아파치 웹서버 이미지를 추가하여 웹서버 이미지를 만들 수 있다.

**도커 컨테이너**

- 도커 이미지가 리눅스 컨테이너 형태로 실행한 상태, 인스턴스를 의미한다.
- **doker daemon** 에있는 커널에서 리눅스 컨테이너를 생성한후 해당 도커 이미지에 포함된 명령을 실행하여 docker container 를 만들고 실행한다.
- 도커 컨테이너는 분리된 공간이므로, doker daemon를 통해 접속할 수도 있고, 내부에 들어가서 코드 수정 재실행도 가능하다.

**정리**

도커는 다른 머신에서도 같은 환경을 구성할 수 있습니다. 그 환경을 구성하기 위해 필요한 설정값들을 이미지라는 파일안에 담아 만들게 됩니다. 예를들어 우분투, MYSQL, 아파치 등등 필요한 것들을 이미지로 만들 수 있습니다.

![](https://i.imgur.com/I1LSdw3.png)

위와 같이 도커는 이파일을 읽고 필요한 것들을 좌르륵 다운받고, 그 환경에 맞춰 가상 컨테이너를 컴퓨터에 만들게 되는 것입니다.

또한 예를 들어 한개는 파이썬, 한개는 장고, 한개는 자바 이와같이 컨테이너들을 독립적으로 관리가 가능하고 컨테이너 끼리의 통신도 가능합니다.

![](https://i.imgur.com/pIKpJoM.png)

만약 위와 같이 하나의 서버에서 자바트래픽이 늘게 된다면 컨테이너를 늘려주면되고, 적어진다면 컨테이너를 중지 시키면 됩니다. 도커로 인해서 매번 새로운 서비스를 만들때 마다 새로운 서버를 사고 설정할 필요성이 적어집니다. 이미 이미지라는 파일에 어플리케이션 구동에 필요한 패키지, 설정등 을 정의했기 때문입니다.

## 도커 활용

1. **도커 이미지 다운 및 실행**

   ```bash
   # ubuntu 이미지 다운받고(이미다운받으면 다운 받아진 이미지를 사용함)를 myubuntu 컨테이너로 구동하는데
   # 백그라운드로 실행하겠다.
   **docker run -it -d --name myubuntu ubuntu**

   # 다운받아진 ubuntu 이미지 확인
   **docker images**
   REPOSITORY   TAG       IMAGE ID       CREATED      SIZE
   ubuntu       latest    1318b700e415   5 days ago   72.8MB

   # 실행중인 도커 컨테이너 확인
   **docker ps**
   CONTAINER ID   IMAGE     COMMAND   CREATED         STATUS         PORTS     NAMES
   e55b1c4758b6   ubuntu    "bash"    8 minutes ago   Up 2 seconds             myubuntu

   # 실행중인 컨테이너 접속
   docker exec -it myubuntu /bin/bash
   ```

1. **도커 파일을 활용**

   **도커 파일**

   - 도커 이미지를 작성할 수 있는 기능이다.
   - 도커 파일 문법으로 이미지 생성을 위한 스크립트 작성을 할 수 있고 이를 기반으로 이미지를 생성하게 된다.
   - 아래와 같은 우분투라는 베이스 이미지에 아파치를 얹어서 하나의 이미지를 만들어서 컨테이너로 실행해보자.

     ![](https://i.imgur.com/KEQaumW.png)

   ```bash
   **Dockerfile-apache**
   --------
   FROM ubuntu:18.04
   LABEL maintainer="hakjun@naver.com"

   # RUN 명령어로 각 레이어를 추가한다.
   # RUN 명령어는 이미지 생성시 일종의 layer 를 만들 수 있는 명령으로 보통 베이스 이미지에 패키지
   # 프로그램을 설치하여, 새로운 이미지를 만들때 많이 사용한다.
   # 패키지 프로그램 정보 업데이트
   RUN apt-get update
   # apache2 를 추가한다.
   RUN apt-get install -y apache2

   # apache2 디폴트 웹서버 설정은 /var/www/html/
   COPY ./2021_DEV /var/www/html/

   # apache2 웹서버 구동 명령은 다음과 같다
   ENTRYPOINT ["/usr/sbin/apache2ctl", "D", "FOREGROUND"]

   -------

   # 빌드한다
   # Dockerfile-apache 내용을 이미지로 만드는데 myweb 이라는 이미지로 생성 하겠다는 내용이다.
   docker build --tag myweb -f Dockerfile-apache ./

   # 생성된 이미지를 확인
   # 아래와 같이 내가 생성한 myweb 이라는 베이스 이미지에 포함된 ubuntu 라는이미지가 생성되는 것을 확인 할 수 있다.
   docker images
   myweb        latest    63f900d532f9   40 seconds ago   197MB
   ubuntu       18.04     39a8cfeef173   5 days ago       63.1MB

   # 생성된 이미지로 컨테이너를 구동한다. myweb 이미지로 myserver 컨테이너를
   # 구동하겠다!
   # 호스트 pc 의 9999 포트의 요청은 컨테이너의 80 번 포트로 포워딩하겠다는 뜻
   docker run -dit -p 9999:80 --name myserver myweb

   # http://{host ip}:9999/ 로 접속하면 아파치 기본 index.html 의내용이 출력된다.
   ```

1. **도커 컴포우즈 활용**

   - 지금까지는 컨테이를 하나씩만 띄워 봤다 하지만 실제 서비스에서는 여러 서버들이 연관되어 돌아간다. 웹 서비스는 프론트앤드 서버, 데이터베이스서버, 백엔드 서버로 이루어져 있는 경우가 많다.
   - **Docker Compose 는 여러 컨테이너를 모아서 관리하기 위한 툴이다.**
   - 아래와같이 nginx, 웹서버, db 로 이루어진 하나의 서비스가 있다고 가정하고 도커 컴포우즈로 구성해보자. 아래 nginx, server, mysql 은 각각 별도의 컨테이너이다.

     ![](https://i.imgur.com/IqMg4rp.png)

**1. 도커 컴포우즈 파일 작성**

      ```
      # 폴더 구조
      ubuntu@ip-172-31-12-149:~/**SPRING_SERVER**$ ls
      **docker-compose.yml  nginx  sp**

      ubuntu@ip-172-31-12-149:~/SPRING_SERVER$ cd sp
      ubuntu@ip-172-31-12-149:~/SPRING_SERVER/**sp**$ ls
      **Dockerfile  demo1-0.0.1-SNAPSHOT.jar**

      # 도커 컴포우즈 파일
      -----------
      version: "3"

      # nginxproxy, db, spring 는 각각의 컨테이너이고 하위에 필요한 설정을 입력한다.
      services:
        nginxproxy:
          depends_on:   # depends_on 은 컨테이너의 의존관계를 의미한다. nginxproxy 컨테이너는 db, spring 컨테이너가 구동된후 실행된다.
            - db
            - spring
          image: nginx:latest
          container_name: proxy #컨테이너의 이름을 지정해 줄 수 있다.
          ports:
            - "80:80"
          restart: always
          volumes:
            - "./nginx/nginx/nginx.conf:/etc/nginx/nginx.conf" #컨테이너의 nginx 설정파일을 호스트피시에 있는 ./nginx/nginx/nginx.conf 설정을 사용하 겠다는 의미이다.

        db:
          image: mysql:5.7
          container_name: mysqldb
          volumes:
            - "mydb:/var/lib/mysql"
          restart: always
          # EXPOSE: 3000, 다음과 같이 컨테이너 내부터서 포트를 열어둘 수 있다.(컨테이너 끼리만 통신 가능!)
          environment:
            MYSQL_ROOT_PASSWORD: test
            MYSQL_DATABASE: test

        spring:
          depends_on:
            - db
          build:   # spring 컨테이너는 따로 Docker 파일을 만들어서 설정을 추가하였다.
            context: ./sp
            dockerfile: Dockerfile
          #contaoner_name: sp
          restart: always

      volumes:
        mydb:

      ```

2. **Dockerfile 작성**

   ```
   # openjdk:8-jdk-alpine 이미지를 다운
   FROM openjdk:8-jdk-alpine

   # 작업 디렉토리를 설정
   WORKDIR /app

   # 호스트 피시에 있는 jar 파일을 /app/app.jar 경로에 복사한다.
   COPY ./demo1-0.0.1-SNAPSHOT.jar /app/app.jar

   # 실행 명령으로 jar 파일을 실행한다.
   ENTRYPOINT ["java","-jar","/app/app.jar"]
   ```

1. **nginx 설정**

   ```bash
   http {
       include       /etc/nginx/mime.types;
       default_type  application/octet-stream;
       log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                         '$status $body_bytes_sent "$http_referer" "$request_uri" "$uri"'
                         '"$http_user_agent" "$http_x_forwarded_for"';
       access_log  /var/log/nginx/access.log  main;
       sendfile on;
       keepalive_timeout 65;

       upstream docker-spring {
           least_conn;
           server spring:8080;
       }

       server {
           listen 80;

           location / {
               proxy_pass         http://docker-spring;
               proxy_redirect     off;
               proxy_set_header   Host $host;
               proxy_set_header   X-Real-IP $remote_addr;
               proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header   X-Forwarded-Host $server_name;
           }

       }
   }
   ```

1. **도커 컴포우즈 실행 및 종료**

   ```bash
   **// scale 명령어로 spring 컨테이너 3개 구동
   docker-compose up -d --scale spring=3

   // nginx 서버, spring server 3개 mysql 서버 1개 가 구동중임을 확인
   ubuntu@ip-172-31-12-149:~/SPRING_SERVER$ docker ps
   CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                               NAMES
   b66b611d7638   nginx:latest           "/docker-entrypoint.…"   11 seconds ago   Up 9 seconds    0.0.0.0:80->80/tcp, :::80->80/tcp   proxy
   97957b3baf38   spring_server_spring   "java -jar /app/app.…"   13 seconds ago   Up 10 seconds                                       spring_server_spring_2
   51c714b183c5   spring_server_spring   "java -jar /app/app.…"   13 seconds ago   Up 10 seconds                                       spring_server_spring_3
   752dfe4fdcfc   spring_server_spring   "java -jar /app/app.…"   13 seconds ago   Up 10 seconds                                       spring_server_spring_1
   b44513634413   mysql:5.7              "docker-entrypoint.s…"   14 seconds ago   Up 12 seconds   3306/tcp, 33060/tcp**

   // 종료
   **docker-compose down**

   ```

## 도커 사용법

**도커 로그인**

```bash
docker login
```

**다운 받으려는 이미지 검색**

```bash
#이미지 검색
docker search {image}

#이미지를 특정 tag 로 검색 혹은 latest(가장 최신 버전)으로 검색
docker search {image}:{tag} or docker search {image}:latest

```

**이미지 다운 및 삭제**

```bash
# 이미지 다운
docker pull ubuntu

# 다운 받은 이미지 목록 확인
docker images

# 이미지 id 만 확인
docker image ls -q

# 이미지 삭제
docker rmi {이미지 아이디 || 이미지  저장소 이름}

```

**컨테이너 관련 주요 명령어**

- 이미지는 컨테이너로 만들어줘야 실행이 가능하다
- 이미지와 컨테이너는 각각 관리해야한다
- 컨테이너 생성시, docker 프로그램에서 이름이 자동 부여된다.

```bash
# ububtu 이미지로 컨테이너 생성
docker create ububtu

# 실행중인 컨테이너만 확인
docker ps

# 실행중이지 않은 컨테이너 까지 포함해서 전체 컨테이너를 확인한다.
docker ps -a

# 이 명령어를 여러번 사용하면 같은 우분투라는 이미지로 여러 독립적인 컨테이너를 생성하게 된다.
docker create ububtu

# 컨테이너 id 만 보여주는 명령어이다.
docker ps -a -q

# 컨테이너 삭제
docker rm {삭제할 컨테이너 아이디 || 컨테이너 NAMES}

# 컨테이너의 이름을 지정하면서 컨테이너를 생성할 수 있다.
docker create --name myububtu ububtu

# 컨테이너 실행
docker start {컨테이너 이름}

# 컨테이너 실행시.. 바로 종료된다.
docker start myububtu

위과 같이 우분투를 실행하게되면 실행하자마자 종료된다. 아직 우분트라는 패키지안에 실행되는 프로그램이
없기 때문에 도커가 바로 종료된다.
/bin/bash 에서 배시라는 프로그램이 실행되지만 키보드 입력을 통해서 표준 스트림 중 입력(stdin)
을 받을 수 있는 상태가 아니기 때문에(별도 터미널 및 표준 입력 연결 설정이 없었기 때문에)
실행하자마자 끝나게 된다. 그렇기 때문에 해당 컨테이너는 바로 종료되는 것이다.

# 컨테이너 실행후 해당 ubuntu 로 들어가서, 터미널로 명령을 진행 할 수 있다
docker run -it ububtu

it: 가상터미널을 할당해서 해당 컨테이너에 표준입력을 할 수 있도록 연결하는 옵션
i: 컨테이너에 입력을 열어 놓는다.
t: 가상 터미널을 할당한다
```

**도커 런 주요 명령어**

```bash
# 컨테이너 이름을 원하는 이름으로 변경, ububtu 라는 이미지를 myububtu 라는 컨테이너로 실행한다.
docker run -it --name myububtu ububtu

# 컨테이너 종료하면서 빠져나오는 명령어이다.
exit

# 이경우 exit 할경우 해당 컨테이너 종료와 함께 컨테이너가 삭제된다.
docker run -it --rm --name myububtu ububtu

# 컨테이너를 백그라운드로 실행하고 싶다면
docker run -it -d --name myububtu ububtu

# 그럼 해당 컨테이너에 들어가고 싶다면 attach 명령어를 사용한다
# 즉 백그라운드 실행에서 포그라운드로 전환되는 것이다.
docker attach myububtu

```

**실행중인 도커 컨테이너 종료 및 중지 재개**

```bash
# 컨테이너 중지
docker stop myububtu

# 잠깐 프로세스를 멈추는것
docker pause {컨테이너 아이디}

# 멈췄던 프로세스를 다시 재개하는것
docker unpause {컨테이너 아이디}

# 실행중인 컨테이너를 재시작
docker restart {컨테이너 아이디}

```

**웹서버로 docker run 옵션 테스트**

```bash
# 아파치는 특이하게 이미지 이름이 httpd 이다
docker search httpd

# 너무 많은 이미지가 나오면 힘드니까 원하는 만큼만 보고 싶을 경우
docker search httpd -limit=5

# 이미지 다운받고 바로 실행하는 명령어
# 하지만 백그라운드 옵션을 안썼기때문에 표준 입력출력이 컨테니어있는 아파치에 붙기때문에 명령어가 작동하지 않는다.
docker run httpd

# 그렇다면 컨테이너를 백그라운드로 실행, -d 백그라운드로 실행해라.
docker run -d --name apachweb httpd

```

- 이제 컨테이너를 실행중인데 이건 웹서버이다. 위와 같이 만들어노면 해당 컨테이너를 어떻게 접속할까?
  이때 사용하는 옵션이 p 이다.

도커를 실행한 pc 를 host pc 라고한다. 도커 컨테이너를 실행하면 private 한 ip 가 할당되게 된다.
호스트 pc ip 에서 특정 port 로 access 시, 해당 port 를 docker 컨테이너의 특정 private
ip 의 특정 포트로 변환해줄 수 있다. 이를 NAPT 기술이라고 한다.
이것을 지원해주는 옵션이 바로 p 옵션이다.

```
docker run -d -p 9999:80 --name apacheweb2 httpd
```

![](https://i.imgur.com/8MNkYIs.png)

위와 같이 실행하게되면 apacheweb2 라는 컨테이너는 apach 웹서버 프로그램을 실행하게 되고host pc 에 9999 포트로 접속하게 되면 자동으로 이를 해당 컨테이너의 80 번 포트로 연결해주겠다는의미이다
즉 포트포워딩을 해준다고 생각하면 된다.

**-v 옵션을 활용하기**

```bash
호스트 피시에있는 특정 폴더를 컨테이너에 집어넣기위해서 또는 컨테이너가 삭제되더라고 특정
컨테이너에 있는 폴더는 삭제되지 않도록 내호스트 pc 에있는 폴더를 사용하도록 -v 옵션을 사용한다.

예를 들어 mysql 컨테이너를 실행시킬때 해당 컨테이너가 종료되더라도 데이터가 날라가면안된다.
그렇기 때문에 해당 mysql 데이터를 내 host pc 에 두는 것 이라고 생각하면 된다.

apache 웹서버 기본 설정으로는 /usr/local/apache2/htdocs 폴더의 있는 index.html 를 가르
키게 되어있다. 만약 호스트 pc 상에 나만의 index.html 파일이 있다면 -v 옵션을 사용해
호스트 pc 의 특정 폴더를 docker 컨테이너의 특정 폴더로 교체 할수 있다.

docker run -d -p 9999:80 -v "내호스트 pc 의 index.html 파일이있는 경로":/usr/local/
apache2/htdocs --name apacheweb2 httpd

위와 같이 설정할경우 {내 호스트pc 의 ip}:9999 로 접속하게 되면 내가 만든 index.html 파일에 해당하는 내
용이 보여지게 된다.

```

**도커가 사용하고 있는 저장매체 현황 확인하기**

```bash
# 추후 도커가 사용하는 저장 매체 공간이 이슈가 될 수도 있으므로  위 명령어로 저장공간 사용량을 확인할 수 있다.
docker system df
```

**실행중인 컨테이너 사용 리소스 확인하기.**

```bash
docker container stats
```

**실행중인 컨테이너 안으로 들어가서 명령을 실행하기**

```bash
# exec 는 해당 컨테이너에 신규 명령을 실행하는 명령이고
docker exec -it {컨테이너 아이디 || 컨테이너 이름} /bin/sh(혹은 /bin/bash)

# attach 는 컨테이너와 연결하는 명령어 이다.
docker attach {컨테이너 아이디 || 컨테이너 이름}

```

**모든 컨테이너 삭제하기**

```bash
# docker ps -a -q 는 모든 컨테이너의 아이디만 보여주는 명령어이다
# 이명령어를 활용하여 다음과 같이 모든 컨테이너를 동시에 중지하거나 모든 컨테이너를 삭제할 수 있다.

# 모든 컨테이너 중지
docker stop $(docker ps -a -q)

# 모든 컨테이너 삭제
docker rm $(docker ps -a -q)

# 모든 이미지 삭제
docker rmi -f $(docker images -q)
```

**도커 파일**

- 도커 이미지를 작성할 수 있는 기능이다
- 도커 파일 문법으로 이미지 생성을 위한 스크립트 작성을 할 수 있고 이를 기반으로 이미지를 생성하게된다.
- 도커파일은 텍스트 파일 형식이다 어떠한 에디터로든 작성할 수 있다는 뜻이다.
- 기본적으로 명령과 인자로 이루어 진다
- 도커파일에서도 주석을 사용할 수 있다.

```bash
# 주석가능
```

**주요 도커파일 만들기**

- Dockerfile

```bash
# 베이스 이미지를 먼저 써준다.
# FROM 은 베이스 이미지 지정 명령이고 반드시 Dockerfile에 작성해야하는 명령이다.
FROM alpine
```

**도커파일 빌드하기**

```bash
# tag 명령어로 이미지 이름설정이 가능하다 저장소/이미지이름:태그 와 같이 작성가능하고
# 저장소 이름 및 태그는 작성안해도되며 태그 이름이 없을경우 디폴트로 latest 태그가 붙여진다.
# ./ 은 현재폴더를 의미한다. 도커파일이 어디에있는지를 지정하는
docker build --tag myimage ./

# -f 옵션으로 기본 디폴트 도커파일명인 Dockerfile 가 아닌 다른 도커파일명(Dockerfile2)을 지정할 수 있다.
docker build --tag myimage2 -f Dockerfile2 ./

# 기본적으로 도커파일의 이미지는 새로 다운 받지 않고 다운받은 이미지를 사용한다
# 만약 도커파일내에 FROM 으로 지정된 이미지를 새로 다운 받고 싶다면 --pull=true 와 같이 작성하면된다.
docker build --tag myimage -f Dockerfile --pull=true

```

**도커파일의 LABEL**

- 보통 저자, 버전, 설명, 작성일자들을 key value 형태로 입력하는 기능이다.

```bash
# 베이스 이미지를 먼저 써준다.
# FROM 은 베이스 이미지 지정 명령이고 반드시 Dockerfile에 작성해야하는 명령이다.
FROM alpine

LABEL maintainer="hakjun@naver.com"
LABEL version="1.0.0"
LABEL description="test docker image"

```

**도커파일의 COPY**

```bash
# Dockerfile
-------
FROM httpd:alpine
LABEL maintainer="hakjun@naver.com"
LABEL version="1.0.0"
LABEL description="test docker image"

# 도커 파일 위치기준 상대 경로로 작성해 줘야한다.
# FROM dttpd:alpine 해당 이미지를 기반으로 ./2021_DEV 폴더의 내용들을 추가해서
# 하나의 이미지로 만들겠다는 내용이다.
COPY ./MYFOLDER /usr/local/apache2/htdocs

-------
# 도커 파일 빌드하기
docker build --tag myimage -f Dockerfile

# 이 도커파일로 빌드된 이미지를 실행시키면된다.
docker run -d -p 9999:80 --name apacheweb **myweb**

# 그럼 아래와 같이 실행되는 모습을 볼 수 있다.
CONTAINER ID   IMAGE     COMMAND              CREATED              STATUS              PORTS                                   NAMES
f3c6e9fb8eae   **myweb**     "httpd-foreground"   About a minute ago   Up About a minute   0.0.0.0:9999->80/tcp, :::9999->80/tcp   apacheweb
```

**도커 파일의 이미지 조사하기**

```bash
docker inspect {이미지 이름}

# 컨테이너 또한 조사가 가능하다
docker inspect {컨테이너 이름}
```

**도커의 CMD**

모든 도커는 컨테이너가 실행될때 실행이 되는 프로그램이 있다 이를 설정하는 명령이 CMD 이다.

이 CMD 를 작성하는 방법에는 여려가지가 있다.

그중하나를 알아보자

```bash
# sh 쉘을 이용해서 -c httpd-foreground 명령어를 실행시킨다는 의미이다.
# **만약 위와 같이 CMD 명령어를 추가해주면 기본적으로 설정 되어있던 도커파일 이미지의
# CMD 는 덮어씌어 진다.**
# CMD ["/bin/sh", "-c", "httpd-foreground"]

# 또한 아래와같이 도커 컨테이너 실행시에 CMD 명령어를 쓸수도 있다.
docker run -d -p 9999:80 --name apacheweb myweb /bin/sh -c httpd-foreground

Dockerfile
---
FROM httpd:alpine
LABEL maintainer="hakjun@naver.com"
LABEL version="1.0.0"
LABEL description="test docker image"

# 도커 파일 위치기준 상대 경로로 작성해 줘야한다.
# FROM dttpd:alpine 해당 이미지를 기반으로 ./2021_DEV 폴더의 내용들을 아파치 웹서버에 추가해서
# 하나의 이미지로 만들겠다는 내용이다.
COPY ./2021_DEV /usr/local/apache2/htdocs
CMD ["/bin/sh", "-c", "httpd-foreground"]
----

# 위 도커파일로 빌드된 이미지를 실행시키고
# 다음 이미지 조사 명령어를 실행시키면 **설정해두었던** CMD 를 확인할 수 있다.

docker build --tag myweb ./
docker run -d -p 9999:80 --name apacheweb myweb
docker inspect **myweb

"Cmd": [
                "/bin/sh",
                "-c",
                "httpd-foreground"
            ],**

```

**도커 컨테이너 로그 및 에러 출력확인하는 명령어.**

```bash
# 도커 컨테이너에서 출력결과,에러를 확인하는 명령어이다
docker logs {컨테이너 이름}

# 명령어가 현재 실행중인 단계까지 기다린후 중지한다면
docker stop {컨테이너 이름}

# 즉시 컨테이너를 중지한다.
docker kill {컨테이너 이름}
```

```bash
# 도커 컨테이너에서 출력결과,에러를 확인하는 명령어이다
docker logs {컨테이너 이름}

# 명령어가 현재 실행중인 단계까지 기다린후 중지한다면
docker stop {컨테이너 이름}

# 즉시 컨테이너를 중지한다.
docker kill {컨테이너 이름}
```

**RUN 명령어**

- 도커는 이미지 생성시 각 단계를 layer 로 나누어 작성한다
- 이를 통해 특정 단계 변경시, 전체 이미지를 다시 다운로드 받지 않아도된다.
- RUN 명령어는 이미지 생성시, 일종의 layer 를 만들수 있는 명령으로 보통 베이스 이미지에 패키지 프로그램을 설치하여 새로운 이미지를 만들때 사용한다.

```bash
vi Dockerfile-apache
-----
FROM ubuntu:18.04
LABEL maintainer="hakjun@naver.com"

# RUN 명령어로 각 레이어를 추가한다.
# 패키지 프로그램 정보 업데이트
RUN apt-get update
# apache2 apache-utils 를 추가한다.
RUN apt-get install -y apache2 apache-utils

# apache2 디폴트 웹서버 설정은 /var/www/html/
COPY ./2021_DEV /var/www/html/

# apache2 웹서버 구동 명령은 다음과 같다
ENTRYPOINT ["/usr/sbin/apache2ctl", "D", "FOREGROUD"]

-------

# 빌드한다
docker build --tag myweb -f Dockerfile-apache ./

# 만약 위 도커파일에서 추가로 패키지를 설치한다면?

만약 위  도커파일에서 RUN 명령어로 추가 패키지를 설치한다면 위 빌드과정에서 시간이 많이 줄어드는
것을 확인 할 수 있다. 도커이미지에 layer 를 쌓으면서 이미 다운받은 패키지는 도커 캐시를
통해서 재사용하기 때문이다. 즉 새로 추가된 패키지만 설치하게 된다.

```

**EXPOSE 명령어**

- 도커 컨테이너의 특정 포트를 외부에 오픈하는 설정이다.
  - doker run -p 옵션으로도 설정 할 수 있다.
    - docker run -p 옵션은 컨테이너의 특정 포트를 외부에 오픈하고 해당 포트를 호스트 pc 의 특정 포트와 매핑시키는 것이다.
  - 이에반해 EXPOSE 는 컨테이너 생성시에 특정 포트를 외부에 오픈하는 것 까지만 하는 설정이다
    - 즉 호스트 pc 에서 해당 컨테이너에 포트번호로 접속할 수 있는 것은 아니다.
    - 도커 compose 를 사용할때 컨테이너 끼리 통신할때 사용된다.

```bash
FROM ubuntu:18.04
LABEL maintainer="hakjun@naver.com"

# RUN 명령어로 각 레이어를 추가한다.
# 패키지 프로그램 정보 업데이트
RUN apt-get update
# apache2 apache-utils 를 추가한다.
RUN apt-get install -y apache2 apache-utils

# 특정 포트 외부에 오픈 / 보통 컨테이너 끼리 통신할때 사용한다.
EXPOSE 80

# apache2 디폴트 웹서버 설정은 /var/www/html/
COPY ./2021_DEV /var/www/html/

# apache2 웹서버 구동 명령은 다음과 같다
ENTRYPOINT ["/usr/sbin/apache2ctl", "D", "FOREGROUD"]

```

**ENV 명령어**

- 컨테이너 내부에 환경 변수를 설정하는 명령어
- 설정한 환경변수는 RUN, CMD, ENTRYPOINT 명령에도 적용된다.

```bash
vi Dockerfile-mysql

---
FROM mysql:5.7

# mysql 의 슈퍼관리자 root id 에 대한 password 란에 원하는 패스워드 설정
ENV MYSQL_ROOT=123456

# dbname 란에 원하는 데이터베이스 이름 설정
ENV MYSQL_DATABASE=dbname

# 필요시 추가 설정 적용가능하다.
---

# 도커 이미지 작성
docker build --tag mysqldb -f Dockerfile-mysql

# 도커 컨테이너 실행
docker run -d --name mydb mysqldb

# 컨테이너 접속해서 쉴 실행하기
docker exec -it mydb /bin/bash

# 이미지에서 설정한 패스워드 입력
mysql -u root -p
>> 패스워드 입력

```

**WORKDIR 명령어**

- RUN, CMD, ENTRYPOINT 명령이 실행될 디렉토리 설정

```bash
vi Dockerfile-workdir

---
FROM httpd:alpine

WOEKDIR /usr/local/apache2/htdocs

CMD /bin/cat index.html

---
도커 이미지 작성
docker build --tag mywork -f Dockerfile-workdir ./

도커 컨테이너 실행
docker run -d --name mytest mywork

doker logs mytest 명령어로 실제 /usr/local/apache2/htdocs/index.html
파일이 cat 으로 출력됐는지 확인할 수 있다.

```

**docker cp 몀령어**

- 컨테이너에서 특정 파일을 호스트 pc 로 가져오는 명령이다. 반대로 호스트 pc 에 있는 파일을 컨테이너로 복사도 가능하다.
- 특정 파일 확인을 위해 활용한다.

```bash
# 위에서 작성한 이미지 컨테이너로 구동
docker run -d -p 9999:80 --name  mywebserver --rm myweb

# mywebserver 에 있는 파일 호스트 pc 로 복사하기
# mywebserver 의 /etc/apache2/sites-available/000 경로에 있는 파일을 호스트 피시의 ./ 경로에 복사하겠다.
docker cp mywebserver:/etc/apache2/sites-available/000-default.conf ./

# 결과 확인
ubuntu@ip-172-31-0-78:~$ ls
**000-default.conf**  2021_DEV_HTML  Dockerfile  Dockerfile-ubuntu

```

**Docker Compose 란?**

- Docker Compose 는 여러 컨테이너를 모아서 관리하기 위한 툴이다.
- 웹서비시는 프론트앤드 서버, 데이터베이스서버, 백엔드 서버로 이루어져 있는 경우가 많다.

  각각을 도커 컨테이너로 작성하고 연결하여 동작하기 때문에 Docker Compose 와 같은 컨테이너 관리툴이 필요하다.

  - 더 나아가 서비스 규모가 커지면 복수의 컨테이너를 유지하고 관리해야 하며 이를 위해 쿠버네티스 등의 관리 툴이 사용된다.
  - 도커와 Docker Compose 를 잘다룰 수 있으면, 기본적인 서비스 구현이 가능하며 도커와 Docker Compose 에 대한 탄탄한 이해가 바탕이 되어야 추후 필요시 쿠버네티스도 원할하게 익히고 활용할 수 있다고 한다.

  **도커 컴포우즈의 작성 기본**

  docker-compose.yml 파일을 작성하여 실행하고 실행해야 한다.

  **version**

  - **Docker Compose 파일 포맷 지정**

  ```bash
  version: "3"

  services:
  	db: # 컨테이너 이름 정의
  		image: mysql:5.7 # 도커헙에 있는 이미지를 mysql 이미지를 사용하겠다.
  		restart: alaways # **컨테이너가 다운되었을 경우, 항상 재시작 하라는 설정.
  		volumes:
  			- ./mysqldata:/var/lib/mysql # docker 의 -v 옵션과 동일함,
  		enviroment: # 환경 변수 설정
  			- MYSQL_ROOT_PASSWORD=hakjun
  			- MYSQL_DATABASE=hjdb
  		## 만약 docker-compose 에 데이터 베이스 패스워드, 적는게 불안하다면 다음과같이
  		## 호스트 pc 에 txt 파일을 작성해놓고 띄울 수 있다
  		env_file:
  			- ./mysql.env

  ---------------------
  ex) mysql.env
  MYSQL_ROOT_PASSWORD=hakjun
  MYSQL_DATABASE=hjdb
  ----------------------**
  ```

  **port**

  - docker run 의 -p 옵션과 동일한 역할을 한다.
  - yaml 문법에서 aa:bb 와 같이 작성하면, 시간으로 해석하기 때문에 쌍따움표를 붙여줘야함

  ```bash
  version: "3"

  services:
  	db:
  		image: mysql:5.7
  		restart: alaways
  		**volumes:
  			- ./mysqldata:/var/lib/mysql # docker 의 -v 옵션과 동일함,
  		enviroment:
  			- MYSQL_ROOT_PASSWORD=hakjun
  			- MYSQL_DATABASE=hjdb
  		ports:
  			- "3306:3306" # host pc 의 3306 포트의 요청은 해당 컨테이너 3306 으로 매핑하겠다.**


  ```

  **도커 컴포우즈 실행/중지하기**

  ```
  # 백그라운드로 실행
  docker-compose up -d

  # 컨테이너 중지
  docker-compose stop

  # 컨테이너 삭제
  doccker-compose down
  ```
