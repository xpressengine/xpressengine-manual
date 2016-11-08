# 위젯

가장 먼저 해야할 작업은 다른 컴포넌트와 마찬가지로 추상클래스인 `\Xpressengine\Widget\AbstractWidget`을 상속받는 클래스를 구현해야 합니다. 그 다음 구현한 클래스를 [XE에 등록](plugin-component.md)해 주면 됩니다.


## 클래스 생성하기

이 문서에서는 위젯 작성법을 쉽게 이해할 수 있도록 '로그인 정보 위젯'을 예로 들어 설명하겠습니다. 로그인 정보 위젯은 현재 로그인되어 있는 회원의 정보를 출력하는 간단한 위젯입니다.

```php
<?php
// plugins/myplugin/src/Widgets/LoginInfoWidget.php
namespace MyPlugin\Widgets;

class LoginInfoWidget extends \Xpressengine\Widget\AbstractWidget
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

위와 같이 작성한 클래스 파일을 컴포넌트를 담을 플러그인에 생성합니다. 파일의 위치는 플러그인 디렉토리 내의 어느 곳이든 상관없습니다. 다만 플러그인 디렉토리의 `src/Widgets/LoginInfoWidget.php`에 생성하는 것을 권장합니다. 

위젯 클래스는 기본적으로 작성해야 할 메소드가 있습니다.

`render` - 위젯을 출력할 때 호출되는 메소드입니다.

`renderSetting` - 사이트 관리자는 위젯 생성기를 통해 위젯(위젯코드)을 생성하게 됩니다. 위젯 생성기에서는 선택된 위젯이 필요로 하는 위젯 설정 폼이 출력되는데, 이 메소드에서 반환되는 내용이 위젯 설정 폼으로 출력됩니다.

`resolveSetting` - 위젯시스템은 사이트 관리자가 위젯 생성기에서 입력한 위젯의 설정 정보를 사용하여 위젯코드를 생성합니다. 위젯코드를 생성하기 전에 이 메소드는 사이트 관리자가 입력한 설정 정보를 건내 받고 재가공한 다음 반환합니다.


## 위젯 출력하기

앞서 말한바와 같이, 위젯이 출력될 때에는 `render` 메소드가 사용됩니다.

```php
<?php
// plugins/myplugin/src/Widgets/LoginInfoWidget.php
namespace MyPlugin\Widgets;

class LoginInfoWidget extends \Xpressengine\Widget\AbstractWidget
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