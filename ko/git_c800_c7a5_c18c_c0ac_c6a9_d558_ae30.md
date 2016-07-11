# 기본 설치하기


## 인스톨러를 이용한 설치

### Linux
터미널에서 아래와 같이 명령어를 실행합니다.
```
$ php -r "copy('http://start.xpressengine.io/installer', 'installer');" && php installer install
```

### Window
> Git 설치
> 터미널 환경을 위해 Git을 설치합니다. Git(준비중) 다운로드 및 설치를 참고하세요

Git-Bash를 실행하고 아래와 같이 명령어를 실행합니다.
```
$ php -r "copy('http://start.xpressengine.io/installer', 'installer');" && php installer install
```

위 명령어 실행하면 인스톨이 시작되면 설치 정보를 입력합니다.


## Git 을 이용한 설치
> Git을 사용하면 업데이트및 현재 개발중인 코드를 손쉽게 적용할 수 있습니다.
> 코어 버전 업데이트할 때 FTP 없이 Git 을 통해 업데이트 할 수 있습니다.

Github 저장소 파일을 이용해 설치합니다

```
$ git clone https://github.com/xpressengine/xpressengine.git
$ cd xpressengine
$ composer install
...
$ php artisan xe:install
...

위 명령어 실행하면 인스톨이 시작되면 설치 정보를 입력합니다.


## 설치 정보 입력
