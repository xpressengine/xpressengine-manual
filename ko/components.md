# 컴포넌트

컴포넌트는 **플러그인을 통해 등록할 수 있는 XE의 구성요소**를 통틀어 지칭하는 용어입니다. 여러가지 타입의 컴포넌트가 있으며 테마, 스킨, 에디터, 위젯 등이 대표적입니다. 서드파티 개발자들은 플러그인을 통해 자신이 원하는 타입의 컴포넌트를 제작하여 XE에 추가할 수 있습니다.



## 컴포넌트 타입

XE는 기본적으로 ??개 타입의 컴포넌트를 가지고 있습니다.

모든 컴포넌트는 `Xpressengine\Plugin\ComponentInterface` 인터페이스를 구현한 클래스를 가지고 있어야 합니다.



```
Xpressengine\Plugin\ComponentInterface
  ├── Xpressengine\Theme\AbstractTheme
  ...
  └── 
```



### 테마
테마에 대한 개념 설명
abstract class가 무엇인지 정도만 기술

### 스킨


### ...

