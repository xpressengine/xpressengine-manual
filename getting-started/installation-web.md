# 설치하기

## 웹 / FTP 환경에서 설치하기

> [http://start.xpressengine.io/download/latest.zip](http://start.xpressengine.io/download/latest.zip) 을 [다운로드](http://start.xpressengine.io/download/latest.zip) 합니다.
 다운로드 받은 zip 파일의 압축을 풀고 서버에 업로드 합니다.
 
 > [공식 홈페이지](https://www.xpressengine.io/)에서 다운로드 받은 파일은 composer 및 기타 라이브러리가 빌드된 상태의 파일입니다. (약 100MB)<br>
 빌드 된 상태가 아닌 XE 코어만 필요하신 경우 [XE 깃허브](https://github.com/xpressengine/xpressengine)에서 다운로드 해주세요. (약 6MB)
 
 ### FTP에 업로드

FTP는 FileZila 를 사용해서 설명합니다. FileZila 는 무료로 사용이 가능한 프로그램 입니다. [다운로드](https://filezilla-project.org/download.php?type=client)<br>
모든 파일은 압축을 풀어 웹 서버 디렉토리에 바로 업로드할 수 있습니다.

### 디렉토리 권한 설정

| 폴더 목록 | 권한 및 설명 |
|:--------|:--------:|
| ./storage | 미디어 첨부 및 로그 저장 파일 경로<br>`기본 권한 0707` |
| ./bootstrap<br>./bootstrap/cache | `기본 권한 0707` |
| ./config<br>./config/production | XE의 기본설정 폴더<br>`기본 권한 0707` |
| ./vendor | composer등 XE 라이브러리 폴더<br>`기본 권한 0707` |
| ./plugins | XE 플러그인 폴더<br>`기본 권한 0707` |
| 그외 루트 파일 | XE최상위 파일(index.php, composer.phar 등)<br>`기본 권한 0707` |

상단에 나온 폴더 하위 및 모든 파일에 `권한 0707`을 부여합니다.

> 더나은 서비스 제공을 위해 설치된 서버의 환경을 수집하고 있습니다. 서버, 웹서버, PHP, Database 등의 정보를 수집합니다.

> 권한 설정할 때 `하위 디렉터리로 이동`, `모든 파일과 디렉터리에 적용` 을 반드시 체크해 주세요  

<center><img src="../.gitbook/assets/ftp_permission.gif"></center>



### 웹 인스톨러 실행

FTP에 파일이 누락된 것이 없다면, 내 사이트로 접속하여 설치를 진행할 수 있습니다.
아래의 화면은 XE를 설치하는 모습입니다.

> 만약 하위 디렉토리에 설치할 경우는 해당 디렉토리로 접속해 주세요.

<center><img src="../.gitbook/assets/install.gif"></center>

> DB 접속 정보를 모르거나, 정확히 입력해도 진행되지 않는다면, 사용하고 있는 호스팅 회사에 접속 방법을 문의하시기 바라며, 기본 포트와 호스트, DB 이름과 사용자 계정명이 다를 수 있습니다.
> 웹 서버 환경에 따라 cURL 확장 기능이 방화벽으로 차단되어 있는 경우가 있습니다. 사용하는 호스팅 회사에 cURL 사용 가능여부를 확인해주시기 바랍니다.



## 알려진 문제점

### FTP의 파일 업로드 오류

파일 업로드 및 디렉토리 설정을 완료하고 웹 인스톨러 접근할 때 오류가 발생하는 경우가 있습니다. 이 문제는 FTP 파일 업로드 중 누락된 파일이 있어 발생할 수 있는 문제 입니다. 해결하기 위해서 FTP로 다시 업로드 해야합니다. 동일 조건을 업로드할 경우 비슷한 오류가 계속해서 발생할 수 있으므로 중복파일 `건너뛰기` 옵션으로 업로드 해보는걸 권장합니다.

### 웹 서버 타임아웃

서버 성능에 따라 웹 서버 타임아웃 설정에 의해 설치에 실패할 가능성이 있습니다. 이 문제는 사용하고 있는 호스팅 회사 또는 서버 관리자에게 문의하시기 바랍니다.


