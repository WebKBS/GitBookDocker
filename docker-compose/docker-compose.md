---
description: docker compose 실행 및 중지 방법
---

# Docker Compose 실행 및 중지

## Compose 실행

yaml 파일을 모두 작성했다면 서비스를 실행하기 위해서

docker compose 실행을 해야한다.

아래와 같이 입력한다.

```bash
docker-compose up
```

docker compose가 실행된다.

이는 attach모드로 실행되며, 만약 detach모드로 실행하고 싶다면 -d 옵션을 붙여 줘야한다.

```bash
docker-compose up -d
```

{% hint style="info" %}
실행된 컨테이너 이름은 해당 프로젝트 폴더의 이름이 붙는다.

만약 내 프로젝트 폴더 이름이 compose라면,

```bash
✔ Network compose_default      Created
✔ Volume "compose_data"        Created
✔ Container compose-mongodb-1  Created 
```

이런 식으로 폴더이름이 앞에 붙는다.
{% endhint %}



## Compose 중지

docker compose는 터미널에서 컨트롤 + C로 중지 할 수 있다.

하지만 이는 말그대로 중지 Stop 된다. 즉, 컨테이너가 그대로 남아 있게된다.



```bash
docker-compose down
```

docker-compose down을 사용하면 사용된 모든 컨테이너가 삭제되고 생성된 네트워크와 모든것이 종료된다.

컨테이너가 삭제되고 다른 모든것들이 종료되지만, 설정된 volume은 삭제되지 않는다.

볼륨은 항상 남아있게된다.

만약 필요없는 볼륨까지 모두 삭제를 원한다면 -v 옵션을 사용해야한다.

```bash
docker-compose down -v
```

{% hint style="danger" %}
만약 서비스 중인 상태라면 볼륨에 중요한 데이터가 남아 있을수 있으니 볼륨을 삭제하는것은 항상 조심하자. 모든 데이터가 삭제된다.
{% endhint %}



## 이미지 re-build 하기

```bash
docker-compose up --build
```

도커 이미지를 모두 리빌드한다.

예시로)

이미 이미지가 모두 실행되고 컨테이너 실행시  프론트 package.json 버전이 낮아서 버전을 다시 변경후 이미지를 다시 빌드해야 할때가 있다.&#x20;

이럴때 이미 빌드된 이미지를 전체 삭제하고 다시 compose up을 하는 대신 즉시 다시 리 빌드할 때 사용된다.
