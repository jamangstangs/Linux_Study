## 리눅스 런레벨

- 리눅스는 시스템이 가동되는 방법을 **7가지 런레벨로 나눌 수 있다. **
- 리눅스는 기본적으로 Multi-User를 지원한다. 

| 런레벨 | 설명                                       |
| ------ | ------------------------------------------ |
| 0      | Power Off, 종료 모드                       |
| 1      | Rescue, 시스템 복구 모드                   |
| 2      | Multi-User, 우분투에서는 사용하지 않는다.  |
| 3      | Multi-User, 텍스트 모드의 다중 사용자 모드 |
| 4      | Multi-User, 우분투에서는 사용하지 않는다.  |
| 5      | Graphical, 그래픽 모드의 다중 사용자 모드  |
| 6      | Reboot                                     |

### runlevel 확인

```shell
# target은 링크파일로, 실제 파일과 연결되어 있다. 따라서, 위와 같이 실제 파일을 확인할 수 있다. 
# 링크파일 개념은 후에 배울 것이다. 
$ cd /lib/systemd/system
$ ls -l runlevel?.target
lrwxrwxrwx 1 root root 15  4월 24 14:50 runlevel0.target -> poweroff.target
lrwxrwxrwx 1 root root 13  4월 24 14:50 runlevel1.target -> rescue.target
lrwxrwxrwx 1 root root 17  4월 24 14:50 runlevel2.target -> multi-user.target
lrwxrwxrwx 1 root root 17  4월 24 14:50 runlevel3.target -> multi-user.target
lrwxrwxrwx 1 root root 17  4월 24 14:50 runlevel4.target -> multi-user.target
lrwxrwxrwx 1 root root 16  4월 24 14:50 runlevel5.target -> graphical.target
lrwxrwxrwx 1 root root 13  4월 24 14:50 runlevel6.target -> reboot.target
```

### 현재 runlevel 확인

```shell
# 현재 우리가 사용하고 있는 default.target, 현재 설정된 런레벨을 확인할 수 있다. 
$ ls -l /lib/systemd/system/default.target
lrwxrwxrwx 1 root root 16  4월 24 14:50 /lib/systemd/system/default.target -> graphical.target
```

### 부팅시 runlevel 변경

```shell
# ln : 링크 파일을 만드는 명령어
$ ln -sf /lib/systemd/system/multi-user.target /lib/systemd/system/default.target

$ ls -l /lib/systemd/system/default.target
lrwxrwxrwx 1 root /lib/systemd/system/default.target -> /lib/systemd/system/multi-user.target
```



## Mount란?

### 개념

- 리눅스에서 HDD partition, CD, USB를 사용하려면 지정한 위치에 연결해줘야한다. 
- **물리적인 장치를 특정한 위치에 연결해주는 과정**

### 명령어

```shell
# 현재 마운트된 장비를 확인 가능하다. 
$ mount 
/dev/disk1s5s1 on / (apfs, sealed, local, read-only, journaled)
devfs on /dev (devfs, local, nobrowse)
/dev/disk1s4 on /System/Volumes/VM (apfs, local, noexec, journaled, noatime, nobrowse)

# 현재 마운트된 장치들을 마운트 해제해보자. 
$ unmount /dev/disk1s5s1

```

### 마운트 위치

- /media/ : 자동으로 마운트된 CD/DVD의 디렉토리 위치이다. 

- USB장치의 이름은 종봉 바뀌므로, 아래 명령어를 기억하자.

  ```shell
  $ ls /deb/sd*
  ```

  - /dev/sda : ubuntu가 설치된 최초의 하드디스크를 의미한다. 
  - /deb/sdb : 추가한 USB를 나타낸다. 

### 언마운트 주의점

- unmount할 디렉터리에서 명령을 실행하면 안된다. 



## 내가 모르는 리눅스 명령어

- head, tail : 텍스트 형식으로 작성된 파일의 앞 10행, 마지막 10행을 화면에 출력한다. 
  - default : 10행
  - -num : num행 만큼 출력한다. 

```shell
$ head text.txt
$ tail text.txt
$ head -5 text.txt
```

- more, less : 텍스트 형식으로 작성된 파일을 페이지 단위로 화면에 출력한다. 
- file : 해당 파일이 어떤 종류의 파일인지를 보여준다. 

```shell
$ file text.txt
text.txt: Unicode text, UTF-8 text

# 위와 같이 어떤 종류의 파일인지를 보여준다. 
```



## User 관리, File 속성

### User와 Group

- 리눅스 : Multi-User System, 1대에 여러 명이 접속이 가능하다. 

  - **Super User : root**라는 이름을 가진 유저이다. 
    1. 시스템의 모든 작업을 실행할 수 있는 권한을 가지고 있다. 
    2. 시스템에 접속 가능한 사용자를 생성할 수 있는 권한을 가지고 있다. 

  이와 같은 환경에서, 모든 사용자를 어떻게 관리하나?

### /etc/passwd

```shell
$ more /etc/passwd
nobody:*:-2:-2:Unprivileged User:/var/empty:/usr/bin/false
root:*:0:0:System Administrator:/var/root:/bin/sh
daemon:*:1:1:System Services:/var/root:/usr/bin/false
_uucp:*:4:4:Unix to Unix Copy Protocol:/var/spool/uucp:/usr/sbin/uucico
_taskgated:*:13:13:Task Gate Daemon:/var/empty:/usr/bin/false
_networkd:*:24:24:Network Services:/var/networkd:/usr/bin/false
_installassistant:*:25:25:Install Assistant:/var/empty:/usr/bin/false
# 각 행의 의미를 알아보자. 
```

- 사용자이름:비밀번호:사용자ID:**사용자가 소속된 그룹ID**:추가정보:홈디렉토리:기본셀
  - root -> 사용자 이름
  - 별 -> 암호가 리눅스에서는 x표시가 되어있다. 맥에서는 *로 표시네
  - 0 -> 사용자ID 
  - 0 -> 사용자가 소속된 그룹 ID
  - System Administrator -> 추가 정보
  - /var/root-> 홈디렉토리, **nobody의 홈디렉토리를 의미한다.**
  - /bin/sh -> 기본셀, 로그인시 제공되는 shell을 의미한다. 

### 명령어

#### User 

- adduser : 새로운 사용자 추가

  ```shell
  $ adduser jm1
  # user id 지정하면서 생성
  $ adduser -uid 2 jm2
  # group id를 지정하면서 생성
  $ adduser -gid 3 jm3
  ```

  - 그냥 추가하면, uid는 +1 올려서 저장한다. 
  - 그냥 추가하면, group은 User 이름과 동일한 이름으로 하나 생성해서 거기에 넣어준다.

- passwd : 사용자의 비밀번호를 변경한다. 

  ```shell
  $ passwd jm1 

- usermod : 사용자의 속성을 변경한다. 

  ```shell
  # 기본 shell을 zsh로 변경한다. 
  $ usermod --shell /bin/zsh
  # ubuntu그룹에 jm1을 추가한다. 
  $ usermod --groups ubuntu jm1

- userdel : 사용자 삭제

  ```shell
  $ userdel jm2
  ```

#### Group

- groups : 사용자가 소속된 그룹을 보여준다. 

  ```shell
  $ groups
  $ groups jm1

- groupadd : 새로운 그룹을 생성한다. 

  ```shell
  $ groupadd group1
  $ groupadd -gid 2 group2
  ```

- groupdel : 그룹을 삭제한다. 

  ```shell
  $ groupdel group2
  ```











