# Docker Image

## 이미지는 읽기 전용이다.

* 도커 이미지는 읽기 전용이라 다시 빌드 한다고해서 변경사항이 적용되지 않는다.

변경된 내용을 적용하려면 새로 빌드를 해야한다.

```bash
docker build .
```



## 도커의 캐싱기능.

도커는 기본적으로 캐시기능을 사용한다.

도커는 모든 명령과 결과를 캐시하고, 이미지를 다시 빌드할때 명령을 다시 실행할 필요가 없으면 캐시된 값을 사용한다.

만약 변경된 내용이 있다면 변경된 파일을 감지해 파일을 다시 복사한다.



도커의 캐싱기능을 이용해 Dockerfile을 수정하고 최적화를 해보자.

```docker
# 노드 이미지를 기반으로 한다.
FROM node

# 작업 디렉토리를 /app으로 설정한다.
WORKDIR /app

# package.json 파일만 복사하는 이유는 package.json이 변경되면 npm install 명령어를 다시 실행하기 위해서이다.
COPY package.json /app

# package.json 파일이 변경사항이 없다면 npm install은 캐시된 값을 사용한다.
RUN npm install

COPY . /app

# 컨테이너에서 사용할 포트를 노출한다.
EXPOSE 80

# 컨테이너가 시작될 때 실행되는 기본 명령어를 설정한다.
CMD ["node", "server.js"]
```

COPY package.json /app을 먼저 배치하고 이후 RUN npm install을 사용해서

package.json의 변경사항이 없다면 캐싱기능을 이용해 더욱 빠르게 빌드 할 수 있다.

{% hint style="info" %}
package.json파일은 npm install 시 매우 무겁기 때문에 캐싱기능을 활용하는것이 좋다.
{% endhint %}

![](<.gitbook/assets/스크린샷 2024-01-24 시간 16.52.03.png>)

캐시된 내용을 볼 수 있다.



## 도커 이미지 정보 보기

```bash
docker image inspect [옵션] [이미지_이름 또는 이미지_ID]
```

해당 이미지의 상세 정보를  볼수 있다.
