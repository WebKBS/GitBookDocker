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

#### 이미지 설정

```yaml
services:
  mongodb:
    image: "mongo" # 이미지의 이름이다.
  backend:
  frontend: 
```

#### 볼륨 설정

```yaml
services:
  mongodb:
    image: "mongo"
    volumes:
      - "data:/data/db" # [볼륨 이름]:/[경로]
    environment:
      
  backend:
  frontend: 
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
    environment:
      
  backend:
  frontend: 

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

