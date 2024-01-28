---
description: 도커 볼륨에 대해서 알아보자.
---

# Docker Volume

앞서 컨테이너에 대해서 이야기를 했다.

컨테이너는 종료시 컨테이너가 가지고 있는 모든 데이터를 제거하지만

볼륨의 특이성은 컨테이너가 종료되어도 데이터를 지속적으로 가지고 있다.

볼륨은 호스트와 데이터를 영구적으로 저장하기 위한 메커니즘이다.



도커 볼륨은 두가지 데이터 저장 메커니즘을 가지고있다.

볼륨과 바인드 마운트



## Volume

볼륨엔 익명(Anonymous) 볼륨과 네임(named) 볼륨이 있다.



### 익명 볼륨 생성

Dockerfile에서 아래와같이 작성 하면 익명 볼륨이 된다.

```bash
VOLUME [ "/app/경로" ]
```

Dockerfile에 위와같이 입력하고 build후 run을 하면 익명 볼륨이 생긴다.



볼륨에 이름을 지정하지 않으면 익명 볼륨이 된다.&#x20;

익명 볼륨은 호스트의 어딘가에 저장된다.

호스트 어딘가에 저장된 볼륨을 보기 위해서는 터미널에서 아래와 같은 명령어를 입력한다.

```bash
docker volume ls
```

아래와 같은 임의의 값의 이름인 정보가 나온다. (예시)

```
DRIVER    VOLUME NAME
local     215691a323208be.....
```

Dockerfile에서 VOLUME을 이용해 경로로 지정하더라도 호스트 머신의 생성된 어딘가의 폴더에 매핑된다.

컴퓨터내 어딘가에 숨겨져있으며 액세스를 할 수 없다.

{% hint style="info" %}
익명 볼륨의 특이점은 컨테이너가 --rm으로 종료(제거)되면 익명으로 생성된 볼륨은 사라진다!
{% endhint %}

{% hint style="warning" %}
만약 컨테이너 종료시 --rm으로 종료(제거)되지 않고 일반 stop으로 종료 된다면 익명 볼륨은 제거 되지 않는다. 익명 볼륨 생성은 Dockerfile에 있기때문에 이미지가 다시 생성되고 컨테이너가 실행될때마다 익명 볼륨은 새로운 익명 볼륨을 생성한다.

즉, 익명 볼륨이 쌓이게 된다.
{% endhint %}

### 익명 볼륨 삭제

```bash
docker volume rm [볼륨 이름]
```



만약 남아있는 모든 익명 볼륨을 삭제할때.

단, 컨테이너와 연결되어 있는 볼륨은 삭제되지 않는다.

```bash
docker volume prune
```

> 익명 볼륨은 하나의 특정 컨테이너에만 연결된다.



### 네임 볼륨 생성

네임 볼륨을 사용하면 컨테이너가 종료되더라도 그대로 유지된다. 다시 컨테이너가 실행되면 볼륨과 폴더가 복구된다.&#x20;

다시 사용 가능하다. 즉, 영구적인 데이터나 편집하거나 삭제되어선 안되는 중요한 데이터에 적합하다.

{% hint style="warning" %}
네임볼륨은 Dockerfile에서 설정이 불가능하다!\
\
\- Dockerfile\
VOLUME \[ "/app/경로" ]  - 불가능!
{% endhint %}

Dockerfile은 누군가 볼 수 있으며, 만약 중요한 데이터의 저장되는 경로 위치를 알려주는것은 말이 안되기때문.



네임 볼륨은 터미널에서 작성이 가능하다.

```bash
docker run -v [my_named_volume:/경로/경로] [이미지]
```



컨테이너 생성 전체 예시)

```bash
# 이 부분은 컨테이너 생성 전체 예시
docker run -d -p 3000:80 --rm --name [컨테이너 이름] -v [볼륨이름:/경로/경로] [이미지]
```



데이터를 저장한 뒤, 컨테이너를 종료후&#x20;

docker volume ls를 해보면 볼륨이 여전히 존재하는것을 확인할 수있다.

{% hint style="info" %}
만약 다시 컨테이너를 생성해서 볼륨과 연결하려면 이전 볼륨의 이름과 경로가 일치 해야한다!\
일치하지 않으면 데이터를 가져올수 없음.
{% endhint %}


