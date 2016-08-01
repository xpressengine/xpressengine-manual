# UI오브젝트

UI오브젝트는 아래와 매우 간단한 구조로 작동합니다. 

1. PHP코드나 블레이드 파일에서 `uio($id, $arguments, $callback)` 함수 호출
2. `uio` 함수는 `$id`에 해당하는 UI오브젝트의 인스턴스를 생성(생성시 `$arguments` 와 `$callback`을 파라미터로 전달)
3. 생성된 인스턴스의 `render` 메소드를 호출
3. `render` 메소드가 반환하는 결과값을 출력

가장 먼저 해야할 작업은 다른 컴포넌트와 마찬가지로 추상클래스인 `\Xpressengine\UIObject\AbstractUIObject`를 상속받는 클래스를 구현해야 합니다. 그 다음 구현한 클래스를 XE에 등록해 주면 됩니다.

## 클래스 생성

이 문서에서는 UI오브젝트의 작성법을 쉽게 이해할 수 있도록 '이미지 UI오브젝트'를 예로 들어 설명하겠습니다. 이미지 UI오브젝트는 이미지 파일 주소를 입력받아 `<img src="이미지 파일 주소">` 형태로 출력합니다.
```php
<?php
namespace MyPlugin\UIObjects;

class ImageUIObject extends AbstractUIObject
{
    public function render()
    {
      // implement code
      
      return parent::render();
    }
}
```



## 출력

`render`메소드에 구현합니다. 반드시 `return parent::render()`를 해주어야 합니다.


## UI오브젝트 등록

`uiobject/<plugin_name>@<pure_id>`


## alias 등록

`XeUIObject::setAlias($alias, $id)`


## 폼 UI오브젝트

`form*`의 형태로 등록된 alias에 등록된 UI오브젝트를 폼 UI오브젝트라 합니다.

반드시 `label`, `values`를 인자로 받아서 출력해야 합니다.
