# UI오브젝트

UI오브젝트는 아래와 매우 간단한 구조로 작동합니다. 

1. PHP코드나 블레이드 파일에서 `uio($id, $arguments, $callback)` 함수 호출
2. `uio` 함수는 `$id`에 해당하는 UI오브젝트의 인스턴스를 생성(생성시 `$arguments` 와 `$callback`을 파라미터로 전달)
3. 생성된 인스턴스의 `render` 메소드를 호출
3. `render` 메소드가 반환하는 결과값을 출력

가장 먼저 해야할 작업은 다른 컴포넌트와 마찬가지로 추상클래스인 `\Xpressengine\UIObject\AbstractUIObject`를 상속받는 클래스를 구현해야 합니다. 그 다음 구현한 클래스를 XE에 등록해 주면 됩니다.

## 클래스 생성

이 문서에서는 UI오브젝트의 작성법을 쉽게 이해할 수 있도록 '이미지 UI오브젝트'를 예로 들어 설명하겠습니다. 이미지 UI오브젝트는 이미지 파일 주소를 입력받아 `<img src="이미지 파일 주소">` 형태로 출력하는 간단한 역할을 합니다.

```php
<?php
// plugins/myplugin/src/UIObjects/ImageUIObject.php
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

위와 같은 클래스 파일을 컴포넌트를 담을 플러그인에 생성합니다. 파일의 위치는 플러그인 디렉토리 내의 어느 곳이든 상관없습니다. 다만 플러그인 디렉토리의 `src/UIObjects/ImageUIObject.php`에 생성하는 것을 권장합니다. 


## 출력

클래스를 생성하였다면, `render` 메소드를 구현합니다. 

`render` 메소드에서는 입력된 파라미터를 사용하여 출력할 html 스트링을 생성한 다음, `$this->template` 변수에 저장합니다.

```php
    public function render()
    {
      // 입력값 가져오기
      $src = $this->arguments['src'];
      $alt = $this->arguments['alt'];
      
      // 이미지 태그 생성
      $this->template = '<img src="'.$src.'" alt="'.$alt.'">';
      
      // 출력
      return parent::render();
    }
```

마지막으로는 반드시 `return parent::render()`를 해주어야 합니다. `parent::render()` 메소드는 `$callback` 입력이 있는지 검사하고, `$callback`를 자동으로 실행해주는 역할을 합니다.

## UI오브젝트 등록

작성한 UI오브젝트 클래스는 다른 컴포넌트와 마찬가지로 XE에 등록해야 합니다. UI오브젝트는 `uiobject/<plugin_name>@<pure_id>` 형식의 컴포넌트 아이디를 지정해야 합니다. 여기에서는 `uiobject/myplugin@image`를 아이디로 사용하겠습니다.

등록 방법은 [컴포넌트 등록](plugin-component.md) 문서를 참고하시기 바랍니다.

성공적으로 등록되었다면, 아래 코드로 테스트해 볼 수 있습니다.

```php
uio('uiobject/myplugin@image', ['src'=>'path/to/image.jpg', 'alt'=>'test image']);
```


## alias 등록

...
`XeUIObject::setAlias($alias, $id)`


## 폼 UI오브젝트

`form*`의 형태로 등록된 alias에 등록된 UI오브젝트를 폼 UI오브젝트라 합니다.

반드시 `label`, `values`를 인자로 받아서 출력해야 합니다.
