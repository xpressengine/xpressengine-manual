# 업데이트

XE 업데이트는 웹 페이지에서 업데이트 하는 방법과, 콘솔에서 명령어를 사용하는 방법이 있습니다. 두 가지 방법 모두 미리 FTP나 git 등으로 XE 코어의 소스코드를 최신 코드로 다운로드 받아 덮어 씌운 다음 진행할 수 있습니다.

#### 최신버전 다운로드

우선 XE 최신버전을 다운로드 받은 후, 압축을 풀어서 XE가 설치된 디렉토리에 덮어씌웁니다. 

만약 최신버전 바로 이전의 버전을 설치하여 사용하고 계셨다면, 최신버전에서 변경된 파일만 덮어씌우셔도 됩니다. 변경된 파일은 XE3의 [Github 릴리즈 페이지](https://github.com/xpressengine/xpressengine/releases)에서 다운로드 받을 수 있습니다. 변경된 파일 모음은 각 릴리즈마다 `changed.xxx.zip`의 형식으로 첨부되어 있습니다.

> 주의!<br> 
> 만약, XE소스코드나 플러그인 소스코드를 수정했다면 미리 백업해두시길 바랍니다.

#### 웹 페이지에서 업데이트 적용

'사이트 관리 > 플러그인 & 업데이트 > XE3 업데이트' 페이지에서 업데이트 할 수 있습니다.

만약 실제 서비스 서버와 별도로 개발 서버(또는 스테이지 서버)를 운영하고 있다면, 개발 서버에서는 'composer update 실행 안 함' 항목을 체크 해제한 상태에서 업데이트 하십시오. `composer update`가 함께 실행되면서 `/vendor` 디렉토리에 설치된 의존 라이브러리들도 최신 상태로 업데이트됩니다.

개발 서버에서 문제없이 업데이트가 됐다면, 개발 서버와 서비스 서버의 소스코드를 동기화 한 다음, 서비스 서버에서 다시 업데이트를 실행하십시오. 이 때에는 'composer update 실행 안 함' 항목을 체크한 다음 업데이트를 실행하시기 바랍니다.


#### 콘솔에서 명령어를 사용하여 업데이트 적용

아래와 같이 콘솔 커맨드를 사용하여 업데이트된 소스코드를 적용할 수도 있습니다.

```
php artisan xe:update --skip-download
```

실제 구동화면입니다.

```
$ php artisan xe:update --skip-download

 Update Xpressengine.

 Update version information:
   3.0.0-beta -> 3.0.0-beta.2

 Update the Xpressengine ver.3.0.0-beta. There is a maximum moisture is applied.
Do you want to update? (yes/no) [no]:
 > yes

Run the composer update. There is a maximum moisture is applied.
----------------------------------------------------------------

 composer update
 ...
 ...
 
 Run the migration of the Xpressengine.
--------------------------------------

 updating category.. [skipped]
 updating config.. [skipped]
 updating counter.. [skipped]
 updating document.. [skipped]
 updating dynamicField.. [skipped]
 updating editor.. [skipped]
 updating media.. [skipped]
 updating menu.. [skipped]
 updating permission.. [skipped]
 updating plugin.. [skipped]
 updating routing.. [skipped]
 updating settings.. [skipped]
 updating site.. [skipped]
 updating skin.. [skipped]
 updating storage.. [skipped]
 updating tag.. [skipped]
 updating temporary.. [skipped]
 updating theme.. [skipped]
 updating translation.. [success]
 updating update.. [skipped]
 updating user.. [skipped]


 [OK] Update the Xpressengine to ver.3.0.0-beta.2

```

**`skip-composer` 옵션**

XE 업데이트 커맨드는 내부적으로 `composer update` 명령을 실행하여 `/vendor` 디렉토리에 설치된 의존 라이브러리들도 최신 상태로 업데이트합니다. `--skip-composer` 옵션을 사용하면 `composer update`를 실행하지 않습니다.

만약 실제 서비스 서버와 별도로 개발 서버(또는 스테이지 서버)를 운영하고 있다면, 개발 서버에서는 이 옵션을 사용하지 않고 업데이트합니다.

개발 서버에서 문제없이 업데이트가 됐다면, 개발 서버와 서비스 서버의 소스코드를 동기화 한 다음, 서비스 서버에서 다시 업데이트를 실행하십시오. 이 때에는 만약 실제 서비스 서버와 별도로 개발 서버(또는 스테이지 서버)를 운영하고 있다면, `--skip-composer` 옵션을 사용하여 중복으로 `composer update`를 실행하지 않도록 할 수 있습니다.


#### 설치된 플러그인의 업데이트

XE3 코어를 업데이트할 때, 설치된 플러그인들은 같이 업데이트 되지 않습니다. 업데이트를 완료한 후에는 `사이트 관리자 > 플러그인 > 플러그인 목록`에서 플러그인들도 업데이트 하시기 바랍니다.


