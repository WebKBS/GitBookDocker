# yaml 파일 생성

프론트엔드와 백엔드 폴더가있는 최상위 root에 yaml 파일을 생성한다.

docker-compose.yaml

또한, 확장자 명을 yml 이라고 작성해도 된다.

yml을 사용해도 되지만 되도록이면 yaml을 추천한다. 이유는 구글링..!



## version

```yaml
version: 
```

version 앱이나 파일버전을 의미하는게 아니다.

이는 Docker Compose 파일의 버전을 지정하는 것이다.

예) "2", "3", "3.1" 등..

Docker Compose에서 사용되는 YAML 파일은 여러 버전이 있으며, 각 버전은 지원하는 기능 및 문법의 차이가 있을 수 있다.

고로, 예전 버전은 레거시 버전일 수 있어 더이상 지원되지 않을 수 있다. 최신 권장 버전을 사용하자.

{% embed url="https://docs.docker.com/compose/compose-file/" %}

{% hint style="warning" %}
현재 공식문서를 보면 따로 version은 작성하지 않는 걸로 보인다.

version을 작성하지 않으면 항상 최신 버전으로 사용된다.
{% endhint %}



## services

```yaml
services: 
```

Docker Compose 파일에서 정의하는 메인 섹션 중 하나로, 애플리케이션을 구성하는 각 서비스를 정의하는 한다.&#x20;

각 서비스는 컨테이너로 실행될 독립적인 구성 요소를 나타낸다.&#x20;

이 서비스들은 동일한 네트워크에 속하며 서로 통신할 수 있다.

yaml 파일은 들여쓰기를 사용해서 종속성을 표현한다.

탭 키를 사용하면 들여쓰기를 할 수 있다.

```yaml
services: 
  mongodb: 
  backend: 
  frontend: 
```

만약 약 데이터베이스, 백엔드, 프론트엔드 컨테이너를 설정한다고 가정하자. 위처럼 사용할 수 있다.

각 각 정의하고 싶은 이름은 자유다.

services에 위처럼 3가지가 있다면 컨테이너 종류가 3가지가 된다.



### sevices에서 컨테이너 설정

## 데이터베이스 설정

### 이미지 설정

```yaml
services:
  mongodb:
    image: "mongo" # 이미지의 이름이다.
```

### 볼륨 설정

```yaml
services:
  mongodb:
    image: "mongo"
    volumes:
      - "data:/data/db" # [볼륨 이름]:/[경로]
```

터미널에서 -v 를 사용해서 volume을 사용하는것과 동일하다.

volume은 여러개가 될수 있기 때문에 " - " 기호로 정의한다.



다른 컨테이너와 같은 볼륨을 공유하기 위해선 가장 아래 volumes를 설정해야한다.

그리고 mongodb의 볼륨은 이름이 있기 때문에 이름을 설정해줘야한다.

```yaml
services:
  mongodb:
    image: "mongo"
    volumes:
      - "data:/data/db" # [볼륨 이름]:/[경로]

volumes: 
  data: #네임 볼륨 지정해야함
```



environment 환경변수 설정.

터미널에서 -e 부분과 같다. 이전 터미널에서 mongodb의 MONGO\_INITDB\_ROOT\_USERNAME

&#x20;과 MONGO\_INITDB\_ROOT\_PASSWORD 를 하드코딩으로 사용했었는데

```yaml
environment:
  MONGO_INITDB_ROOT_USERNAME: "root"
  MONGO_INITDB_ROOT_PASSWORD: "example"
```

이런 식으로 작성이 가능하나, 환경변수에는 중요한 내용들이 있기때문에 반듯시

.env파일을 만들고 파일을 불러오는 식으로 만드는 것이 보안에 좋다. 환경변수 파일 .gitignore는 필수!

environment를 대신해서 env\_file 을 사용할 수 있다.

```yaml
env_file:
  - ./env/mongo.env
```



데이터 베이스 설정시 최종 yaml 파일

```yaml
services:
  mongodb:
    image: 'mongo'
    volumes:
      - 'data:/data/db'
    env_file:
      - ./env/mongo.env
volumes:
  data: 
```

위 데이터베이스 설정한 yaml 파일을 터미널로 변경하면 이렇게 된다.

```bash
docker run --name mongodb \ # 컨테이너 이름
 --env-file ./env/mongo.env \ # 환경변수
 -v data:/data/db # 볼륨 설정
 --rm \ # 컨테이너 종료시 컨테이너 삭제
 -d # 이 부분은 docker-compost up -d 실행시 적용 (*기본은 attach모드이다)
 mongo # 이미지 이름
```



## 백엔드 설정

### 이미지 만들기

