# 토글 메뉴 제작 방법

## 토글메뉴

부가적으로 필요한 기능들을 드롭박스로 호출할 수 있도록 토글메뉴를 사용할 수 있습니다. 예를 들어 게시글 페이지에서 게시글 작성자를 클릭하면 프로필을 보는 메뉴나, 회원의 관리페이지로 넘어갈 수 있는 토글메뉴가 우측에 나타나고, 공유아이콘을 클릭하면 페이스북에 공유하기, 라인에 공유하기, 트위터에 공유하기와 같은 토글메뉴 드롭박스에 나타나게 할 수 있습니다.

토글메뉴는 형태에 따라 3가지 타입을 지원합니다.

* MENUTYPE\_EXEC
* MENUTYPE\_LINK
* MENUTYPE\_RAW 

### 토글메뉴 생성하기

추가하고자 하는 새로운 토글메뉴 클래스에서 `AbstractToggleMenu` 를 상속 받습니다. 그리고 명시된 추상메서드를 구현합니다.

```php
<?php
// plugins/myplugin/src/ToggleMenus/ToggleItem.php
namespace MyPlugin\ToggleMenus;

class ToggleItem extends AbstractToggleMenu
{
    public function getText()
    {
      return 'string';
    }

    public function getType()
    {
      // implement code

      return static::MENUTYPE_RAW
    }

    public function getAction()
    {
      return 'do_something()';
    }

    public function getScript()
    {
      return 'js_file_directory';
    }
}
```

* `getText`: 메뉴가 펼쳐졌을때 보여지게될 문자열입니다.
* `getType`: `MENUTYPE_EXEC`, `MENUTYPE_LINK`, `MENUTYPE_RAW` 세 가지 중 한가지를 반환해야 합니다.
* `getAction`: 해당 메뉴를 클릭했을때 실행 되어질 js 문자열입니다. 만약 타입이 `raw` 인 경우 html 이 반환되어야 합니다.
* `getScript`: 메뉴의 동작을 위해 특정 js 파일이 필요한 경우 해당 파일의 경로를 반환해 줍니다.

#### exec

`exec` 타입은 `getAction` 에 의해 반환된 문자열이 그 자체로 js 로 실행 가능한 형태를 가집니다. 특정 함수를 실행하는 경우 함수에서 필요로하는 인자는 해당 토글메뉴내에서 제공되어야 합니다.

```text
public function getType()
{
  return static::MENUTYPE_EXEC;
}

public function getAction()
{
  return 'alert("hello")';
}
```

#### link

`link` 타입은 클릭시 다른페이지로 이동하는 메뉴입니다.

```text
public function getType()
{
  return static::MENUTYPE_LINK;
}

public function getAction()
{
  return '/';
}
```

#### raw

`raw` 타입은 메뉴에 표현될 형태부터 실행될 방식까지 직접 지정하여 사용하는 방식입니다.

```text
public function getType()
{
  return static::MENUTYPE_RAW;
}

public function getAction()
{
  return '<a href="#" onclick="method('argument')">Raw메뉴</a>';
}
```

### 토글메뉴 등록

#### 컴포넌트 아이디

컴포넌트 아이디는 아래와 같은 규칙으로 작성합니다.

```text
<type>/toggleMenu/<plugin>@<name>
```

`type`은 토글메뉴를 사용할 타입의 아이디 입니다. \(ex - `user`, `module/board@board`\)  
`plugin`은 토글메뉴가 소속될 플러그인 디렉토리 이름입니다.  
`name`은 토글메뉴의 고유한 아이디 입니다.

#### 컴포넌트 등록

composer.json 파일에 다음과 같이 입력합니다.

```javascript
// plugins/my_plugin/composer.json
...
  "extra": {
        "xpressengine": {
            "title": "my plugin",
            "component": {
                "my_plugin@my_field/toggleMenu/my_plugin@toggle_item": {
                    "class": "GilDongHong\\XePlugin\\MyPlugin\\ToggleMenus\\ToggleItem",
                    "name": "toggle_item ToggleItem",
                    "description": "The toggleMenu supported by My_plugin plugin."
                }
            }
        }
    },

    ...
```

### 토글메뉴 띄우기

type을 통해 등록된 토글메뉴를 불러올 수 있습니다. 가장 직관적인 방법은 ToggleMenuHandler의 getItems 메쏘드를 사용하는 것입니다.

```text
app('xe.toggleMenu')->getItems('user');

//[
//     "user/toggleMenu/xpressengine@profile" => App\ToggleMenus\User\ProfileItem,
//     "user/toggleMenu/xpressengine@manage" => App\ToggleMenus\User\ManageItem,
//]
```

해당 함수는 user를 `$type`으로 삼은 ToggleMenu 아이템들을 리턴합니다.  
타입에 따라서 모듈과 같이 instance를 사용하는 경우 두 번째 파라미터로 `$instanceId`,  
User 모델과 같이 고유한 키값을 가지는 경우 세 번째 파라미터로 `$identifier`를 받습니다.  
각 파라미터는 ToggleMenu의 getAction에서 받아서 처리합니다.

#### Frontend

XE는 Frontend에서 ToggleMenu 아이템들을 로드할 수 있는 방법을 이미 준비했습니다.  
`<a>`태그 안에 몇 가지 속성을 주는 것만으로 해당 type에서 필요한 ToggleMenu를  
`<li>...</li>`태그에 담아 렌더링할 수 있습니다.

`data-toggle` "xe-page-toggle-menu"를 삽입합니다.  
`data-url` 를 넣어 toggleMenu 리스트를 동적으로 호출할 url을 삽입합니다.  
`data-data` 사용할 type과 해당 type이 필요로 하는 파라미터를 추가로 넣어줍니다.\($identifier의 키값을 id로 받고 있는 것을 확인합니다.\)  
`data-side` 렌더링된 리스트가 해당 태그 어느 방향에 위치할지 결정합니다.

아래의 예제는 board플러그인의 모듈을 target으로 삼은 targetMenu를 호출하는 태그입니다.

```text
<a href="#" 
   data-toggle="xe-page-toggle-menu"
   data-url="{{route('toggleMenuPage')}}"
   data-data='{!! json_encode(['id'=>$item->id,'type'=>'module/board@board','instanceId'=>$item->instance_id]) !!}'
   data-side="dropdown-menu-right">
   {{$text}}
</a>
```

### 토글메뉴 관리

관리자가 해당 타입에 등록되어있는 토글메뉴를 사용할지 안할지를 결정할 수 있도록 화면을 띄워줄 수 있습니다.

```text
{!! new ToggleMenuSection($type) !!}
```

위 예제는 $type을 사용하는 토글메뉴들의 리스트를 on/off할 수 있는 토글버튼과 함께 렌더링합니다. 저장 버튼을 누르면 ToggleMenuHandler를 통해 config에 설정정보를 저장합니다.

