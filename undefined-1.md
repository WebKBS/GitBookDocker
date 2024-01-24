---
description: detached 모드와 attached모드
---

# 대화형

```bash
docker run -p [호스트의 포트]:[컨테이너의 포트] [이미지_이름]
```

도커 run을 사용하면 기본적으로 attacted(연결) 모드가 실행된다.

attached 모드를 사용하면 터미널이 실행된 상태에서 동작되며, 백그라운드 콘솔로그 같은 내용을 실시간으로 확인 할 수있다.

만약 detached 모드를 사용하려면 다음과같이 옵션을 추가한다.

```bash
docker run -p [호스트의 포트]:[컨테이너의 포트] -d [이미지_이름]

# build된 이미지가 있다면,
docker run -d [이미지_이름]
# 또는
docker run --detach [이미지_이름]
```



docker start를 사용할때.

```bash
docker start [옵션] [컨테이너_ID 또는 컨테이너_이름]
```

docker start를 사용하면 기본적으로 detached 모드가 실행된다. 터미널이 실행된 상태가 아니다.

만약 attacted 모드를 사용하고 싶다면 옵션을 추가하면된다.

```bash
docker start -a [컨테이너_ID 또는 컨테이너_이름]
# 또는
docker start --attach [컨테이너_ID 또는 컨테이너_이름]
```
