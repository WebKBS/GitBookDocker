# AWS EC2 도커 이미지 사용

도커 허브에 이미지를 push 했다면

AWS EC2에서 이미지를 사용해보자.



이미지 사용은 제법 간단하다.

public으로 docker hub에 공개했으니 공개된 이미지는 가져다 쓰면 된다.

바로 컨테이너를 만든다.

```bash
# aws 로그인 된 터미널창에서
docker run -d --rm -p 80:80 [저장소 아이디]/[저장소 이름]
# 옵션에 대한 내용은 생략 예시임.
# 실행 실패한다면 sudo를 붙여주자
sudo docker run -d --rm -p 80:80 [저장소 아이디]/[저장소 이름]
```

위와 같이 입력하면 컨테이너가 실행되는것을 확인할 수 있다.

```bash
# 실행된 도커 컨테이너 확인
docker ps
# 만약 에러메세지가 있으면 sudo 추가
sudo docker ps
```

실행된 컨테이너를 확인할 수 있다.

