---
title: (Linux) 백그라운드 작업 nohup 사용법
intro: 터미널 종료 시 프로세스 중단을 무시하고 계속해서 백그라운드로 돌리는 방법
date: 2019-03-31 15:01:22
tags: [linux, nohup, python]
category: linux
---
리눅스에서 24시간으로 작동해야 하는 파이썬(Python) 프로그램이 있습니다. 이런 경우에 `python filename.py`을 치고 터미널을 켜둔 상태에서 24시간을 두면 됩니다. 하지만 현실적으로 우리는 터미널을 항상 켜둘 수 없습니다. 백그라운드 상태에서 돌아가야 하고 터미널 세션을 종료했다 하더라도 계속해서 돌아가는 게 이상적입니다. 이것을 가능하게 도와주는 것이 nohup(노헙)입니다.

## nohup과 &의 차이

리눅스에서 백그라운드로 프로그램을 실행하는 방법 중 또 다른 하나로 명령어 뒤에 &을 붙이는 방법이 있습니다.

```terminal
python filename.py &
```

위의 방법으로 실행 시 백그라운드로 작동하지만 터미널 세션을 종료하면 프로그램이 종료됩니다.

반면에 nohup을 사용하면 앞에서 말했듯이 터미널을 종료해도 데몬 형태로 계속해서 작동합니다.

## 사용법

### 데몬 형태 실행 방법

```terminal
nohup python filename.py &
```

터미널에서 위의 명령어를 입력하기만 하면 데몬 형태의 프로세스로 프로그램이 돌아갑니다. 그리고 nohup.out이라는 파일이 생기며 print문이 어느 정도 쌓이면 해당 파일에 입력이 됩니다.

실시간으로 찍히는 print문을 입력하고 싶다면 아래의 `-u` 명령어를 추가하시면 됩니다.

```terminal
nohup python -u filename.py &
```

### 로그보는 방법

```terminal
tail -f nohup.out
```

`-u`를 입력하셨다면 실시간으로 로그가 출력되는게 보이실겁니다. 하지 않으셨다면 어느 정도 쌓이고 보이실 겁니다.

### 종료

```terminal
ps -ef | grep filename.py
```

위 명령어를 치시면 데몬 형태로 실행 중인 프로세스들이 뜹니다. 두 번째 칸에 뜨는게 프로세스 아이디(Process ID)입니다.

```terminal
kill 프로세스 아이디
```

위 명령어로 종료시켜줍니다.

## 주의사항

nohup으로 작업할 파일은 755 이상의 권한이 필요합니다.

```terminal
chmod 755 shell.sh
```