백엔드 설정시 image는 이름을 지정하지 않는다.

mongo에서 지정한 mongo는 mongodb 자체 퍼블릭으로 공유한 데이터베이스 이기때문에 mongo를 사용했다.

하지만 백엔드 사용시에는 image를 Dockerfile을 설정해줘야한다.

여기선 image 대신 build 옵션을 사용한다.

```yaml
backend:
  build: ./backend # 도커 파일이 있는 위치를 설정한다.
```

build 옵션에서는 Docker 파일이 있는 위치를 설정해야한다.

만약 폴더 이름이 현재 루트의 backend 라면, ./backend를 설정해야한다.



또 다른 방법이 있다.

context 와 dockerfile 키를 사용하는 방법

```yaml
backend:
  build:
    context: ./backend
    dockerfile: Dockerfile
```

context는 Docker파일이 위치가 있는 곳을 지정하고 dockerfile은 말그대로 Dockerfile을 사용하겠다는 뜻이된다.

여기서 중요한 점은 context 사용시 Dockerfile에서 COPY를 사용할 때 같은 경로가 일치해야 한다.

```docker
# Dockerfile
# 만약 도커 파일이 ./backend를 사용하고 Dockerfile 내부에서 COPY . . 을 사용할때
COPY . .
```

위 방법은 혹여나 Dockerfile이 다른 중첩 폴더에 있을때, 그 폴더를 엑세스해야하는 경우 context를 통해 다른 폴더 경로 지정으로 사용할 수 있다.

build 키로 이미지를 만들 수 있다.

위 코드를 터미널로 교체하면 아래와 같다.

```bash
docker build -t [이미지 이름] .
```

{% hint style="info" %}
터미널에는 이미지 이름이 있어야 컨테이너를 서로 연결해 줄 수 있지만

yaml 파일을 사용시, docker-compose 사용시에는 하나의 yaml 파일에 설정하여 통합하기 때문에

build 옵션을 사용하고 이미지 이름은 따로 필요하지 않다. 익명 이름을 사용.
{% endhint %}



### 컨테이너 만들기

#### 포트 설정

```yaml
backend:
  build: ./backend
  ports:
    - '80:80' # 백엔드에서 설정한 포트와 일치시킨다.
```

포트는 위와같이 ports: 를 사용하고 아래 " - " 하이픈을 사용한다.

만약 백엔드 포트가 80이라면 80으로 설정해준다.

#### 볼륨 지정은 개별로 설정할 수 있지만, 데이터베이스, 백엔드, 프론트엔드 모두 통합으로 작성할것이라서 가장 아래 network 설정을 해주면된다. 차후 설명.

#### 볼륨 설정

```yaml
backend:
  build: ./backend
  ports:
    - '80:80' 
  volumes:
    - logs:/app/logs # 볼륨 설정은 자유다. 만약 로그를 남기고 싶다면 이와 같이 만들면 된다.
```

만약 로그 데이터를 삭제하지 않고 보관하고 싶을땐 위와같이 볼륨을 설정하면 된다.

logs 라는 이름있는 볼륨이 생성된다.

단 이름 있는 볼륨은 최상위 volume 키에도 추가해야한다.

```yaml
backend:
  build: ./backend
  ports:
    - '80:80' 
  volumes:
    - logs:/app/logs 

volumes: 
  data:
  logs: 
```

data: 는 데이터베이스에서 설정한 네임 볼륨



#### 바인드 마운트

바인드  마운트를 하기 위해선 로컬 컴퓨터 내의 완전한 전체 절대 경로가 필요했다.

하지만 yaml 파일에서는 그럴 필요가 없다.

yaml 파일이 위치한 곳이 최상위 root 폴더라면 그 위치부터 상대 경로를 사용한다.

```yaml
backend:
  build: ./backend
  ports:
    - '80:80' 
  volumes:
    - logs:/app/logs
    - ./backend:/app
    - /app/node_modules # 바인드 마운트 사용시 필요함.

volumes: 
  data:
  logs: 
```

만약 폴더이름이 backend 이면 위 처럼 사용을 한다. yaml 파일과 같은 경로인 ./ 를 사용해서 backend 폴더의 app 폴더를 의미한다.

logs의 네임 볼륨은 최상위 volumes의 키에 추가를 해야했지만

바인드 마운트는 최상위 volumes 키에 추가할 필요가 없다.

바인드 마운트 사용시 개발환경에서 실시간으로 변경사항을 반영하기위해 /app/node\_modules를 추가해줘야한다.

#### 환경변수 설정

환경 변수 설정은 데이터베이스의 환경설정 파일을 가져와서 사용하는 방법과 같다.

