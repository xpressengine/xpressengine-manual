# 사이트 관리 페이지 추가하기

XE는 사이트 관리자 또는 관리 등급을 가진 회원만 접근할 수 있는 '사이트 관리 영역'을 가지고 있습니다. 사이트 관리 영역은 사이트 관리에 필요한 다양한 '사이트 관리 페이지'들로 구성되어 있습니다. 사이트 관리 영역은 일반적으로 `http://<도메인>/settings`로 접근할 수 있습니다.


플러그인도 사이트 관리 영역에 플러그인을 위한 페이지를 추가할 수 있습니다.

## 라우트 등록

관리 페이지를 위한 라우트를 등록하는 방법은 일반 웹페이지의 라우트를 등록하는 방법과 크게 다르지 않습니다. 다만, `Route::group` 메소드 대신 `Route::settings` 메소드를 사용하면 됩니다. 

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


## 메뉴 추가

사이트 관리 영역의 좌측에는 관리 메뉴 트리가 출력됩니다. 플러그인은 사이트 관리 페이지를 

## 권한 추가


## 컨트롤러 구현