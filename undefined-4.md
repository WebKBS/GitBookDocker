# 네트워크 통신 도커화

(node 기준) node로만 개발하여 HTTP요청을 외부 api와 통신하면 이는 정상적으로 작동한다.

node로 만든 앱을 도커화 하여 이미지로 만든 후 빌드를 하더라도 이는 정상적으로 작동한다.

이미지를 만들때 node를 복사하기때문에 외부 통신에는 전혀 문제가 없다.



## 로컬 호스트와의 통신

만약 node의 코드중 로컬의 데이터베이스와 통신하는 로직이 있다면 이는 문제가 생긴다.

왜냐하면 node의 복사된 이미지는 컨테이너에서 실행중이고, 로컬의 데이터베이스는 로컬에서 실행되기 때문이다. (데이터베이스가 로컬 호스트 localhost:3000으로 사용된 가정)

도커는 컨테이너에서 실행중인 이미지가 로컬 호스트와 네트워크 통신을 하지않는다.

node에 로컬호스트와 통신하기 위한 코드가 있다면 docker run, 컨테이너 실행부터 에러가 난다.



이를 해결하기 위해서 node에 작성된 데이터베이스 url을 수정해줘야 한다.

만약 아래처럼 로컬에서 실행된 mongodb의 url이라고 가정하자.

```javascript
mongoose.connect("mongodb://localhost:27017/fwefwef")
```

위처럼 localhost:27012로 로컬에서 실행된 주소를 아래처럼 변경해주자.

localhost:3000가 아닌 host.docker.internal

```javascript
mongoose.connect("mongodb://host.docker.internal/fwefwef")
```

이는 도커 컨테이너 내부에서 알 수 있는 호스트 머신 ip주소로 변환된다.

이후 docker 이미지를 다시 빌드 한 후 컨테이너를 실행하면 정상적으로 작동 된다.





## 컨테이너와 컨테이너 간의 통신

로컬 호스트의 데이터베이스와 통신하려면 로컬에서 해당 데이터베이스를 설치가 되어있어야 한다.

이를 도커화를 진행함으로 있어서 데이터베이스를 로컬에 설치하지 않고 도커를 사용해서 실행 해보자.



컨테이너와 컨테이너 간의 통신을 하기위해선 node와 데이터베이스 각각 컨테이너를 실행해야한다.

### docker hub에서 해당 데이터베이스 이미지 사용

mongo db예시를 사용한다.

{% embed url="https://hub.docker.com/_/mongo" %}

```bash
docker run -d --name mongodb mongo
```

위 코드를 입력해면 mongo이미지를 받은 후 mongodb이름을 지정하고 detach모드로 컨테이너를 실행한다.

앞서 적은 로컬 데이터베이스와 연결하는 host.docker.internal는 더이상 작동하지 않는다.

왜냐하면 host.docker.internal는 로컬 호스트 머신의 ip 주소를 참조하기 때문이다.

컨테이너간 통신에서는 다른 방법을 사용해야한다.

터미널에 다음과 같이 입력한다.

```bash
docker container inspect [컨테이너 이름]
```

위 코드를 입력하면 해당 컨테이너의 정보를 볼 수 있다.

많은 정보 중에서 "NetworkSettings" 키의 "IPAddress"를 확인한다.

이 IP주소를 node에 작성된 mongo url에 추가해줘야한다.

예시)

```javascript
mongoose.connect("mongodb://172.17.0.2:27017/fwefwef")
```

27017포트는 mongo의 디폴트 포트 주소.

node의 코드를 위와같이 수정후 다시 이미지를 빌드한다.

이를 이용해 컨테이너간의 통신이 가능하다.



## 도커 컨테이너 네트워크 만들기

도커 컨테이너 네트워크는 다중 컨테이너가 있을때 각 컨테이너간의 통신을 허용한다.

docker run 명령어에 --network 옵션을 추가하면 모든 컨테이너를 하나의 동일한 네트워크에 추가할 수 있다.

앞서 말한 도커 컨테이너 정보를 보면서 ip주소를 확인하며 node의 주소 또한 변경하였는데 이를 대신해 network옵션은 ip조회 작업을 자동으로 수행한다.



### 도커 네트워크 만들기

도커는 자동으로 볼륨을 생성하지 않는다. 네트워크를 사용해서 컨테이너를 실행하려면 직접 컨테이너를 만들어야한다.



docker network create를 사용해서 도커 네트워크를 생성한다.

```bash
docker network create [네트워크 이름]
```

도커 네트워크를 생성하고 생성된 네트워크를 확인할 수 있다.

```bash
docker network ls
```

생성된 네트워크를 볼 수 있으며, 처음 생성이라면 기본 default의 네트워크도 있는것을 확인할 수 있다.



이제 데이터베이스를 네트워크와 연결시키자.

```bash
docker run -d --name mongodb --network [네트워크 이름] mongo
```

위 코드를 입력하면 몽고디비가 네트워크에 연결되고 컨테이너가 실행된다.



이제 node에서 mongoose로 연결할 url주소를 다시 바꿔줘야한다.

```javascript
mongoose.connect("mongodb://mongodb:27017/fwefwef")
```

{% hint style="info" %}
여기서 mongodb문자열은 컨테이너 생성시에 만든 컨테이너의 이름이다.

```bash
docker run -d --name [컨테이너이름] --network [네트워크 이름] mongo
```

컨테이너 이름이 27017 포트앞의 아이피 주소이다.

만약 이름을 지정하지 않았다면 자동으로 생성된 이름을 넣어주면 된다.
{% endhint %}

데이터베이스 컨테이너를 설정했으니 이제 node의 컨테이너도 다시 설정한다.

```bash
docker run --name [컨테이너이름] -d --rm -p 3000:3000 --network [네트워크 이름] [이미지]
```

이 방법을 사용하면 같은 네트워크를 바라보는 컨테이너가 자동으로 ip주소로 변환된다.

{% hint style="info" %}
node 컨테이너 실행시에도 반드시 같은 네트워크 이름을 사용해야 한다.
{% endhint %}

이제 도커 네트워크를 사용해서 네트워크 간의 통신이 가능해진다.



{% hint style="warning" %}
다운받은 데이터베이스 이미지를 컨테이너 생성시 -p (publish) 옵션이 없는 이유는?

\-p, publish 옵션은 로컬호스트나, 컨테이너 네트워크 외부에서 그 컨테이너의 무언가 연결할 계획인 경우에만 필요하다.

컨테이너간 연결이 있으면 포트를 게시할 필요가 없다. 어떻게 보면 중요한 데이터베이스를 포트를 설정해서 외부에 노출할 이상한 경우가 생길수 있기때문,\
publish를 사용해 포트를 설정하는것은 외부의 노출이 필요할때만 해야한다.
{% endhint %}

{% hint style="warning" %}
데이터베이스 컨테이너 생성 후 삭제시 모든 데이터도 함께 삭제된다. 데이터를 유지하기 위해서는 반드시 볼륨이 필요하다.
{% endhint %}







