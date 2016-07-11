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
```

위 명령어 실행하면 인스톨이 시작되면 설치 정보를 입력합니다.


## 설치 정보 입력

#### 데이터베이스, 사이트 정보 입력
*인스톨러 캡쳐 이미지*(database, site 정보 입력 / 엔터)
인스톨러가 Database 및 기본 설정 파일을 생성합니다. 이 작업은 시간이 오래 걸릴 수 있습니다.

* Host [localhost] : Database 주소. 기본 `localhost`
* Port [3306] : Database prot. 기본 `3306`
* Database name : Database name
* UserId [root] : Database user id. 기본 `root`
* Password [] : Database user password
* site url [http://mysite.com] : 홈페이지 주소 입력.
  > 하위 디렉토리에 설치 할 경우 하위 디렉토리 까지 입력해야합니다.
* Timezone [Asia/Seoul] : 타임존 정보를 입력합니다. 기본 `Asia/Seoul`
  > [타임존](http://php.net/manual/kr/timezones.php) 에서 원하는 지역의 시간대를 입력하세요.
* locale [] : 언어를 입력합니다. 영여, 한국어 두가지 언어를 지원합니다. 
  > 다른 언어의 설치는 인스톨 후에 언어팩을 업로드 해서 사용가능합니다. RC 버전에서 지원할 예정입니다.


인스톨은 영여, 한국어 두가지 언어 설치를 지원합니다.
다른 언어의 설치는 인스톨 후에 언어팩을 업로드 해서 사용가능합니다. RC 버전에서 지원할 예정입니다.


#### 관리자 정보 입력
*인스톨러 캡쳐 이미지*(관리자 정보 입력)


* Email : 관리자 이메일
* Name [admin] : 관리자 이름. 기본 `admin`
* Password : 관리자 비밀번호
* Password again : 관리자 비밀번호 확인

#### 디렉토리 권한 및 서버 정보 수집 동의
*인스톨러 캡쳐 이미지*(설정)

* ./storage directory permission [0707] : /storage 디렉토리 권한 설정. 기본 `0707`
* ./bootstrap/cache directory permission [0707] : /bootstrap/cache 디렉토리 권한 설정. 기본 `0707`
* Do you agree to collect your system environmental information? [yes] : 서버 환경 정보 수집 동의. 기본 `yes`
  > 더나은 서비스 제공을 위해 설치된 서버의 환경을 수집하고 있습니다. 서버, 웹서버, PHP, Database 등의 정보를 수집합니다.


## 설정 파일을 이용한 설치
설정파일을 사용하면 더욱 쉽게 설치할 수 있습니다. 설치하기 전에 아래와 같이 커맨드를 실행하여 설정파일을 생성합니다.
```
$ copy('http://start.xpressengine.io/installer', 'installer');" && php installer make
```

xe_install_config.yaml 파일이 생성됩니다. 파일을 열고 설치 정보를 입력하세요.
설치 커맨드를 실행합니다. --config 및 --no-interact 옵션을 사용하십시오.
```
$ php installer install --config=.xe_install_config.yaml --no-interact
```

#### 설치옵션
* --config=< configfile> 설정파일을 지정합니다.
* --no-interact 대화형입력을 사용하지 않고 설정파일의 정보를 사용하여 자동으로 설치합니다. 이 옵션을 --config옵션과 같이 사용해야 합니다.
* --install-dir 설치경로를 지정합니다. 지정하지 않을 경우 현재 디렉토리에 설치합니다.


