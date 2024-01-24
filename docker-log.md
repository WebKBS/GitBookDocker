# Docker log

Docker 컨테이너의 로그는 컨테이너가 실행 중인 동안 발생한 이벤트, 출력, 오류 등의 정보를 기록한 것을 볼 수 있다.

```bash
docker logs [옵션] [컨테이너_ID 또는 컨테이너_이름]
```



옵션에 -f 또는 -follow를 입력하면 다시 실시간으로 기록된 내용을 확인할 수 있다.

```bash
docker logs -f 컨테이너_ID
# 또는
docker logs --follow 컨테이너_ID
```



또한 기록된 시간을 보려면 다음과 같은 옵션을 사용한다.

```bash
docker logs -t 컨테이너_ID
# 또는
docker logs -timestamps 컨테이너_ID
```

