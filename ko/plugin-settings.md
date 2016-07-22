# 사이트 관리 페이지 추가하기

XE는 사이트 관리자 또는 관리 등급을 가진 회원만 접근할 수 있는 '사이트 관리' 영역을 가지고 있습니다. 사이트 관리 영역은 일반적으로 `http://<도메인>/settings`로 접근할 수 있습니다. '사이트 관리' 영역은 사이트 관리에 필요한 다양한 웹페이지들로 구성되어 있습니다.


플러그인도 사이트 관리 영역에 플러그인을 위한 페이지를 추가할 수 있습니다.

## 라우트 등록

관리 페이지를 위한 라우트를 등록하는 방법은 일반 웹페이지의 라우트를 등록하는 방법과 크게 다르지 않습니다. 다만, `Route::group` 메소드 대신 `Route::settings` 메소드를 사용하면 됩니다. 

사이트 관리 영역에 속하는 모든 페이지의 URL은 모두 첫번째 세그먼트로 `settings`를 가지게 됩니다. 라우트를 등록하는 코드는 플러그인 클래스의 

```php
<?php
namespace MyPlugin;

use Xpressengine\Plugin\AbstractPlugin;
use Schema;

class Plugin extends AbstractPlugin
{
  public function boot()
  {
      // 사이트 관리 페이지 추가
      
  }
  
  
}
```



## 메뉴 추가


## 권한 추가


## 컨트롤러 구현