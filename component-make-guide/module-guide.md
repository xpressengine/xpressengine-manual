# 모듈 제작 방법

XE는 사이트 관리 페이지를 통해 각각의 메뉴를 정의할 수 있는 기능을 제공합니다. 메뉴의 아이템으로 등록되는 컴포넌트는 모듈로 생성하여 등록할 수 있습니다.

## 모듈 생성 <a id="undefined"></a>

새로운 모듈을 생성하기 위해선 `Xpressengine\Menu\AbstractModule`을 상속받아 클래스를 구현해야 합니다. 이 클래스는 XE에서 사용되어지기 위해 다음과 같이 모듈을 지칭하는 아이디를 가져야 합니다.

```text
module/<plugin_id>@<module_id>
```

### 모듈 구조 <a id="undefined-1"></a>

모듈은 메뉴에 등록, 수정 또는 삭제처리를 위한 메서드를 구현해야 합니다.

```php
use Xpressengine\Menu\AbstractModule;

class MyModule extends AbstractModule
{
  public function createMenuForm()
  {
    ...
  }

  public function storeMenu($instanceId, $menuTypeParams, $itemParams)
  {
    ...
  }

  public function editMenuForm($instanceId)
  {
    ...
  }

  public function updateMenu($instanceId, $menuTypeParams, $itemParams)
  {
    ...
  }

  public function summary($instanceId)
  {
    ...
  }

  public function deleteMenu($instanceId)
  {
    ...
  }

  public function getTypeItem($id)
  {
    ...
  }
}
```

`createMenuForm`와 `editMenuForm` 메서드는 메뉴를 생성 또는 수정할 때, 모듈이 필요로 하는 값을 사이트 관리자로부터 입력받기 위한 폼을 반환하는 메서드입니다. 이 폼에 의해 입력된 값은 메뉴를 등록하거나 수정할 때 호출되는 `storeMenu`, `updateMenu` 메서드의 `$menuTypeParams` 파라미터로 전달됩니다.

메뉴가 삭제될 때에는 `deleteMenu` 메서드를 호출하게 되는데, 만약 삭제를 처리하기 전 사용자에게 알려야 할 메시지가 있다면 `summary` 메서드에 작성하십시오. 해당 메시지가 사용자에게 노출됩니다.

때때로 XE의 기능요소들이 메뉴를 통해 특정 모듈에 속한 객체 아이템에 접근하고자 하는 경우가 있습니다. `getTypeItem`는 이럴때 모듈의 객체 아이템을 반환하기 위해 정의된 메서드입니다.

### 라우트 <a id="undefined-2"></a>

메뉴는 대부분 각각의 메뉴아이템에 연결된 주소로의 이동을 위해 라우트를 가집니다. 그러나 외부링크와 같은 특별한 경우에는 라우트를 가지지 않기도 합니다. 이럴 때는 `isRouteAble` 메서드를 통해 `false` 값을 반환해야 합니다.

```php
class MyModule extends AbstractModule
{
  ...
  public static function isRouteAble()
  {
    return false;
  }
  ...
}
```

### 설정페이지 <a id="undefined-3"></a>

모듈은 다른 컴포넌트와 마찬가지로 `getSettingsURI` 메서드를 통해 설정페이지 주소를 반환합니다. 하지만 모듈은 각각의 메뉴의 아이템으로서 생성되어지면서 인스턴스를 가지게 됩니다. 이때 각 인스턴스별로 설정페이지가 존재할 수 있습니다. 이럴땐 `getInstanceSettingURI` 메서드를 통해 각 인스턴스별 설정페이지 주소를 반환해야 합니다.

```php
class MyModule extends AbstractModule
{
  ...
  public static function getInstanceSettingURI($instanceId)
  {
    return route('mypluing.mymodule.setting', $instanceId);
  }
  ...
}
```

[  
](https://xpressengine.gitbook.io/xpressengine-manual/ko/cef4-d3ec-b10c-d2b8-c81c-c791-ac00-c774-b4dc/component-widget)

