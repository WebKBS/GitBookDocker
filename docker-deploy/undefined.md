# 도커 허브 이용

{% embed url="https://hub.docker.com/" %}

도커 허브에 접속 한 후 저장소를 만든다.

{% hint style="info" %}
연습용이니 public을 이용한다. private 사용시 1개만 가능하고 이후는 결제가 필요하다.
{% endhint %}



프로젝트에서 새로운 터미널을 생성한다. ( aws ec2 사용중인 터미널 제외 )

{% hint style="info" %}
.dockerignore 파일이 없다면 생성해서 도커 허브 저장소에 업로드 되는것을 제외하자. public이므로 누구나 이미지를 받아 갈수 있으니 주의!

```ignore
node_modules
Dockerfile
*.pem
```

.pem 파일 특히 주의!
{% endhint %}

## docker 이미지 생성

```bash
docker build -t [이미지이름] .
```



## docker hub push

```bash
docker push [저장소아이디]/[저장소 이름]:[태그]
```

### 태그 이름 변경하는 방법

```bash
docker tag [변경할 이미지] [저장소아이디]/[저장소 이름]
```

{% hint style="info" %}
만약 push가 실패한다면 로그인을 해줘야한다.

```bash
docker login
```

login 성공시 Login Succeded 확인

다시 push를 해준다.
{% endhint %}

성공적으로 도커 허브 저장소에 업로드 된것을 확인해보자.



