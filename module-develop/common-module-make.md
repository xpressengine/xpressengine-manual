# 모듈 생성
## 모듈 생성 방법
XE는 사이트 관리 페이지를 통해 각각의 메뉴를 정의할 수 있는 기능을 제공합니다. 메뉴의 아이템으로 등록되는 컴포넌트는 모듈로 생성하여 등록할 수 있습니다.
새로운 모듈을 생성하기 위해선 `Xpressengine\Menu\AbstractModule`을 상속받아 클래스를 구현해야 합니다. 이 클래스는 XE에서 사용되어지기 위해 다음과 같이 모듈을 지칭하는 아이디를 가져야 합니다.

```text
module/<plugin_id>@<module_id>
```