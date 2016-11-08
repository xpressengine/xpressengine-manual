# 위젯

가장 먼저 해야할 작업은 다른 컴포넌트와 마찬가지로 추상클래스인 `\Xpressengine\Widget\AbstractWidget`을 상속받는 클래스를 구현해야 합니다. 그 다음 구현한 클래스를 [XE에 등록](plugin-component.md)해 주면 됩니다.


## 클래스 생성하기

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

`renderSetting` - 사이트 관리자는 위젯 생성기를 통해 위젯(위젯코드)을 생성하게 됩니다. 위젯 생성기에서는 선택된 위젯이 필요로 하는 위젯 설정 폼이 출력되는데, 이 메소드에서 반환되는 내용이 위젯 설정 폼으로 출력됩니다.

`resolveSetting` - 위젯시스템은 사이트 관리자가 위젯 생성기에서 입력한 위젯의 설정 정보를 사용하여 위젯코드를 생성합니다. 위젯코드를 생성하기 전에 이 메소드는 사이트 관리자가 입력한 설정 정보를 건내 받고 재가공한 다음 반환합니다.


## 위젯 출력하기

### 기본적인 방법으로 출력하기

앞서 말한바와 같이, 위젯이 출력될 때에는 `render` 메소드가 사용됩니다.

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


### 위젯 스킨을 사용하여 출력하기

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

## 위젯 설정 사용하기

위젯을 출력할 때 필요로 하는 설정 정보를 사이트 관리자로부터 입력받을 수 있습니다. 사이트 관리자는 위젯 생성기에서 출력할 위젯을 선택한 다음, 각 위젯의 설정 폼에 위젯 설정 정보를 입력합니다. 위젯의 설정 폼은 `renderSetting` 메소드에서 반환하는 내용으로 출력됩니다.

### 위젯 설정폼 구현

```php
<?php
// plugins/myplugin/src/Widgets/UserInfoWidget.php

    ...
    
    public function renderSetting(array $args = [])
    {
      // 회원이름 뒤에 나오는 호칭(예: xxx님)
      return '<input type="text" name="postfix" value="'.array_get($args, 'postfix', '').'">';
    }
```

위 코드의 경우, 위젯 생성기에 하나의 텍스트박스가 출력됩니다.

### 입력된 설정 정보 처리

사이트 관리자가 설정 폼에 입력한 내용을 위젯코드로 변환하기 전에 한번 더 재가공할 수 있습니다. `resolveSetting` 메소드를 사용하십시오. 위젯시스템은 위젯코드를 생성하기 전에 이 메소드를 실행합니다. 만약 사용자가 입력한 값의 유효성 검사나 재처리가 필요하다면 이 메소드에 구현하면 됩니다.

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


### 입력된 설정 정보 저장