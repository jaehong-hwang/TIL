# chroot 세팅 과정, 세팅 이슈

``` bash
vi /etc/ssh/sshd_config
```
위 명령어를 통해 

Match User username
chroot 설정을 했다.

이슈는 /bin/bash가 실행이 안된다는것.
즉 ssh의 루트 디렉토리를 설정이 된건 좋지만
그 루트 디렉토리에 모든 bin 파일들이 들어가야한다는점.