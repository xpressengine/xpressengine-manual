
# 설치하기

## 서버 요구사항

XE를 설치하기 위해서는 아래의 요구사항이 만족되어야 합니다.
* 웹서버\(apache, nginx 등\)
* PHP 7 이상\(XE3.0.0-beta.24 부터\) 
  * PDO PHP Extension
  * cURL PHP Extension
  * FileInfo PHP Extension
  * GD PHP Extension
  * Mbstring PHP Extension
  * OpenSSL PHP Extension
  * Zip PHP Extension
* MariaDB or MySQL 5.1 이상
* 터미널 접속 환경
* 디스크 300M 이상의 여유 공간
  * 500M 이상 권장
  
  
## 최신버전 다운로드
>참고하세요!     
XE 3.0.1 버전에서 이모지 지원을 위해 Mysql의 Charset이 UTF8MB4가 기본이 되었습니다.     
설치시 문제가 있는 경우 [여기를 눌러](https://www.xpressengine.io/blog/XE-301-%EB%B0%B0%ED%8F%AC-%EC%95%88%EB%82%B4) 해결하실 수 있습니다.

우선 XE 최신버전을 다운로드 받은 후, 압축을 풀어서 XE가 설치된 디렉토리에 덮어씌웁니다.

만약 최신버전 바로 이전의 버전을 설치하여 사용하고 계셨다면, 최신버전에서 변경된 파일만 덮어씌우셔도 됩니다. 변경된 파일은 XE3의 [Github 릴리즈 페이지](https://github.com/xpressengine/xpressengine/releases)에서 다운로드 받을 수 있습니다. 변경된 파일 모음은 각 릴리즈마다 `changed.xxx.zip`의 형식으로 첨부되어 있습니다.

> 주의!  
만약, XE소스코드나 플러그인 소스코드를 수정했다면 미리 백업해두시길 바랍니다.

### 설치 파일 다운로드

> [http://start.xpressengine.io/download/latest.zip](http://start.xpressengine.io/download/latest.zip) 을 [다운로드](http://start.xpressengine.io/download/latest.zip) 합니다.
 다운로드 받은 zip 파일의 압축을 풀고 서버에 업로드 합니다. \(약 100MB\)

#### 설치 동영상

이해를 위해 짧은 이미지를 준비했습니다

<center><img src="../.gitbook/assets/install.gif"></center>

#### FileZila

FTP는 FileZila 를 사용해서 설명합니다. FileZila 는 무료로 사용이 가능한 프로그램 입니다. [다운로드](https://filezilla-project.org/download.php?type=client)

#### 디렉토리 권한 설정

**bootstrap/cache, config/production, storage, vendor, plugins 폴더 설정**

상단의 폴더들을 707 권한으로 변경해주세요.

> 권한 설정할 때 `하위 디렉터리로 이동`, `모든 파일과 디렉터리에 적용` 을 반드시 체크해 주세요

<center><img src="../.gitbook/assets/ftp_permission.gif"></center>

### 웹 인스톨러 실행

설치할 사이트에 접속하면 인스톨 화면으로 이동됩니다.  
웹 서버가 파일을 쓸 수 있도록 권한을 설정합니다.

> 만약 하위 디렉토리에 설치할 경우는 해당 디렉토리로 접속해 주세요.

#### 설치 진행하기

> DB 접속 정보를 모르거나, 정확히 입력해도 진행되지 않는다면, 사용하고 있는 호스팅 회사에 접속 방법을 문의하시기 바라며, 기본 포트와 호스트, DB 이름과 사용자 계정명이 다를 수 있습니다. 


### 알려진 문제점

#### FTP의 파일 업로드 오류

파일 업로드 및 디렉토리 설정을 완료하고 웹 인스톨러 접근할 때 오류가 발생하는 경우가 있습니다. 이 문제는 FTP 파일 업로드 중 누락된 파일이 있어 발생할 수 있는 문제 입니다. 해결하기 위해서 FTP로 다시 업로드 해야합니다. 동일 조건을 업로드할 경우 비슷한 오류가 계속해서 발생할 수 있으므로 중복파일 `건너뛰기` 옵션으로 업로드 해보는걸 권장합니다.

#### 웹 서버 타임아웃

서버 성능에 따라 웹 서버 타임아웃 설정에 의해 설치에 실패할 가능성이 있습니다. 이 문제는 사용하고 있는 호스팅 회사 또는 서버 관리자에게 문의하시기 바랍니다.

