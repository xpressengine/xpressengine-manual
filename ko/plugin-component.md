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






## Register를 사용하여 등록하기