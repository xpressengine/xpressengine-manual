# 사이트 관리 페이지 추가하기

XE는 사이트 관리자 또는 관리 등급을 가진 회원만 접근할 수 있는 '사이트 관리 영역'을 가지고 있습니다. 사이트 관리 영역은 사이트 관리에 필요한 다양한 '사이트 관리 페이지'들로 구성되어 있습니다. 사이트 관리 영역은 일반적으로 `http://<도메인>/settings`로 접근할 수 있습니다.


플러그인도 사이트 관리 영역에 플러그인을 위한 페이지를 추가할 수 있습니다.


## 메뉴 등록

사이트 관리 영역의 화면 좌측에는 관리 메뉴 트리가 출력됩니다. 플러그인은 이 트리에 메뉴를 추가할 수 있습니다. `XeRegister` 파사드의 `push` 메소드를 사용하여 `settings/menu`에 메뉴 정보를 등록합니다.

```php
\XeRegister::push('settings/menu', $menuId, $menuInfo);
```

아래 코드는 사이트 관리 메뉴의 `컨텐츠` 메뉴 하위에 서브 메뉴로 `게시판` 메뉴를 추가하는 예제입니다.

```php
\XeRegister::push('settings/menu', 'contents.board', [
    'title' => '게시판',
    'display' => true,
    'description' => '',
    'ordering' => 2000,
]);
```

첫번째 파라미터는 항상 `settings/menu`를 써야합니다.

두번째 파라미터는 메뉴의 아이디인 동시에 메뉴의 부모 메뉴를 지정하는 역할을 합니다. 부모메뉴의 아이디와 현재 메뉴의 아이디를 점(.)을 사용하여 연결해주십시오. 위 예제의 경우 `contents` 메뉴 하위에 `board` 메뉴를 추가합니다.

세번째 파라미터는 메뉴의 상세정보를 담은 배열입니다.

- `title`은 메뉴가 출력될 때 사용하는 메뉴의 이름입니다.

- `display`가 `true`이면 메뉴가 메뉴 트리에 출력됩니다. `false`의 경우 메뉴 트리에 출력되지 않습니다. 예를 들어 `회원 정보 수정`과 같은 메뉴는 메뉴 트리에는 출력되지 않는 숨김 메뉴입니다. 사이트 관리페이지의 상단에는 빵조각(breadcrumb)으로 현재 관리페이지의 위치를 표시해주는데, 이때 숨김 메뉴를 등록해 놓으면 유용합니다.

- `description`은 메뉴에 대한 설명입니다. 사이트 관리페이지의 상단에 출력됩니다.

- `ordering`은 메뉴가 출력되는 순서를 지정할 때 사용됩니다. 같은 레벨의 메뉴들이 출력될 때 `ordering`이 작은 메뉴부터 출력됩니다.
 
## 관리 페이지 등록(라우트 등록)

관리 페이지를 등록하는 것은 결국 라우트를 등록하는 것을 뜻합니다. 관리 페이지를 위한 라우트를 등록하는 방법은 일반 웹페이지의 라우트를 등록하는 방법과 크게 다르지 않습니다. 다만, `Route::group` 메소드 대신 `Route::settings` 메소드를 사용하면 됩니다. 

```php
  Route::settings($uri, function() {
    Route::get('/', ...);
    Route::post('/', ...);
  });
```

사이트 관리 영역에 속하는 모든 페이지의 URL은 모두 첫번째 세그먼트로 `settings`를 가지게 됩니다. 라우트를 등록하는 코드는 플러그인 클래스의 `boot` 메소드에 등록하십시오.

```php
<?php
// plugins/my_plugin/plugin.php
namespace MyPlugin;

use Xpressengine\Plugin\AbstractPlugin;
use Route;
use Presenter;

class Plugin extends AbstractPlugin
{
  public function boot()
  {
      // 사이트 관리 페이지 추가
      // http://<domain>/settings/my_plugin url로 접근 가능
      Route::settings(static::getId(), function() {
        Route::get('/', function(){
          return Presenter::make(static::view('views.settings');
        }
      });
  }
  
}
```

사이트 관리 페이지들이 출력될 때에는 사이트 관리 영역용 테마를 적용한 후 출력돼야 합니다. `Presenter`를 사용하여 결과를 반환하십시오. 자동으로 사이트 관리 영역용 테마가 적용됩니다.



### 라우트에 메뉴 연결하기

사이트 관리 페이지의 라우트를 등록할 때, 라우트에 메뉴를 지정할 수 있습니다. 아래 코드는 회원 추가 페이지에 메뉴를 지정하는 예제입니다.

```php
Route::settings('user', function () {
    Route::get('create', [
           'as' => 'settings.member.create',
           'uses' => 'Member\Settings\UserController@create',
           'settings_menu' => 'member.create'
    ]);
});
```

라우트를 등록할 때, 두번째 파라미터 배열의 `settings_menu` 필드에 메뉴아이디를 지정하면 됩니다. 위 예제의 경우 `member.create` 메뉴를 라우트와 연결하고 있습니다. `member.create` 메뉴는 이미 아래와 같이 등록된 상태여야 합니다.

```php
\XeRegister::push('settings/menu', 'member.create', [
    'title' => '새 회원 추가',
    'description' => '신규회원을 추가합니다.',
    'display' => false,
    'ordering' => 200
]);
```




## 권한 추가


## 컨트롤러 구현