```yaml
backend:
  build: ./backend
  ports:
    - '80:80' 
  volumes:
    - logs:/app/logs
    - ./backend:/app
    - /app/node_modules # 바인드 마운트 사용시 필요함.
  env_file:
    - ./env/backend.env

volumes: 
  data:
  logs: 
```



#### depends\_on 설정

depends\_on 은 터미널에서 docker run 실행시 없는 옵션이다. 도커 컴포즈에만 있는 옵션.

이를 사용하는 이유는 하나의 yaml 파일에서 의존하고 있는 여러 컨테이너가 있기때문에 이를 의존성을 연결하기 위해 사용한다.

앞서 데이터베이스 mongo db설정을 먼저하고 데이터베이스가 먼저 생성 된 후에 백엔드 컨테이너를 생성하기 때문에 의존하고 있는 데이터베이스의 컨테이너 이름을 추가하면 된다.

```yaml
backend:
  build: ./backend
  ports:
    - '80:80' 
  volumes:
    - logs:/app/logs
    - ./backend:/app
    - /app/node_modules
  env_file:
    - ./env/backend.env
  depends_on: 
    - mongodb # 데이터베이스 컨테이너 이름을 사용한다.
```

이로 인해 백엔드 컨테이너 설정은 끝났다.

이를 터미널로 작성하면 다음과 같다.

```bash
docker run --name backend \
    -p 80:80
    --env_file ./env/backend.env
    -v logs:/app/logs
    -v /절대경로 바인드마운트 경로/app
    -v /app/node_modules
    --rm
    [이미지이름]
```



## 프론트엔드 설정

### 이미지 만들기

백엔드 설정과 같다.

```yaml
frontend:
  build: ./frontend
```

### 컨테이너 만들기

#### 포트 설정

백엔드 설정과 같다. 사용할 포트를 적어주면 된다.

```yaml
frontend:
  build: ./frontend
  ports:
    - '3000:3000'
```

#### 볼륨 설정

백엔드와 설명은 같다.

```yaml
frontend:
  build: ./frontend
  ports:
    - '3000:3000'
  volumes:
    - ./frontend/src:/app/src
```

#### 대화형 옵션

프론트엔드에서는 대화형 옵션을 사용하기 위해 -it 옵션을 사용한다.

\-it 옵션을 사용하기 위해 다음과 같은 키를 사용해야한다.

1. stdin\_open: true    - 이는  -i 에 해당한다.
2. tty: true     - 이는 -t에 해당한다.

stdin\_open은 개방형 입력 연결이 필요하다는것을 도커에게 알리는 것이다.

tty는 터미널에 연결하기 위함이다.

위 두 옵션은 개방형 표준 입력을 위한 입력 플래그의 조합이다.

아래와 같이 입력한다.

```yaml
frontend:
    build: ./frontend
    ports:
      - '3000:3000'
    volumes:
      - ./frontend/src:/app/src
    stdin_open: true
    tty: true
```

#### depends\_on 설정

프론트엔드와 백엔드를 동시에 서비스한다면 당연히 프론트엔드는 백엔드에 의존성이 있다. 그래서 depends\_on 설정을 해줘야한다. 이하 설명은 백엔드에서 설명한 백엔드와 데이터베이스 설명과 같다.

```yaml
frontend:
  build: ./frontend
  ports:
    - '3000:3000'
  volumes:
    - ./frontend/src:/app/src
  stdin_open: true
  tty: true
  depends_on:
    - backend # 연결할 백엔드 컨테이너 이름
```

이제 프론트엔드 설정까지 완료되었다.

위 프론트엔트 코드를 터미널로 작성하면 이렇게 된다.

```bash
docker run --name frontend \ 
    -v /절대경로/frontend/src:/app/src \
    -p 3000:3000
    -it \
    [이미지 이름]
```



## 최종 적인 파일은 이렇다.&#x20;

위 내용을 전체 작성을 하면 다음과 같다. 연습용이니 참고만..

```yaml
services:
  mongodb:
    image: 'mongo'
    volumes:
      - 'data:/data/db'
    env_file:
      - ./env/mongo.env
  backend:
    build: ./backend
    # build:
    #   context: ./backend
    #   dockerfile: Dockerfile
    ports:
      - '80:80'
    volumes:
      - logs:/app/logs
      - ./backend:/app
      - /app/node_modules # 바인드 마운트 사용시 필요함.
    env_file:
      - ./env/backend.env
    depends_on:
      - mongodb

  frontend:
    build: ./frontend
    ports:
      - '3000:3000'
    volumes:
      - ./frontend/src:/app/src
    stdin_open: true
    tty: true
    depends_on:
      - backend

volumes:
  data:
  logs:
```
