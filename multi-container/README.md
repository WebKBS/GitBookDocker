---
description: 다중 컨테이너를 만들어보자!
---

# multi-container

웹을 개발할때 필요한 것이 3가지가 있다.

프론트엔드, 백엔드, 데이터베이스.

물론 백엔드가 필요없고, 데이터베이스만 필요한 경우, 프론트엔드만 필요할 수도 있으며,&#x20;

백엔드에서 api만 서로 주고받는 형식이라면 프론트엔드가 필요 없을 수도 있다.



하지만 3가지 다 쓴다고 전제, 가정하에 다중 도커 컨테이너를 만들어서

프론트엔드, 백엔드, 데이터 베이스 이 세 컨테이너를 연결하여 하나의 서비스처럼 만들어보자.



데이터베이스는 MongoDB를 사용.

## 데이터베이스 컨테이너 만들기

```bash
docker run --name mongodb --rm -d -p 27017:27017 mongo
```

이 코드는 --rm 으로 컨테이너 중지시 mongodb의 컨테이너는 삭제된다.

볼륨을 만들지 않았기때문에 데이터도 함께 삭제된다.

\-p (publish) 옵션을 사용하여 mongodb의 포트를 만들고 이를 로컬에서 실행중인 node를 연결하여 로컬에서 사용할 수 있다.



## Node js 컨테이너 만들기

1. 도커 파일 생성

```docker
FROM node

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 80

CMD ["node", "app.js"]
```

2. 도커 이미지 만들기

```bash
docker build -t [이미지 이름] .
```

{% hint style="info" %}
만약 사용하지 않는 이미지를 한번에 지우고 싶다면 아래 코드를 입력한다.

```bash
docker image prune -a
```
{% endhint %}

3. 도커 컨테이너 만들기

```bash
docker run --name [이름] --rm -d -p 80:80 [이미지 이름]
```

포트 번호는 설정한데로 사용.

{% hint style="warning" %}
만약 컨테이너 실행에 에러(데이터베이스 연결 실패)가 발생한다면 node의 데이터베이스 연결할 코드에 url을 확인하자. localhost로 되어있다면 실패할 수도 있다. 앞서 설명한 이전 페이지 "네트워크 통신"에 적은 host.docker.internal을 사용하자.
{% endhint %}



## React 컨테이너 만들기.

프론트엔드는 React로 설명.

1. 도커 파일 생성

```docker
FROM node

WORKDIR /app

COPY package.json ./

RUN npm install

COPY . .

RUN npm run build

EXPOSE 3000

CMD ["npm", "start"]
```

2. 도커 이미지 생성

```bash
docker build -t [이미지 이름] .
```

3. 도커 컨테이너 생성

```bash
docker run --name [이름] --rm -d -p 3000:3000 [이미지 이름]
```



전체 도커 컨테이너 연결을 완료하였다.

detach 모드로 실행했으며, docker logs \[컨테이너이름] 으로 실행중인 컨테이너의 로그를 확인할 수 있다.

{% hint style="info" %}
만약 react실행시 실행 환경을 확인하려면 detach모드를 지우고 -it 옵션을 사용한다.

그러면 리액트 실행 환경을 터미널에서 확인할 수 있다.

```bash
docker run --name [이름] --rm -p 3000:3000 -it [이미지 이름]
```
{% endhint %}



다음은 도커 네트워크를 사용하여 프론트엔드, 백엔드, 데이터베이스를 하나의 도커 네트워크에서 사용해보자.

