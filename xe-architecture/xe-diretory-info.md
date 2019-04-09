# 디렉토리 구조

루트 디렉토리에서 중요한 항목만 나열해 보았습니다. XE는 라라벨 프레임워크의 구조를 기반으로하여 커스터마이징되어 있습니다.

```text
├── app/
├── assets/
├── bootstrap/
├── config/
├── core/
├── migrations/
├── plugins/
├── resources/
├── storage/
├── vendor/
├── web_installer/
├── artisan
├── composer.json
├── composer.user.json.example
└── index.php
```

## 루트 디렉토리

### app

XE를 구성하는 컨트롤러나 커맨드와 같은 어플리케이션 레벨에 해당하는 코드가 들어있습니다.

### core

XE의 핵심적인 서비스 및 컴포넌트 관리 기능을 위한 클래스 파일의 모음입니다.

### migrations

XE를 설치하거나 업데이트할 때, 실행되는 코드가 들어있습니다.

### config

XE의 설정을 저장하는 파일들이 들어있습니다.

### assets

웹페이지에서 필요한 stylesheet, javascript나 이미지 파일과 같은 assets 파일들이 들어있습니다.

### plugins

서드파티에서 작성한 플러그인이 설치되는 디렉토리입니다.

### resources

XE에서 필요한 템플릿 파일이나 언어파일이 들어있습니다.

### storage

이 디렉토리에는 XE가 실행되면서 생성되는 동적인 파일들이 저장되는 위치입니다. 회원 프로필 사진이나 게시판 첨부 파일, 그외 캐시 파일 등이 이 디렉토리에 생성됩니다.

### vendor

composer를 통해 설치되는 외부 라이브러리들이 설치되는 디렉토리입니다.

### web\_installer

XE를 웹을 통해 설치할 때 필요한 파일들이 들어있습니다.

### artisan

터미널에서 XE의 명령을 실행할 때 사용하는 파일입니다.

### composer.json

XE는 PHP 패키지 관리툴인 composer를 사용합니다. 이 파일은 composer 툴에서 사용하는 설정 파일입니다.

### composer.user.json.example

XE에서는 `composer.json`파일을 직접 수정하는 것을 금지합니다. `composer.json` 파일은 XE의 소스코드에 포함된 파일이므로 XE를 업데이트할 때 덮어씌워집니다. `composer.json`을 수정하는 대신 `composer.user.json` 파일을 사용하여 패키지를 관리할 수 있습니다. `composer.user.json.example` 파일 이름을 `composer.user.json`로 바꿔서 사용하시면 됩니다.

### index.php

웹브라우저를 통해 사이트에 접근할 때, 항상 실행되는 PHP파일입니다.

