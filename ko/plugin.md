# 플러그인

플러그인은 여러분이 XE를 할수 있는 유일한 방법입니다. XE에 새로운 기능을 추가하거나 기본 기능을 변경하려고 할 때, 또는 테마나 스킨과 같은 컴포넌트를 XE에 추가하고 싶을 때, 플러그인을 사용하십시오. 

XE는 오픈소스프로그램으로 자유롭게 코어 소스코드를 수정하여 사용할수 있지만, 업데이트 되는 코어소스를 계속 적용하려면 코어소스코드의 수정을 피해야 합니다. 대신 플러그인을 사용하면 코어소스코드의 수정을 피할 수 있습니다. 또한 플러그인을 다른 XE사용자들과 공유할 수도 있습니다.

각 플러그인은 고유한 이름을 가지는 하나의 디렉토리로 구성되며, `/plugins` 디렉토리에 등록됩니다.


## 번들 플러그인

XE는 사용자들이 자주 사용할만한 플러그인을 XE에 포함하여 배포하고 있습니다.

* alice - XE의 기본 테마인 Alice 테마를 제공합니다.
* google_analytics - 구글
* orientator
* page
* board
* comment
* ckeditor
* claim
* external_page
* social_login
* news_client


## 플러그인 구조

각 플러그인은 하나의 디렉토리로 구성되며, 그 디렉토리에는 플러그인에 필요한 파일들이 포함돼 있습니다. 아래의 파일 목록은 각 플러그인이 포함해야 할 필수 파일들입니다.

```
plugins/
└── myplugin/
    ├── composer.json
    └── plugin.php

```

#### composer.json

XE에서 하나의 플러그인은 composer 패키지이기도 합니다. 다른 플러그인이나 라이브러리 패키지에 대한 의존성 처리, 오토로드(autoload)와 같은 composer의 장점을 활용하기 위해 XE에서는 composer를 사용하여 플러그인을 관리합니다. `composer.json` 파일은 composer의 패키지로서의 정보를 담고 있는 파일입니다. XE에서는 composer에서 필요한 정보 이외에 플러그인의 정보를 담기 위해 이 파일을 같이 사용합니다.

`alice` 플러그인의 `composer.json` 파일입니다.

```
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
          "screenshot": "/plugins/alice/screenshots/main.png"
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
    ├── composer.json
    ├── icon.png
    └── plugin.php
```




## 플러그인 상태

어떤 플러그인이 `/plugins` 디렉토리에 등록돼 있다고 해도, 그 플러그인이 XE에서 바로 작동되지 않습니다. 각 플러그인은 활성화 또는 비활성화 상태를 가집니다. 작동되길 원하는 플러그인을 직접 활성화(activate)시켜야 비로소 플러그인이 작동합니다. 플러그인을 활성화시키려면 사이트 관리자 > 플러그인 > 플러그인 목록에서 원하는 플러그인을 활성화시키십시오.


