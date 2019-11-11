# 플러그인 정보 추가 및 수정
## 플러그인 이름 생성하기

XE에서 제공하는 `php artisan` 명령어로 플러그인을 만든경우 설치된 익스텐션 페이지에서 확인할 수 있는 이름은 `플러그인이름 + Plugin`이며, 이름과 설명은 기본적인 더미 설명으로 채워져있습니다.
[이미지]

이 항목을 수정하기 위하여 `XE/plugins/만든플러그인명/composer.json` 파일을 엽니다.

생성된 직후의 플러그인의 `composer.json` 파일을 아래처럼 수정해줍니다.

```json
{
  "name": "xpressengine-plugin/dummy",
  "description": "이곳에는 플러그인 설명을 담습니다. 이 플러그인은 더미 플러그인 입니다.",
  "keywords": [
    "xpressengine",
    "plugin"
  ],
  "license": "LGPL-3.0-or-later",
  "version": "1.0.0",
  "type": "xpressengine-plugin",
  "support": {
    //사용자에게 지원이 가능한 이메일 주소를 입력합니다.
    "email": "contact@xpressengine.com"
  },
  "authors": [
    {
    //창작자의 이름(활동명), 이메일, 웹사이트 주소를 순서대로 적습니다.
      "name": "XEHub",
      "email": "contact@xpressengine.com",
      "homepage": "https://xpressengine.io/",
      "role": "Developer"
    }
  ],
  "extra": {
    "xpressengine": {
    //설치된 익스텐션 페이지에 노출될 플러그인 이름을 입력합니다.
      "title": "플러그인 생성 테스트용 이름.",
      "component": {}
    }
  },
  "require": {
    //플러그인이 지원할 XE 코어의 최저 버전을 입력합니다.
    "xpressengine/xpressengine": "~3.0.4"
  },
  "autoload": {
    "psr-4": {
      "LuisK\\XePlugin\\Dummy\\": "src/"
    }
  }
}
```

해당 항목을 그대로 복사하지 않고, 주석을 보고 창작자의 정보를 기입하시기 바랍니다.

