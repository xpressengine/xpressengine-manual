# 쿠키

## 쿠키\(cookie\)

XE에서 모든 쿠키는 서버에서 미들웨어를 거치면서 인증 코드와 함께 암호화 됩니다. 이 말은 클라이언트가 변경되었을 때 쿠키가 유효하지 않다는 것을 의미합니다. 쿠키 값은 [Request](../undefined/request.md) 인스턴스와 [Response](../undefined/response.md) 인스턴스를 사용하여 조회하거나 저장할 수 있습니다.

### 쿠키 값 가져오기

```php
$value = $request->cookie('name');
```

### Response에 새로운 쿠키 추가하기

`cookie` 헬퍼함수는 새로운 `Symfony\Component\HttpFoundation\Cookie` 인스턴스를 생성하기 위한 간단한 팩토리로 작동합니다. 쿠키를 `Response` 인스턴스에 추가하려면 `withCookie` 메소드를 사용하면 됩니다:

```php
$response = new Illuminate\Http\Response('Hello World');

$response->withCookie(cookie('name', 'value', $minutes));
```

### 영원히 남게되는 쿠키 생성하기

_영원히는 실제로는 5년을 의미합니다._

```php
$response->withCookie(cookie()->forever('name', 'value'));
```

### 쿠키 큐처리 하기

플러그인 클래스의 `boot` 메소드는 XE의 라이프 사이클에서 매우 이른 시점에 실행됩니다. 이렇게 Response 인스턴스가 작성되기 전에 실행되는 코드에서는 반환되는 Response에 추가할 쿠키를 “큐 처리”할 수 있습니다:

```php
<?php namespace App\Http\Controllers;

use Cookie;
use Illuminate\Routing\Controller;

class UserController extends Controller
{
    /**
     * Update a resource
     *
     * @return Response
     */
     public function update()
     {
        Cookie::queue('name', 'value');

        return response('Hello World');
     }
}
```

