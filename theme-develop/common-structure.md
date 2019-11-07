# 테마 기본 경로 및 구조

## 테마 기본 경로

테마의 기본 경로는 `php artisan` 명령어로 만든 테마 기준으로 `plugins/<플러그인명>/src/Theme/테마명` 의 경로로 생성됩니다.
`src`폴더에는 `Theme`만이 아닌 `Skin, widget`등이 같이 존재할 수 있습니다.

## 테마 구조
테마 폴더에 진입하면 테마의 설정 파일인 `info.php`와 테마의 관련 클래스 파일인 `테마명Theme.php`파일이 존재하고 있습니다.

```
│  info.php
│  테마명Theme.php
│
├─assets
│  ├─css
│  │      main.css
│  │      reset.css
│  │      setting.css
│  │      theme.css
│  │
│  ├─images
│  └─js
│          main.js
│          respond.min.js
│
└─views
        gnb.blade.php
        theme.blade.php
```

