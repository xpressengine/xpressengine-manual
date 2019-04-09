# 위젯 제작 방법

가장 먼저 해야할 작업은 다른 컴포넌트와 마찬가지로 추상클래스인 `\Xpressengine\Widget\AbstractWidget`을 상속받는 클래스를 구현해야 합니다. 그 다음 구현한 클래스를 [XE에 등록](https://xpressengine.gitbook.io/xpressengine-manual/ko/d50c-b7ec-adf8-c778/plugin-component)해 주면 됩니다.

## 클래스 생성하기 <a id="undefined"></a>

이 문서에서는 위젯 작성법을 쉽게 이해할 수 있도록 '로그인 정보 위젯'을 예로 들어 설명하겠습니다. 로그인 정보 위젯은 현재 로그인되어 있는 회원의 정보를 출력하는 간단한 위젯입니다.

```php
<?php
// plugins/myplugin/src/Widgets/UserInfoWidget.php
namespace MyPlugin\Widgets;

class UserInfoWidget extends \Xpressengine\Widget\AbstractWidget
{
    public function render()
    {
    }

    public function renderSetting(array $args = [])
    {
    }

    public function resolveSetting(array $inputs = [])
    {
    }
}
```

위와 같이 작성한 클래스 파일을 컴포넌트를 담을 플러그인에 생성합니다. 파일의 위치는 플러그인 디렉토리 내의 어느 곳이든 상관없습니다. 다만 플러그인 디렉토리의 `src/Widgets/UserInfoWidget.php`에 생성하는 것을 권장합니다.

위젯 클래스는 기본적으로 작성해야 할 메소드가 있습니다.

`render` - 위젯을 출력할 때 호출되는 메소드입니다.

`renderSetting` - 위젯변수를 입력받기 위한 폼을 출력합니다. 위젯변수는 위젯을 출력할 때 필요한 컨텐츠에 대하여 사이트 관리자로부터 입력받는 값을 말합니다. 사이트 관리자가 위젯 생성기를 통해 위젯\(위젯코드\)을 생성할 때, 위젯 생성기는 이 메소드를 실행하여 위젯 변수 입력폼을 출력합니다.

`resolveSetting` - 사이트 관리자가 위젯 생성기에서 입력한 위젯 변수를 조합하여 위젯코드를 생성합니다. 이 메소드는 입력받은 위젯 변수를 파라미터로 전달 받은 다음, 위젯 변수를 한번 재가공하여 반환합니다.

## 위젯 등록하기 <a id="undefined-1"></a>

작성한 위젯 클래스는 다른 컴포넌트와 마찬가지로 XE에 등록해야 합니다. 위젯은 `widget/<plugin_name>@<pure_id>` 형식의 컴포넌트 아이디를 지정해야 합니다. 여기에서는 `widget/myplugin@userinfo`를 아이디로 사용하겠습니다.

등록 방법은 [컴포넌트 등록](https://xpressengine.gitbook.io/xpressengine-manual/ko/d50c-b7ec-adf8-c778/plugin-component) 문서를 참고하시기 바랍니다.

성공적으로 등록되었는지 아래 코드로 테스트해 볼 수 있습니다.

```text
<xewidget id="widget/myplugin@userinfo"></xewidget>
```

## 위젯 출력하기 <a id="undefined-2"></a>

### 기본적인 방법으로 출력하기 <a id="undefined-4"></a>

앞서 말한 바와 같이, 위젯이 출력될 때에는 `render` 메소드가 사용됩니다.

```php
<?php
// plugins/myplugin/src/Widgets/UserInfoWidget.php
namespace MyPlugin\Widgets;

class LoginInfoWidget extends \Xpressengine\Widget\AbstractWidget
{
    public function render()
    {
      // 로그인 상태일 경우, 로그인 회원의 이름이 출력
      if(auth()->check()) {
        return auth()->user()->getDisplayName();
      } else {
        return '로그인 상태가 아닙니다'
      }
    }
}
```

로그인 회원의 이름을 출력하거나 '로그인 상태가 아닙니다' 메시지를 출력하도록 작성된 코드입니다. 이 예제에서는 간단히 문자열을 반환했습니다. 좀 더 복잡한 위젯일 경우 블레이드 템플릿을 사용할 수도 있습니다.

### 위젯 스킨을 사용하여 출력하기 <a id="undefined-5"></a>

위젯은 스킨시스템을 기본적으로 지원합니다. 동일한 컨텐츠를 출력하는 위젯이라고 해도, 사이트의 디자인이나 테마에 따라 다른 스킨을 선택하여 출력할 수 있습니다.

```php
<?php
// plugins/myplugin/src/Widgets/UserInfoWidget.php

    ...

    public function render()
    {
      $data = [
        'isLogged' => auth()->check(),
        'user' => auth()->user()
      ];
      return $this->renderSkin($data);
    }
```

위 코드에서와 같이 `renderSkin`을 사용하면 됩니다. `renderSkin` 메소드는 위젯코드에 지정된 스킨정보를 자동으로 가져온 다음, 스킨을 적용하여 출력합니다.

## 위젯 변수 사용하기 <a id="undefined-3"></a>

사이트 관리자로부터 위젯 변수를 입력받기 위해 위젯 생성기는 위젯 변수 입력폼을 출력합니다. 위젯 변수를 사용하려면 위젯 변수 입력폼을 먼저 작성해야 합니다.

### 위젯 설정폼 구현 <a id="undefined-6"></a>

위젯 클래스의 `renderSetting` 메소드가 위젯 변수 입력폼을 출력하도록 하십시오.

```php
<?php
// plugins/myplugin/src/Widgets/UserInfoWidget.php

    ...

    public function renderSetting(array $args = [])
    {
      // 회원이름 뒤에 나오는 호칭(예: xxx님)
      return uio('formText', ['name'=>'postfix', 'value'=>array_get($args, 'postfix'), 'label'=>'호칭', 'description'=>'회원이름 뒤에 출력될 호칭을 적어주세요']);
    }
```

위 코드의 경우, 위젯 생성기에 하나의 텍스트박스가 출력됩니다. 텍스트박스를 출력하기 위해 UI오브젝트를 사용하고 있습니다.

### 입력된 설정 정보 처리 <a id="undefined-7"></a>

사이트 관리자가 위젯 변수 입력폼에 입력한 내용을 위젯코드로 변환하기 전에 한번 더 재가공할 수 있습니다. `resolveSetting` 메소드를 사용하십시오. 위젯시스템은 위젯코드를 생성하기 전에 이 메소드를 실행합니다. 만약 사용자가 입력한 위젯 변수의 유효성 검사나 재처리가 필요하다면 이 메소드에 구현하면 됩니다.

```php
<?php
// plugins/myplugin/src/Widgets/UserInfoWidget.php

    ...

    public function resolveSetting(array $inputs = [])
    {
      if(!array_get($inputs, 'postfix'))
      {
        throw new ValidationException();
      }

      return $input;
    }
```

### 위젯을 출력할 때 위젯 변수 사용하기 <a id="undefined-8"></a>

사이트 관리자가 입력한 위젯 변수는 `render` 메소드에서 바로 사용할 수 있습니다. 위젯변수는 `$this->config` 배열에 저장되어 있습니다.

```php
<?php
// plugins/myplugin/src/Widgets/UserInfoWidget.php

    ...

    public function render()
    {
        // 로그인 상태일 경우, 로그인 회원의 이름이 출력
        if(auth()->check()) {
          return auth()->user()->getDisplayName().array_get($this->config, 'postfix');
        } else {
          return '로그인 상태가 아닙니다'
        }
    }
}
```

[  
](https://xpressengine.gitbook.io/xpressengine-manual/ko/cef4-d3ec-b10c-d2b8-c81c-c791-ac00-c774-b4dc/component-skin)

