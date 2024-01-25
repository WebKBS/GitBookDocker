---
description: docker image를 공유해보자.
---

# 이미지 공유하기

## 도커 허브 사용하기

{% embed url="https://hub.docker.com/" %}

{% hint style="info" %}
도커 허브는 public으로 사용시 무료로 사용할 수 있으나, 1개의 저장소만 private로 가능하고 이후엔 월 5달러의 추가 요금이 필요하다.

즉, 2개 이상의 비공개 저장소를 사용하려면 유료.
{% endhint %}

도커 허브를 가입 한후 저장소를 만들어준다.



저장소를 생성하고 나면 우측에 **Docker commands** 가 있다.

도커허브에 push하는 코드이다.&#x20;

```bash
docker push [가입시이름]/[저장소이름]:tagname
```

아마 이렇게 나올 것이다.&#x20;

tagname은 옵션이며 사용하지 않는다면 무시해도된다.



\*도커 허브에 push를 하기위해서는 로컬 도커 이미지의 이름과 일치해야한다.

가입시이름 및 저장소의 이름과 일치하지않으면 push가 되지 않는다.

고로 로컬 docker에서 다시 이름을 지정해 build를 해준다.

```bash
docker build -t [가입시이름]/[저장소이름] .
```

이후 docker 푸시를하면 저장소에 이미지가 추가되는것을 볼 수 있다.



## 푸시하기전에 로컬 로그인부터

```bash
docker login
```

위 코드를 입력하면 Username이 나온다. 아이디와 비밀번호를 입력하면 login이 된다.

{% hint style="info" %}
로그인을 하지 않은 상태에서는 docker push가 되지 않는다.
{% endhint %}



간략히 예시.

가입시이름: examplename,

저장소이름: examplerepo

도커 저장소에 commands를 보면&#x20;

```bash
docker push examplename/examplerepo:tagname
```

코드를 볼수 있다.

로컬에서 저장소에 push할 이미지를 다시 이름에 맞춰 빌드해준다.

```bash
 docker build -t examplename/examplerepo .
```

이후 다시 push를 해준다.

```
docker push examplename/examplerepo
```



## 태그를 설정하지 않았다면?

태그를 설정하지 않으면 기본적으로 latest가 붙는다.

위 처럼 태그를 설정하지 않으면 default인 latest로 push가 된다.

만약 태그가 있다면 반드시 태그를 맞춰서 push를 해줘야 한다.



## 로컬 도커 로그아웃

```bash
docker logout
```
