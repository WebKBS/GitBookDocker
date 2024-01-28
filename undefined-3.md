---
description: 도커에서 환경변수 사용하기
---

# 환경변수

## Dockerfile 에서 설정

```docker
ENV MY_VARIABLE=my_value
# 또는
ENV PORT 80
```

위와 같은 방법으로 도커파일에서 사용할 수 있다.

만약 EXPOSE를 통해 값을 불러오는 방법

```docker
ENV PORT=80

EXPOSE $PORT
```

같은 도커 파일 내에서 사용하려면 $ 기호를 붙여서 사용할 수 있다.



## 컨테이너 실행시 사용방법

터미널에서 아래와 같이 사용할 수 있다.

```bash
docker run --env MY_VARIABLE=my_value my_image
```

예시)

```bash
docker run --env PORT=8000 my_image
```

\--env 는 -e 로 단순하게 작성할 수 있다.



## .env 파일에서 가져오는 방법

.env 파일

```
PORT=8000
```

터미널에서 실행

```bash
docker run --env-file ./.env my_image
```

도커 컨테이너 실행시 .env 파일을 읽어서 가져온다.



{% hint style="warning" %}
Dockerfile에 직접 환경변수를 작성하는것은 위험할 수 있다.
{% endhint %}

Dockerfile은 외부에 노출될 위험이 있으므로 Dockerfile내에 중요한 키 같은것을 직접 추가하는것은 위험할 수 있다.

고로 환경변수에서 가져와서 사용하는 방법을 택하도록 하자.



### 인수 사용하기

인수란, 도커 이미지를 빌드하거나 컨테이너를 실행하는 데 사용되는 매개변수이다.

ARG라는 인수를 사용해서 이름을 지정할 수 있다.

```docker
ARG DEFAULT_PORT=80

ENV PORT $DEFAULT_PORT
```

