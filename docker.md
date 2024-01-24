# Docker파일 생성

```
Dockerfile
```

확장자를 제외한 Dockerfile을 생성한다.

{% hint style="info" %}
visualstudio에서 Docker extension을 설치하자.
{% endhint %}



일반적으로 FROM으로 시작한다.

base image를 설정한다. 예) node

```docker
FROM node
```



작업 디렉토리 설정

```docker
# 작업 디렉토리를 /app으로 설정한다.
WORKDIR /app
```



패키지 설치

```docker
# npm install 명령어를 실행하여 필요한 Node.js 패키지를 설치한다.
RUN npm install
```



포트 설정

```docker
# 컨테이너에서 사용할 포트를 노출한다.
EXPOSE 80
```



```docker
# 컨테이너가 시작될 때 실행되는 기본 명령어를 설정한다.
CMD ["node", "server.js"]
```



Docker 빌드 (이미지를 구축한다.)

```sh
# bash
docker build .
```



Docker run

```sh
docker run -p [호스트의 포트]:[컨테이너의 포트] [이미지_이름]
```

\-p 는 publish를 뜻한다. 이 명령어를 입력해야 node 서버와 연결된다. 소위 \*포트 포워딩



docker stop

실행중인 도커를 중지할때 사용.

```sh
docker stop [실행중인 이미지 이름]
```



docker start

중지된 Docker 컨테이너를 다시 시작하는 데 사용된다.

```bash
docker start [옵션] [컨테이너_ID 또는 컨테이너_이름]
```
