---
description: 다중 컨테이너를 한 네트워크로 연결해보자.
---

# multi-container 네트워크

도커 네트워크를 설정하는 이유는 각 연결된 서비스를 각각 포트를 지정해서 실행할 필요가 없다.

컨테이너가 동일한 네트워크에 있으면 서로 통신이 가능하기 때문이다.

하나의 서비스가 있는게 아니라 여러개의 서비스가 있다면 각 서비스마다 네트워크를 설정해서 연결되어야 한다.



## 도커 네트워크 생성

```bash
docker network create [네트워크 이름]
```

아래는 생성된 네트워크를 확인할 수 있다.

```bash
docker network ls
```



{% hint style="info" %}
이미지가 생성되어 있다는 전제하에 작성
{% endhint %}

## 데이터베이스 네트워크 연결

```bash
docker run --name mongodb --rm -d --network [네트워크 이름] [이미지 이름]
```



## Node js 네트워크 연결

{% hint style="warning" %}
Node js는 이전에 node 파일에서 데이터베이스 연결시 host.docker.internal 을 사용하여 연결을 했다.

이는 네트워크를 따로 연결하지 않아서 도커 자체의 임의로 생성된 로컬 호스트의 IP를 참조 하기때문에

만약 네트워크 설정을 한다면 host.docker.internal의 주소를 변경해 줘야한다.

host.docker.internal을 생성한 도커 데이터베이스 컨테이너의 이름으로 변경해줘야한다.
{% endhint %}

1. node js 데이터베이스 연결 url 변경

코드 예시)

```javascript
// 도커 네트워크를 설정하지 않은 이전의 url
mongoose.connect(
  'mongodb://host.docker.internal:27017/course-goals',
);

// 위 코드를 도커네트워크 이름으로 변경해야한다.
mongoose.connect(
  'mongodb://[설정한 도커 데이터베이스 컨테이너 이름]:27017/course-goals',
);
```

위 설정을 변경한 후 다시 이미지를 빌드해 줘야한다.

2. 이미지 다시 빌드

```bash
docker build -t [이미지 이름] .
```

3. 도커 컨테이너 네트워크 연결 생성

```bash
docker run --name [컨테이너이름] --rm -d -p 80:80 --network [네트워크이름] [이미지이름]
```

프론트엔드오 통신을 주고 받기 위해선 port가 필요하다.



## 프론트엔드 네트워크 연결

{% hint style="warning" %}
node는 데이터베이스와 연결하기위해 url을 데이터베이스 컨테이너 이름으로 변경하였다.

하지만 프론트엔드 fetch 사용시 url은 로컬호스트를 사용한다. 프론트엔드는 브라우저가 이해하는 코드로 작성해야 하기때문에 localhost를 사용해야한다.
{% endhint %}

1. 도커 컨테이너 네트워크 연결 생성

{% hint style="info" %}
프론트엔드의 컨테이너 설정은 port를 사용해서 외부에 공유를 할 수 있어야한다. (만약 public으로 공개를 원할경우)

백엔드 node와는 다르게 포트의 설정도 필요하다.
{% endhint %}

2. 프론트엔드 네트워크 연결은 필요하지 않을 수 있다.

```bash
docker run --name [컨테이너이름] --rm -d -p 3000:3000 [이미지이름]
# 경우에 따라 포트 번호는 변경될 수 있음.

# node와 같이 -d 옵션을 제외하고 -it 옵션을 추가하여 실시간 터미널 확인 가능
```

