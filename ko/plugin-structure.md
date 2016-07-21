# 플러그인 구조

XE 플러그인은 하나의 디렉토리로 구성되며, 디렉토리 안에는 플러그인에 필요한 파일들이 포함돼 있습니다. 아래의 파일 목록은 각 플러그인이 포함해야 할 필수 파일들입니다.

```
plugins/
└── myplugin/
    ├── composer.json
    └── plugin.php

```

> 플러그인을 생성할 때에는 직접 모든 파일을 작성하지 마시고, 플러그인 생성 명령(`php artisan make:plugin`)을 사용하시기 바랍니다. 몇가지 설정만 입력해주면 플러그인에 필요한 파일을 자동으로 작성해주고, 권장하는 디렉토리 구조를 생성해줍니다.


#### composer.json

XE 플러그인은 하나의 composer 패키지이기도 합니다. 다른 플러그인이나 라이브러리 패키지에 대한 의존성 처리, 오토로드(autoload)와 같은 composer의 장점을 활용하기 위해, XE에서는 composer를 사용하여 플러그인을 관리합니다. `composer.json` 파일은 composer가 패키지의 정보를 담을 때 사용하는 파일입니다. 또한 XE는 XE가 자체적으로 필요로 하는 플러그인 정보를 담기 위해 이 파일을 같이 사용합니다.

아래 코드는 `alice` 번들 플러그인의 `composer.json` 파일입니다.

```json
{
  "name": "xpressengine-plugin/alice",
  "description": "This Package is Xpressengine Plugin - Alice Theme.",
  "keywords": ["xpressengine", "plugin", "theme", "alice"],
  "license": "LGPL-2.1",
  "version": "0.9.0",
  "type": "xpressengine-plugin",
  "authors": [{
      "name": "xpressengine"
  }],
  "extra": {
    "xpressengine": {
      "title": "Alice Theme",
      "screenshots": [
        "screenshots/main.png",
        "screenshots/sub.png",
        "screenshots/site.png"
      ],
      "icon": "icon.png",
      "component": {
        "theme/alice@alice": {
          "class": "Xpressengine\\Plugins\\Alice\\Theme\\Alice",
          "name": "Alice",
          "description": "The First Theme for XpressEngine3",
          "screenshot": "/plugins/alice/screenshots/main.png",
        }
      }
    }
  },
  "autoload": {
    "psr-4": {
      "Xpressengine\\Plugins\\Alice\\": "src/"
    }
  }
}
```

`composer.json` 파일은 json 형식으로 정보를 담고 있습니다. 대부분 composer가 필요로 하는 정보들입니다. 여러분이 눈여겨 보아야 할 정보는 `extra > xpressengine`에 기록된 정보입니다. `extra > xpressengine`에는 이 플러그인의 제목(title), 스크린샷 목록(screenshots), 아이콘(icon), 그리고 이 플러그인에서 등록하는 XE 컴포넌트에 대한 정보(component)를 담고 있습니다.


#### plugin.php

`composer.json` 파일이 플러그인에 대한 정보를 담고 있는 파일이라면, `plugin.php` 파일은 플러그인의 실제 작동을 하는데 필요한 코드가 기술되는 파일입니다.

XE는 플러그인이 설치(install), 업데이트(update), 활성화(activate), 비활성화(deactivate), 삭제(uninstall), 부트(boot)될 때, 이 파일에 기술된 코드를 실행하여 플러그인이 필요로 하는 작업을 할 수 있도록 합니다.

`plugin.php`에는 반드시 하나의 PHP 클래스가 기술되어야 합니다. 이 플러그인 클래스는 반드시 `\Xpressengine\Plugin\AbstractPlugin` 클래스를 `extends`해야 합니다.

가장 간단한 형태의 `plugin.php` 파일입니다.
```
<?php
namespace MyPlugin\Sample;

use Xpressengine\Plugin\AbstractPlugin;

class Plugin extends AbstractPlugin
{
    public function boot()
    {
        // implement code
    }
}
```

플러그인 클래스는 아래의 메소드를 구현할 수 있습니다.

| 메소드 | 설명 |
|--
| activate | 플러그인이 활성화될 때 필요한 코드를 작성하십시오.
| boot | 플러그인이 부트될 때 필요한 코드를 작성하십시오.
| checkInstalled | 플러그인의 설치 여부를 체크한 결과를 반환하십시오. 이 메소드가 `false`를 반환하면 플러그인이 활성화 될 때 `install` 메소드가 실행됩니다.
| install | 데이터베이스 테이블 생성코드와 같이 플러그인을 설치할 때 필요한 코드를 작성하십시오. 
| checkUpdated | 플러그인의 업데이트 여부를 체크한 결과를 반환하십시오. 이 메소드가 `false`를 반환하면 플러그인이 활성화 될 때 `update` 메소드가 실행됩니다.
| update | 플러그인이 변경되었고, 데이터베이스 테이블의 컬럼 추가와 같은 작업이 필요하다면, 이 메소드에 필요한 코드를 작성하십시오. 
| deactivate | 플러그인이 비활성화될 때 필요한 코드를 작성하십시오.
| uninstall | 플러그인이 삭제될 때 필요한 코드를 작성하십시오.
| getSettingsURI | 만약 플러그인이 '플러그인 설정' 페이지를 가진다면, 이 메소드에서 페이지의 url을 반환하십시오. 플러그인 관리페이지에서 반환한 링크가 노출됩니다.


#### 권장하는 플러그인 구조

플러그인은 위에서 설명한 두개의 파일(`plugin.php`, `composer.json`)만으로도 구현이 가능합니다. 하지만 규모가 큰 플러그인을 제작할 때에는 더욱더 많은 파일을 필요로 합니다. 또, 많은 파일을 제대로 관리하기 위해서는 디렉토리 구조를 잘 설계해야 합니다. XE는 플러그인의 구조를 아래와 같이 권장하고 있습니다.

```
plugins/
└── myplugin
    ├── assets/
    ├── screenshots/
    ├── src/
    ├── views/
    ├── icon.png
    ├── composer.json
    └── plugin.php
```

`assets` 디렉토리는 stylesheet, script, 이미지 파일과 같은 asset 파일들을 담는 디렉토리입니다. 웹 브라우저에서 직접적으로 요청하는 파일들을 이 디렉토리에 넣으십시오.

`src` 디렉토리는 PHP 클래스나 함수와 같이 php로 작성된 파일들을 담는 디렉토리입니다. 컨트롤러 및 컴포넌트 파일들을 이 디렉토리에 넣으십시오.

`views` 디렉토리는 템플릿 파일을 담는 디렉토리입니다. XE에서는 blade 템플릿 엔진을 사용합니다. blade 템플릿으로 작성된 PHP 파일, 또는 순수한 PHP 작성된 템플릿 파일을 이 디렉토리에 넣으십시오. 

`screenshots` 디렉토리에는 플러그인의 스크린샷 이미지를 넣으십시오.

`icon.png`와 같이 플러그인의 아이콘 이미지 파일은 플러그인 디렉토리에 넣으십시오. 파일명과 확장자는 다를 수 있습니다.

스크린샷 이미지나 아이콘 이미지는 `composer.json`의 `extra > xpressengine`에 파일경로를 지정해 주어야 합니다.

