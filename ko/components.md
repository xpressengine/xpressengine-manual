# 컴포넌트

컴포넌트는 **플러그인을 통해 등록할 수 있는 XE의 구성요소**를 통틀어 지칭하는 용어입니다. 여러가지 타입의 컴포넌트가 있으며 테마, 스킨, 에디터, 위젯 등이 대표적입니다. 각 타입의 컴포넌트들간의 별다른 공통점은 없습니다. 오직 서드파티 개발자들로부터 등록 받아 XE에 추가될 수 있다는 점이 유일한 공통점입니다.

플러그인 개발자들은 컴포넌트를 제작하여 XE에 등록할 수 있습니다. 만약 자신만의 컴포넌트를 만들고 사용하고싶다면 우선, 플러그인을 하나 생성하십시오. 그 다음 플러그인에 컴포넌트를 제작하여 넣고, 플러그인을 활성화하면 자신이 제작한 컴포넌트를 XE에서 사용할 수 있습니다. 

예를 들어 새로운 테마를 만들어서 자신의 사이트에 적용하고 싶다면, 먼저 빈 플러그인을 하나 생성합니다. 그 다음 플러그인 안에서 테마 컴포넌트를 만듭니다. 만약 테마와 함께 게시판 스킨과 위젯도 만들어서 사이트에 적용하고 싶다면, 이 두가지 컴포넌트도 제작하여 같은 플러그인에 추가합니다. 플러그인을 활성화하면 플러그인에 포함된 컴포넌트들을 XE에서 모두 사용할 수 있습니다.

> XE1에서는 각 컴포넌트를 따로 XE에 추가해야 했습니다. XE3에서는 하나의 플러그인에 컴포넌트를 모두 포함하에 한번에 추가할 수 있습니다. 배포할 때에도 마찬가지로 각 컴포넌트를 따로 배포하지 않고, 플러그인 하나에 담아서 배포하면 됩니다.

## 컴포넌트 인터페이스

모든 컴포넌트는 `Xpressengine\Plugin\ComponentInterface` 인터페이스를 구현한 클래스를 가지고 있어야 합니다.
이 인터페이스는 각 컴포넌트의 기본적인 정보를 

## 컴포넌트 타입

XE는 기본적으로 11개 타입의 컴포넌트를 가지고 있습니다. 각 타입의 컴포넌트는 모두 `\Xpressengine\Plugin\ComponentInterface` 클래스를 구현한 추상클래스를 정의하고 있습니다.

```
\Xpressengine\Plugin\ComponentInterface
  ├── 테마 - \Xpressengine\Theme\AbstractTheme
  ├── 모듈 - \Xpressengine\Module\AbstractModule
  ├── 스킨 - \Xpressengine\Skin\AbstractSkin
  ├── 다이나믹필드 타입 - \Xpressengine\DynamicField\AbstractType
  ├── 다이나믹필드 스킨 - \Xpressengine\DynamicField\AbstractSkin
  ├── 토글메뉴 - \Xpressengine\ToggleMenu\AbstractToggleMenu
  ├── UI오브젝트 - \Xpressengine\UIObject\AbstractUIObject
  ├── 위젯 - \Xpressengine\Widget\AbstractWidget
  ├── 에디터 - \Xpressengine\Editor\AbstractEditor
  └── 에디터툴 - \Xpressengine\Editor\AbstractTool
```

플러그인 개발자는 11개의 타입 이외에 플러그인에서 필요한 타입을 직접 생성하여 사용할 수도 있습니다. 플러그인에서 필요한 컴포넌트 타입을 정의해 놓으면, 다른 플러그인 개발자들이 정의한 타입의 컴포넌트를 제작하여 등록할 수 있습니다. 



### 테마
테마에 대한 개념 설명
abstract class가 무엇인지 정도만 기술

### 스킨


### ...

