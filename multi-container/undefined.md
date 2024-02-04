---
description: 데이터베이스 데이터를 유지하기 위한 볼륨 생성
---

# 데이터베이스 볼륨 추가하기

데이터베이스는 영구적인 데이터를 저장하기 위해선 볼륨을 생성해야한다.

앞서 데이터베이스 컨테이너 중지시 --rm 옵션을 사용하여 컨테이너를 삭제하는데

볼륨을 생성하지 않으면 데이터도 모두 삭제 된다.

또한, 이름이 없는, 바인드 되지 않는 -v 익명 볼륨을 생성시 어디에 데이터가 저장되는지 알기 어렵기때문에

반드시 이름이 있는 볼륨이 필요하고, 해당 폴더에 바인드를 해줘야 한다. \* 바인드 마운트가 아님!

이는 바인드 마운트가 아니고 컨테이너 대신 도커 데몬이 지정한 호스트머신 폴더에 매핑하여 데이터를 저장한다.&#x20;



## 데이터베이스 볼륨 생성

```bash
docker run --name mongodb -v data:/data/db --rm -d --network [네트워크이름] [이미지이름]
# 여기서 볼륨 뒤의 경로는 -v [볼륨이름:/경로/경로]
```

도커는 호스트 머신의 폴더에 이미 있는 데이터를 컨테이너의 폴더로 로드한다. \*데이터가 손실되지 않는다.



## 데이터베이스 보안, 이름과 비밀번호 설정하기

데이터베이스 보안을 위해 사용자 이름과 비밀번호를 환경 설정할 수 있다.

{% embed url="https://hub.docker.com/_/mongo" %}

공식 문서에 "Environment Variables" 확인

### 사용법

\-e MONGO\_INITDB\_ROOT\_USERNAME=

\-e MONGO\_INITDB\_ROOT\_PASSWORD=

설정

```bash
docker run --name mongodb -v data:/data/db --rm -d --network 
-e MONGO_INITDB_ROOT_USERNAME=example
-e MONGO_INITDB_ROOT_PASSWORD=password [네트워크이름] [이미지이름]

```

위와 같이 이름과 비밀번호를 설정할 수 있다.

이름과 비밀번호를 설정하면 node js코드에서 url또한 변경해 줘야한다.

```javascript
//app.js
mongoose.connect(
  'mongodb:/example:password@mongodb:27017/[db 이름]?authSource=admin',
);
```

위처럼 url에 이름과 비밀번호를 설정해야 사용할 수 있다. &#x20;

단! mongo db의 공식문서에 따르면 반드시 url 마지막에 "?authSource=admin'" 쿼리 문을 써야한다.

이걸 사용하지 않으면 연결되지 않는다.

{% hint style="warning" %}
위 코드는 예시이고, 반드시 사용시엔 .env 파일을 사용하자.
{% endhint %}

