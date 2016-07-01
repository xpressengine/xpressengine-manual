# 컴포넌트

컴포넌트는 **플러그인을 통해 등록할 수 있는 XE의 구성요소**를 통틀어 지칭하는 용어입니다. 여러가지 타입의 컴포넌트가 있으며 테마, 스킨, 에디터, 위젯 등이 대표적인 컴포넌트 타입입니다.

플러그인 개발자들은 자신의 원하는 타입의 컴포넌트를 제작하여 사용할 수 있습니다. 플러그인을 하나 생성한 다음, 플러그인에 컴포넌트를 제작하여 넣으십시오. 그 다음 플러그인을 통해 컴포넌트를 XE에 등록할 수 있습니다. 

예를 들어, 테마를 제작하여 XE에 등록하고 싶을 수 있습니다. 



## 컴포넌트 타입

XE는 기본적으로 11개 타입의 컴포넌트를 가지고 있습니다. 플러그인 개발자는 11개의 타입 이외에 플러그인에서 필요한 타입을 직접 생성하여 사용할 수도 있습니다. 플러그인에서 필요한 컴포넌트 타입을 정의해 놓으면, 다른 플러그인 개발자들이 정의한 타입의 컴포넌트를 등록할 수 있습니다. 

모든 컴포넌트는 `Xpressengine\Plugin\ComponentInterface` 인터페이스를 구현한 클래스를 가지고 있어야 합니다.

```
\Xpressengine\Plugin\ComponentInterface
  ├── \Xpressengine\Theme\AbstractTheme
  ├── \Xpressengine\Module\AbstractModule
  ├── \Xpressengine\Skin\AbstractSkin
  ├── \Xpressengine\DynamicField\AbstractType
  ├── \Xpressengine\DynamicField\AbstractSkin
  ├── \Xpressengine\ToggleMenu\AbstractToggleMenu
  ├── \Xpressengine\UIObject\AbstractUIObject
  ├── \Xpressengine\Widget\AbstractWidget
  ├── \Xpressengine\Editor\AbstractEditor
  └── \Xpressengine\Editor\AbstractTool
```




### 테마
테마에 대한 개념 설명
abstract class가 무엇인지 정도만 기술

### 스킨


### ...

