# 관리자 사이트 메뉴 추가 방법

XE는 사이트 관리자 또는 관리 등급을 가진 회원만 접근할 수 있는 '사이트 관리 영역'을 가지고 있습니다. 사이트 관리 영역은 사이트 관리에 필요한 다양한 '사이트 관리 페이지'들로 구성되어 있습니다. 사이트 관리 영역은 일반적으로 `http://<도메인>/settings`로 접근할 수 있습니다.

플러그인도 사이트 관리 영역에 플러그인을 위한 관리페이지를 추가할 수 있습니다.

사이트 관리 페이지를 추가할 때에는 크게 세가지 작업이 필요합니다.

* 페이지\(라우트\) 등록
* 사이트 관리 메뉴 등록 및 페이지와 연결
* 사이트 관리 권한 등록 및 페이지와 연결

세가지 작업에 대하여 자세히 살펴보겠습니다.

## 페이지 등록\(라우트 등록\)

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

첫번째 파라미터는 항상 `settings/menu`를 지정하십시오.

두번째 파라미터는 메뉴의 아이디인 동시에 메뉴의 부모 메뉴를 지정하는 역할을 합니다. 부모메뉴의 아이디와 현재 메뉴의 아이디를 점\(.\)을 사용하여 연결해주십시오. 위 예제의 경우 `contents` 메뉴 하위에 `board` 메뉴를 추가합니다.

세번째 파라미터는 메뉴의 상세정보를 담은 배열입니다.

* `title`은 메뉴가 출력될 때 사용하는 메뉴의 이름입니다.
* `display`가 `true`이면 메뉴가 메뉴 트리에 출력됩니다. `false`의 경우 메뉴 트리에 출력되지 않습니다. 예를 들어 `회원 정보 수정`과 같은 메뉴는 메뉴 트리에는 출력되지 않는 숨김 메뉴입니다. 사이트 관리페이지의 상단에는 빵조각\(breadcrumb\)으로 현재 관리페이지의 위치를 표시해주는데, 이때 숨김 메뉴를 등록해 놓으면 유용합니다.
* `description`은 메뉴에 대한 설명입니다. 사이트 관리페이지의 상단에 출력됩니다.
* `ordering`은 메뉴가 출력되는 순서를 지정할 때 사용됩니다. 같은 레벨의 메뉴들이 출력될 때 `ordering`이 작은 메뉴부터 출력됩니다.

### 페이지에 메뉴 연결하기

사이트 관리 페이지\(라우트\)를 등록할 때, 페이지에 해당하는 메뉴를 지정할 수 있습니다. 아래 코드는 회원 추가 페이지에 메뉴를 지정하는 예제입니다.

```php
// member.create 등록
\XeRegister::push('settings/menu', 'user.create', [
    'title' => '새 회원 추가',
    'description' => '신규회원을 추가합니다.',
    'display' => false,
    'ordering' => 200
]);
```

```php
// settings_menu 지정
Route::settings('user', function () {
    Route::get('create', [
           'as' => 'settings.member.create',
           'uses' => 'Member\Settings\UserController@create',
           'settings_menu' => 'user.create'
    ]);
});
```

라우트를 등록할 때, 두번째 파라미터 배열의 `settings_menu` 필드에 메뉴 아이디를 지정하면 됩니다. 위 예제의 경우 `member.create` 메뉴를 라우트와 연결하고 있습니다.

## 관리 권한

기본적으로 사이트의 최고관리자\(super\)는 모든 관리 페이지에 접근할 수 있습니다. 하지만, 최고관리자가 아닌 관리자\(manager\) 등급의 회원이더라도 선택적으로 사이트 관리 페이지에 접근할 수 있도록 지정할 수 있습니다.

### 권한 등록

권한을 등록하는 방법은 앞서 설명한 메뉴 등록 방법과 유사합니다. `XeRegister` 파사드의 `push` 메소드를 사용합니다.

```php
\XeRegister::push('settings/permission', $permissionId, $permissionInfo);
```

아래 코드는 '회원 생성'이라는 권한을 등록하는 예제입니다.

```php
\XeRegister::push('settings/permission', 'user.create', [
    'title' => '회원 생성',
    'tab' => '회원관리'
]);
```

첫번째 파라미터는 항상 `settings/permission`을 지정하십시오.

두번째 파라미터는 권한의 아이디입니다.

세번째 파라미터는 권한의 상세정보를 담은 배열입니다.

* `title`은 권한의 이름입니다. 권한 관리 페이지에서 권한을 표시할 때 사용됩니다.
* `tab`은 권한이 속할 그룹을 이름을 지정합니다. 권한 관리 페이지에서는 동일한 `tab`을 가진 권한들을 그룹지어 출력합니다.

위와 같이 권한을 등록해 놓으면, '사이트 관리 &gt; 설정 &gt; 관리페이지 권한 설정' 페이지에 등록한 권한이 표시됩니다. 사이트 관리자는 이 페이지에서 특정 사용자에게 권한을 부여할 수 있습니다.

### 페이지에 권한 지정하기

등록한 권한을 관리 페이지\(라우트\)에 연결하는 과정도 사이트 관리 메뉴를 지정했던 방식과 유사합니다. `permission` 필드에 권한의 아이디를 지정하십시오.

```php
Route::settings('user', function () {
    Route::get('create', [
           'as' => 'settings.member.create',
           'uses' => 'Member\Settings\UserController@create',
           'settings_menu' => 'user.create',
           'permission' => 'user.create'
    ]);
});
```

위 예제에서는 '회원 생성' 권한을 회원 추가 페이지에 지정하고 있습니다.

어떤 회원이 회원 추가 페이지에 접근할 경우, XE는 먼저 회원 추가 페이지에 지정된 권한을 찾습니다. 위의 경우 회원 추가\(`user.create`\) 권한을 찾게 됩니다. 그 다음 XE는 로그인한 회원에 권한이 부여되어 있는지 검사합니다. 만약 권한이 부여되어 있지 않은 회원이라면 오류를 출력하게 됩니다.

