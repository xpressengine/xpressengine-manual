# 위젯

가장 먼저 해야할 작업은 다른 컴포넌트와 마찬가지로 추상클래스인 `\Xpressengine\Widget\AbstractWidget`을 상속받는 클래스를 구현해야 합니다. 그 다음 구현한 클래스를 [XE에 등록](plugin-component.md)해 주면 됩니다.

위젯 클래스는 기본적으로 작성해야 할 메소드가 있습니다.



```
render();

renderSetting(array $args = []);

resolveSetting(array $inputs = []);

```



