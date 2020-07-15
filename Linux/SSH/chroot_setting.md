# chroot 세팅 과정, 세팅 이슈

``` bash
vi /etc/ssh/sshd_config
```
위 파일에 들어가 ssh의 `chroot`를 다음과 같이 설정을 했었다.

```
Match User user
    ChrootDirectory /some/your/directory
```

ssh 접속이 안된다. 😢  
`/bin/bash` 가 없다는 오류가 발생하는데 해당 이슈의 원인을 찾았다.

```
$ ssh user@0.0.0.0
user@0.0.0.0's password:
chroot: failed to run command ‘/bin/bash’: No such file or directory 
```

위 이슈가 발상되는 원인은  
`chroot`가 설정된 유저를 통해 ssh로 접속을 하려고 하면  
`/bin/bash` 가 아닌 `chroot` 기준의 `/some/your/directory/bin/bash` 를 찾게 되는것이다.  

그 이외의 **모든 명령어도 동일하며** 이를 해결하는 방법은  
`chroot directory` 내부에 `/bin` 폴더와 그의 의존성 파일들 `ldd /bin/bash` 와 같은 형식으로 확인해  
모두 복사해주어야 한다.

``` bash
$ ldd /bin/bash
        linux-vdso.so.1 (0x00007ffe988fb000)
        libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007f1a60cd2000)
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f1a60ace000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f1a606dd000)
        /lib64/ld-linux-x86-64.so.2 (0x00007f1a61216000)
```

위 파일들을 모두 `chroot`에 복사를 해야 원하는 명령어를 사용 할 수 있다.  
SFTP 계정과 ssh를 동시에 설정하려고 했으나  
SFTP의 루트 디렉토리를 소스 디렉토리로 잡고, ssh 까지 설졍하려고 하니  
소스 디렉토리에 관계 없는 `/bin` 디렉토리의 명령어들을 복사해 줘야하는 이슈가 발생했다.

루트 디렉토리가 소스 디렉토리가 다르다면 큰 이슈 없을것으로 확인된다.
