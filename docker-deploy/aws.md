# AWS 보안그룹 웹 공유 설정

aws ec2에 컨테이너를 실행하는 과정까지 모두 했다면

이제 aws 보안그룹에서 http 공유를 해줘야한다.



aws ec2 실행중인 인스턴스에서 해당 보안 그룹으로 들어간다.

다음 이미지와 같이 인바운드 편집을 눌러서 http를 설정하고 저장한다.

<figure><img src="../.gitbook/assets/스크린샷 2024-02-12 시간 15.24.13.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/스크린샷 2024-02-12 시간 15.24.27.png" alt=""><figcaption></figcaption></figure>

이제 해당 인스턴스의 public ip로 접속하면 실행된 웹을 볼수 있다.
