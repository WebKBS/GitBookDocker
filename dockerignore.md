---
description: 복사되는 파일을 제한하기.
---

# dockerignore

Dockerfile에서 COPY . . 을 사용하는 것은 모든 것을 복사하는 것을 말한다.

dockerignore를 사용해서 복사되는 내용을 제한 해보자.



.dockerignore를 사용하면 이미지 빌드시 불필요한 파일을 제외하여 속도를 향상시킬 수 있다.



## .dockerignore 파일 생성

gitignore를 사용해봤다면 익숙할것이다. docker는 이와 비슷하게 .dockerignore 파일을 생성해야한다.



next js의 예시

```ignore
node_modules/
.next/
out/
*.log
*.pid
.idea
.DS_Store
.env
.env.local
.env.development.local
.env.test.local
.env.production.local
coverage/
```

만약 환경변수가있다면 환경변수 .env 파일을 설정할 수 있을것이다.

불필요한 파일의 상황에 맞춰 ignore 를 해주면 된다.



예를 들어 node\_modules는 node js의 패키지 매니저에 의해 관리되므로 굳이 도커 빌드시 필요가 없다.

이미 Dockerfile 이미지 빌드시 RUN npm install을 통하여 생성된다.

node\_modules는 파일이 매우 크기 때문에 ignore를 해주는것이 이미지 크기를 줄이고 빌드 속도 향상에 큰 도움을 준다.
