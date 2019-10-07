---
layout: post
title: 슬랙 봇과 AWS
description: >
	AI프로젝트 중에 aws서버를 하나 제공해주고 우리의 봇을 24시간 돌릴수 있게 하라는 미션이 주어졌다.
excerpt_separator: <!--more-->

---

<!--more-->
# Slack Bot with AWS!!

저번에 혼자 Github와 Travis와 Docker와 AWS를 연동한다고 2틀동안 삽질했던 경험을 살려 이번에도 역시 엄청난 에러들을 잡아가며 성공해냈다.

역시 익숙한 친구를 쓰는게 좋을 것 같아 nginx로 프록시 서버를 만들기로 했다.

아참 putty에서 작업중이다.



## nginx 설치

```shell
sudo apt-get update
sudo apt-get install nginx
```

위 두 개의 명령어만 입력하면 바로 nginx설치 완료가 되면서 제공된 aws의 public ip를 입력해서 들어가면 nginx어쩌고저쩌고 하는 창이 나온다. 그냥 딱 보면 nginx메인화면이구나 생각나게 생겼다.



## 프록시 설정

다음으로 프록시 설정 및 적용을 위한 디렉터리 연결작업을 해주었다.

```shell
cd /etc/nginx/sites-available/
sudo vi my-server
```

```shell
server {
	listen 80;
	server_name 11.222.222.111;
	location / {
		proxy_pass http://127.0.0.1:5000;
	}
}
```

- sites-available폴더는 프록시 설정을 할 수 있는 폴더이다.

- my-server라는 설정 파일을 위와 같이 만들었다.

- 설정파일을 해석해보자면 11.222.222.111:80/ 주소로 요청이 들어오면 현재 서버에서 실행되는 127.0.0.1:5000으로 요청을 대신 보내준다는 의미이다.

- server_name 에 있는 IP는 사용할 서버의 IP를 입력해주면 된다.

- 슬랙봇을 구동하는 flask는 5000번 포트로 잡히기 때문에 5000번으로 설정해주었다.

```shell
sudo ln -s /etc/nginx/sites-available/my-server /etc/nginx/sites-enabled/
```

위 명령어를 통해 sites-enabled폴더에 설정파일을 연결해준다. 이 폴더에 파일이 연결되어 있어야 nginx가 프록시 설정을 적용할 수 있다.

```shell
sudo service nginx restart
```

이제 다시 nginx를 재시작 해주면 프록시 설정은 끝~



## Slack Bot 연결

그렇다면 이제 만들어둔 슬랙 봇 Event Subscriptions에서 Request URL을 바꿔주면 된다.

![study_slack_bot_deploy_01](/assets/img/post/study_slack_bot_deploy_01.png)

저 검은 부분에 프록시 설정에 넣어주었던 server ip를 넣어주기만 하면 슬랙과도 연결이 끝~~

```
  SyntaxError: Non-ASCII character '\xec' in file....
```

프로젝트를 clone받은 폴더로 이동하여 app.py를 실행했더니 이런 에러가 떠버렸다. 찾아보니 파이썬 인코딩 문제였다.

```python
# -*- coding: utf-8 -*-
```

맨위에 utf-8로 인코딩 지정을 추가해주는 것으로 해결!



![study_slack_bot_deploy_02](/assets/img/post/study_slack_bot_deploy_02.png)

그리고 서버 IP로 들어가보면 Server is ready가 성공적으로 떴다!! 물론 슬랙 봇도 제대로 동작한다!!! 



## Background에서 실행시키기

하지만 중요한 건 내가 쉘스크립트를 나가더라도 app.py가 계속 동작하지 않으면 aws에 nginx로 구동하는데 아무런 의미가 없다. 그래서 이제 flask를 계속 구동시키는 방법을 찾아보았는데 nohup 키워드를 사용하면 백그라운드에서 계속 돌릴 수 있다고 한다. 아래에 한번에 정리해야겠다.

```
$ python app.py
	app.py 실행
	
$ nohup python app.py &
	세션이 끊겨도 백그라운드에서 돌도록 app.py 실행
	
$ nohup: ignoring input and appending output to 'nohup.out'
	nohup으로 실행시키면 이러한 문구가 나오는데 Error난게 아니라 nohup.out이라는 파일에 출력문자열이 저장된다는 안내(?)느낌이다.

$ ps -ef | grep python
	python 명령어로 실행중인 프로세스들의 목록일 볼 수 있다.

$ kill -9 '프로세스번호'
	프로세스 목록에서 번호를 확인하여 수행하면 프로세스를 종료시킬 수 있다.
```

- #### nohup

  리눅스나 유닉스에서 쉘스크립트 파일을 데몬형태로 실행시키는 프로그램이다. 터미널 세션이 끊겨도 실행을 멈추지 않고 동작할 수 있도록!!

- ##### &

  프로세스를 실행할 때 백그라운드에서 동작하도록 !!

  

요정도..로 nohup으로 실행시켜주면 신기하게도 쉘스크립트를 종료해도 슬랙봇을 계속 사용할 수 있다!!! 우와 짱신기~~ 와.. 계속 안되다가 마지막에 제대로 동작했을 때의 쾌감은 정말 말로 설명할 수 없다. 

If : nohup으로 실행시켜도 제대로 동작하지 않는다면 nohup.out파일을 확인해보면 어떤 오류가 떴는지를 알 수 있다!! 보고 오류를 수정하면 된다 ㅎㅎ 한 두어번 수정했다... 역시 error의 늪은 헤어나올 수 없다..



## 후기

사실 aws인스턴스를 직접 만들고 docker와 trevis를 전부 연동하는 작업보다는 확실히 순탄했던 것 같다. 그때는 linux명령어들 1도몰라서 전부 찾아가면서 시간을 보냈으니깐.. 전부 연동하는 작업들도 정리하고 싶은데 어떻게 정리해야할지 아직 복잡한 느낌이다. 

그래도 nginx으로 blue/green deploy를 해봐서 그런지 nginx는 먼가 친근했다. 암튼 무사히 슬랙봇을 aws에서 돌리고 있다. 꿀잼이당ㅎ