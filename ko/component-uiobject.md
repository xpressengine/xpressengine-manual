# UI오브젝트

UI오브젝트는 매우 간단한 구조로 되어 있습니다.

`\Xpressengine\UIObject\AbstractUIObject`를 구현해야 합니다.

`uio($id, $arguments)`

## UI오브젝트 등록

`uiobject/<plugin_name>@<pure_id>`


## 출력

`render`메소드에 구현합니다. 반드시 `return parent::render()`를 해주어야 합니다.

## alias 등록

`XeUIObject::setAlias($alias, $id)`


## 폼 UI오브젝트

`form*`의 형태로 등록된 alias에 등록된 UI오브젝트를 폼 UI오브젝트라 합니다.

반드시 `label`, `values`를 인자로 받아서 출력해야 합니다.
