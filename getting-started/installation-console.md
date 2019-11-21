# 설치하기

## 터미널(SSH) 환경 설치 방법
터미널에서 아래와 같이 명령어를 실행합니다.

```text
$ php -r "copy('http://start.xpressengine.io/download/installer', 'installer');" && php installer install
```
## Git
<blockquote class="safe">
    <p>
        Git을 사용하면 업데이트 및 현재 개발중인 코드를 손쉽게 적용할 수 있습니다. 코어 버전 업데이트할 때 FTP 없이 Git 을 통해 업데이트 할 수 있습니다.
    </p>
</blockquote>

Github 저장소 파일을 이용해 설치합니다

```text
$ git clone https://github.com/xpressengine/xpressengine.git
$ cd xpressengine
$ composer install
...
$ php artisan xe:install
...
```
위 명령어 실행하면 인스톨이 시작되면 설치 정보를 입력합니다.

## 설치 정보 입력

### 1. 데이터베이스, 사이트 정보 입력

인스톨러가 Database 및 기본 설정 파일을 생성합니다. 이 작업은 시간이 오래 걸릴 수 있습니다.

| 항목 | 설명 및 기본 값 (미 입력시) |
|:--------|:--------|
| Host | `localhost` 또는 `127.0.0.1`   DB 서버의 IP또는 도메인 입력 |
| Port | 데이터베이스 포트  `기본 값 : 3306` |
| DB NAME | 데이터 베이스 이름  `기본 값 없음` |
| UserID | 데이터 베이스 USER ID  기본 값 :`root` |
| Password | 데이터 베이스 PW  `기본 값 없음`  |
| site url | http://mysite.com 또는 https://mysite.com  홈페이지 주소 입력  하위 디렉토리인 경우 도메인에 디렉토리를 입력해야합니다. |
| TimeZone | [타임존 정보](http://php.net/manual/kr/timezones.php)를 입력합니다  `기본 값 : Asia/Seoul`  |
| Locale | 언어를 입력합니다. 영어, 한국어 총 두가지를 지원합니다.  `기본 값 : ko / 영어 : en`  |

<blockquote class="safe">
    <p>
        Locale의 경우 다른 언어의 설치는 인스톨 후에 언어팩을 업로드 하여 사용 할 수 있습니다.
    </p>
</blockquote>


### 2. 관리자 정보 입력

| 항목 | 설명 및 기본 값 (미 입력시) |
|:--------|:--------|
| Email | 관리자 이메일 주소 |
| Name | 관리자 이름  `기본 값 : admin` |
| Password | 관리자 비밀번호 |
| Password Again | 관리자 비밀번호 확인 |


### 3. 디렉토리 권한 및 서버 정보 수집 동의

| 폴더 목록 | 권한 및 설명 |
|:--------|:--------|
| ./storage | 미디어 첨부 및 로그 저장 파일 경로 `기본 권한 0707` |
| ./bootstrap  ./bootstrap/cache | `기본 권한 0707` |
| ./config  ./config/production | XE의 기본설정 폴더  `기본 권한 0707` |
| ./vendor | composer등 XE 라이브러리 폴더  `기본 권한 0707` |
| ./plugins | XE 플러그인 폴더  `기본 권한 0707` |
| 그외 루트 파일 | XE최상위 파일(index.php, composer.phar 등)  `기본 권한 0707` |

<blockquote class="safe">
    <p>
        더 나이은 제품 및 서비스 제공을 위하여 XE가 설치된 서버의 환경을 수집하고 있습니다. 수집하는 항목은 서버, 웹서버, PHP, DB 등의 정보를 수집하고 있습니다.
    </p>
</blockquote>


## 설정 파일을 이용한 설치

설정파일을 사용하면 더욱 쉽게 설치할 수 있습니다. 설치하기 전에 아래와 같이 커맨드를 실행하여 설정파일을 생성합니다.

```text
$ copy('http://start.xpressengine.io/installer', 'installer');" && php installer make
```

xe\_install\_config.yaml 파일이 생성됩니다. 파일을 열고 설치 정보를 입력하세요. 설치 커맨드를 실행합니다. --config 및 --no-interact 옵션을 사용하십시오.

```text
$ php installer install --config=.xe_install_config.yaml --no-interact
```

### 설치옵션

* --config=&lt; configfile&gt; 설정파일을 지정합니다.
* --no-interact 대화형입력을 사용하지 않고 설정파일의 정보를 사용하여 자동으로 설치합니다. 이 옵션을 --config옵션과 같이 사용해야 합니다.
* --install-dir 설치경로를 지정합니다. 지정하지 않을 경우 현재 디렉토리에 설치합니다.
