# 컴포넌트 등록

XE는 다양한 타입의 [컴포넌트](components.md)가 있습니다. 여러분이 컴포넌트를 가지는 플러그인 개발을 시작한다면, 먼저 뼈대만 갖춘 컴포넌트를 작성하고, 플러그인을 통해 작성한 컴포넌트를 XE에 등록해야 합니다. 타입이 다른 컴포넌트라도 XE에 등록하는 방법은 모두 동일합니다.


## 컴포넌트 아이디

각각의 컴포넌트는 모두 고유의 아이디를 가지고 있어야 합니다. XE는 컴포넌트의 아이디를 통해 등록된 컴포넌트를 효과적으로 관리합니다. 또, 컴포넌트 아이디는 컴포넌트 타입을 구분짓는 역할도 병행합니다. 각 컴포넌트 타입마다 아이디를 지정하는 규칙이 아래와 같이 정해져있습니다.

| 컴포넌트 타입 | 아이디 규칙 | 예제 |
| -- | -- | -- |
| 테마 | `theme/<plugin_name>@<pure_id>` | `theme/alice@alice` |
| 모듈 | `module/<plugin_name>@<pure_id>` | `module/xpressengine@board` |
| 위젯 | `widget/<plugin_name>@<pure_id>` | `widget/xpressengine@content` |
| 스킨 | `<skin_target_id>/skin/<plugin_name>@<pure_id>` | `user/profile/skin/social_login@default`<br> `module/xpressengine@board/skin/board@gallery`<br> `widget/xpressengine@content/skin/my_plugin@content` |
| UI오브젝트 | `uiobject/<plugin_name>@<pure_id>` | `uiobject/xpressengine@formSelect` |
| ... | ... | ... |





## composer.json을 사용하여 등록하기

플러그인의 필수 구성 파일인 `composer.json`을 통해 컴포넌트를 등록할 수 있습니다. 

`composer.json` 항목의 `extra > xpressengine > component` 항목에 아래의 형식으로 컴포넌트 정보를 기입합니다.

```json
"component": {
  "<컴포넌트 아이디>": {
    "class": "<컴포넌트 클래스명>",
    "name": "<컴포넌트 제목>",
    "description": "<컴포넌트 설명>",
    "screenshot": "<스크린샷 경로>",
  }
}
```

`<컴포넌트 아이디>`에는 앞서 설명한 컴포넌트 아이디를 적습니다.

`<컴포넌트 클래스명>`은 컴포넌트 클래스의 풀네임을 적습니다. 컴포넌트 클래스는 반드시 autoload를 통해 로드 가능한 위치이어야 합니다.

`<컴포넌트 제목>`은 컴포넌트의 제목을 적습니다.

`<컴포넌트 설명>`에는 컴포넌트에 대한 간략한 설명을 적습니다.

`<스크린샷 경로>`에는 컴포넌트의 스크린샷 이미지 경로를 적습니다. 스크린샷 이미지는 이미 플러그인 디렉토리 안에 저장되어 있어야 합니다.


Alice 플러그인의 `composer.json` 파일입니다.
```json
{
  "name": "xpressengine-plugin/alice",
  "description": "This Package is Xpressengine Plugin - Alice Theme.",
  
  ...
  
  "extra": {
    "xpressengine": {
      "title": "Alice Theme",
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
  
  ...
  
}
```



## Register를 사용하여 등록하기