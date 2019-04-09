# UI 오브젝트 제작 방법



## UI오브젝트

UI오브젝트는 아래와 같이 매우 간단한 절차로 작동합니다.

1. PHP코드나 블레이드 파일에서 `uio($id, $arguments, $callback)` 함수 호출
2. `uio` 함수는 `$id`에 해당하는 UI오브젝트의 인스턴스를 생성\(생성시 `$arguments` 와 `$callback`을 파라미터로 전달\)
3. 생성된 인스턴스의 `render` 메소드를 호출
4. `render` 메소드가 반환하는 결과값을 출력

가장 먼저 해야할 작업은 다른 컴포넌트와 마찬가지로 추상클래스인 `\Xpressengine\UIObject\AbstractUIObject`를 상속받는 클래스를 구현해야 합니다. 그 다음 구현한 클래스를 XE에 등록해 주면 됩니다.

### 클래스 생성하기

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

위와 같이 작성한 클래스 파일을 컴포넌트를 담을 플러그인에 생성합니다. 파일의 위치는 플러그인 디렉토리 내의 어느 곳이든 상관없습니다. 다만 플러그인 디렉토리의 `src/UIObjects/ImageUIObject.php`에 생성하는 것을 권장합니다.

### 출력코드 작성하기

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

### UI오브젝트 등록하기

작성한 UI오브젝트 클래스는 다른 컴포넌트와 마찬가지로 XE에 등록해야 합니다. UI오브젝트는 `uiobject/<plugin_name>@<pure_id>` 형식의 컴포넌트 아이디를 지정해야 합니다. 여기에서는 `uiobject/myplugin@image`를 아이디로 사용하겠습니다.

등록 방법은 [컴포넌트 등록](../d50c-b7ec-adf8-c778/plugin-component.md) 문서를 참고하시기 바랍니다.

성공적으로 등록되었는지 아래 코드로 테스트해 볼 수 있습니다.

```php
uio('uiobject/myplugin@image', ['src'=>'path/to/image.jpg', 'alt'=>'test image']);
```

### alias 등록하기

위 예제에서 생성한 UI오브젝트의 아이디는 `uiobject/myplugin@image`로 꽤 복잡합니다. XE에서는 UI오브젝트에 별칭\(alias\)를 지정할 수 있습니다. 별칭이 지정된 UI오브젝트는 실제 아이디 대신 별칭을 사용할 수 있습니다. UI오브젝트의 별칭은 사이트 관리자가 `config/xe.php`의 `uiobject > aliases` 항목에 지정할 수 있으며, 코드상에서는 아래의 방법으로 지정할 수 있습니다.

```php
XeUIObject::setAlias($alias, $id);
```

위 예제에서 제작한 UI오브젝트에 `image`라는 별칭을 아래와 같이 지정할 수 있습니다.

```php
XeUIObject::setAlias('image', 'uiobject/myplugin@image');
```

위와 같이 지정한 이후부터는 별칭을 사용할 수 있습니다.

```php
uio('image', ['src'=>'path/to/image.jpg', 'alt'=>'test image']);
```

### 폼 관련 UI오브젝트

`config/xe.php`에 지정된 UI오브젝트의 별칭 목록입니다.

```text
'uiobject' => [
    'aliases' => [
        'form' => 'uiobject/xpressengine@form',
        'formText' => 'uiobject/xpressengine@formText',
        'formPassword' => 'uiobject/xpressengine@formPassword',
        'formTextarea' => 'uiobject/xpressengine@formTextArea',
        'formSelect' => 'uiobject/xpressengine@formSelect',
        'formCheckbox' => 'uiobject/xpressengine@formCheckbox',
        'formFile' => 'uiobject/xpressengine@formFile',
        'formImage' => 'uiobject/xpressengine@formImage',
        'formMenu' => 'uiobject/xpressengine@menuSelect',
        'formLangText' => 'uiobject/xpressengine@formLangText',
        'formLangTextarea' => 'uiobject/xpressengine@formLangTextArea',
        'langText' => 'uiobject/xpressengine@langText',
        'langTextArea' => 'uiobject/xpressengine@langTextArea',
        'menuType' => 'uiobject/xpressengine@menuType',
        'permission' => 'uiobject/xpressengine@permission',
        'themeSelect' => 'uiobject/xpressengine@themeSelect',
        'captcha' => 'uiobject/xpressengine@captcha',
    ]
],
```

위 별칭 목록 중에 `form*`의 형식으로 등록된 UI오브젝트가 있습니다. 이 UI오브젝트들은 의미 그대로 폼을 구성할 때 사용할 수 있는 UI오브젝트들입니다. 이 UI오브젝트들은 테마나 스킨, 위젯와 같은 컴포넌트가 사이트 관리자에게 설정 폼을 출력할 때 사용됩니다. 폼 관련 UI오브젝트의 사용법은 [폼 출력](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/service-uiobject.md) 문서에서 자세히 설명합니다.

폼 관련 UI오브젝트로 등록되는 UI오브젝트는 몇가지 규칙이 준수해야 합니다.

* 아래의 마크업 형식으로 출력해야 합니다. \(이 마크업 형식은 [bootstrap](http://getbootstrap.com/) 형식을 따르고 있습니다.\)

  ```markup
  <div class="form-group">
      <label for=""></label>
      <!-- 실제 폼 요소를 여기에 작성 -->
      <p class="help-block"></p>
  </div>
  ```

* `label`, `description` 파라미터를 받아서 처리할 수 있어야 합니다. `label`은 폼요소의 라벨 문자열입니다. `<label for=""></label>`에 출력되어야 합니다. `description`은 `<p class="help-block"></p>`에 출력될 폼 요소에 대한 설명입니다.
* `id`, `name` 필드를 파라미터로 받아서 처리할 수 있어야 합니다. 이 두 필드는 실제 폼 요소의 애트리뷰트로 지정됩니다. `id` 필드는 `label`의 `for` 애트리뷰트의 값으로도 지정해주어야 합니다.
* `values` 파라미터를 받아서 처리할 수 있어야 합니다. `values`는 폼 요소에 지정할 값\(value\)입니다. `values`의 형식은 각 폼요소마다 다릅니다.

