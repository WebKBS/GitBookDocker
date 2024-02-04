---
description: 백엔드 컨테이너의 로그파일을 저장해보자.
---

# 컨테이너 로그파일 저장

네임 볼륨을 사용해서 볼륨을 생성하고 폴더를 지정하여 백엔드 컨테이너에서 일어나는 로그를 파일로 저장해보는 방법.



## 컨테이너 실행시 볼륨 설정

로컬 폴더에 파일을 저장하기 위해선

node file system이 구성되어야 하고, 바인드 마운트가 필요하다.



로컬 컴퓨터의 실제 경로 루트를 복사하여 지정해줘야한다.&#x20;

vscode 기준 최상위 root폴더의 파일하나를 우클릭해서 copy path를 하면 실제 해당 경로를 복사 할수 있다.

```bash
# mac os 기준
/Users/내맥북이름/git/dockerStudy/multi-container/backend/app.js
# 끝은 파일이름이기에 파일 이름은 삭제한다.

# 필요한 최종 경로
/Users/내맥북이름/git/dockerStudy/multi-container/backend
```

위 path가 바인드 마운트에 필요한 실제 루트경로이다.



앞서 Bind mount에서 배웠듯이 도커는 로컬 파일을 덮어쓰지 않는다. 그래서 실제 로컬 환경과 같게 구축 해야한다.

node 실행시 node\_modules 가 필요하기 때문에 -v 으로 node\_modules를 추가해줘야한다.

```bash
docker run --name [컨테이너이름] --rm -p 80:80 -v app/node_modules 
-v [실제 루트경로]:/app 
-v [볼륨이름]:/[경로] --network 
-v app/node_modules
[네트워크이름] [이미지이름]
```

바인드 마운트에 대한 설정은 끝났다.



하지만 확인 해야할 것이 분명히 있어야한다.

만약 node의 Dockerfile이  아래와 같이 설정되어있다면 이는 바꿔야한다.

```docker
FROM node

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 80

CMD ["node", "app.js"]
```

위 코드는 CMD 실행시 node app.js 한번만 실행되기때문에 logs 파일에 log가 저장되지 않는다.

파일의 데이터가 변경되면 실시간으로 변경해줄 nodemon 같은것을 사용해야한다. (실시간으로 감지)

node 에서 nodemon 패키지를 설정하고&#x20;

패키지 설정에서 script, devDependencies 설정을 변경해준다. (이미 사용하고있다면 무관)

도커 파일을 아래와 같이 다시 변경하고

```docker
FROM node

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 80

CMD ["npm", "start"]
```

이미지를 다시 빌드하고 컨테이너를 다시 실행해준다.

