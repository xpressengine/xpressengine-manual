# 업데이트

XE 업데이트는 3.0.0-beta 버전부터 지원합니다. 만약 현재 설치된 XE가 그 이전 버전이면 새로 설치하셔야 합니다.

#### 최신버전 다운로드

우선 XE 최신버전을 다운로드 받은 후, 압축을 풀어서 XE가 설치된 디렉토리에 덮어씌웁니다.

> 주의!<br> 
> 만약, XE소스코드나 플러그인 소스코드를 수정했다면 미리 백업해두시길 바랍니다.

#### 업데이트 적용

아래 콘솔 커맨드를 사용하여 업데이트된 소스코드를 적용합니다.

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

설치를 완료한 후에는 `사이트 관리자 > 플러그인 > 플러그인 목록`에서 플러그인들도 업데이트하시기 바랍니다.

