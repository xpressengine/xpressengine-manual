# 라우팅

라우터는 `Request`의 URI를 판단하여 `Request`를 처리할 담당 컨트롤러를 찾는 역할을 합니다. 이 장에서는 라우트를 정의하는 방법에 대하여 설명합니다.

## 기본적인 라우팅

이미 XE에는 매우 많은 라우트가 `app/Http/routes.php` 파일안에 정의되어 있습니다. 플러그인을 개발할 때에는 각 플러그인 클래스의 `boot` 메소드에 라우트 정의 코드를 작성하십시오. 플러그인이 boot될 때 라우트가 등록됩니다.

가장 기본적인 라우트는 URI와 `Closure` 하나로 지정할 수 있습니다.

#### 기본적인 라우트

라우트를 등록할 때에는 `Route` 파사드를 사용합니다.

```php
// GET Http 메소드로 요청될 경우 'Hello World'를 화면에 출력
Route::get('/', function()
{
    return 'Hello World';
});

Route::post('foo/bar', function()
{
    return 'Hello World';
});

Route::put('foo/bar', function()
{
    //
});

Route::delete('foo/bar', function()
{
    //
});
```

`Closure` 대신 컨트롤러를 사용할 수도 있습니다.

```php
Route::get('user/profile', 'UserController@showProfile');
```

#### 여러 HTTP 메소드에 라우트 등록하기

```php
Route::match(['get', 'post'], '/', function()
{
    return 'Hello World';
});
```

`any` 메소드를 사용하면 모든 http 메소드에 응답하는 라우트를 등록할 수도 있습니다.

```php
Route::any('foo', function()
{
    return 'Hello World';
});
```

라우트에 등록된 URL을 생성하려면 `url` 헬퍼함수를 사용하면 됩니다:

```php
$url = url('foo');
```

## 라우트 파라미터

라우트에서 요청된 URI 세그먼트를 얻을 수 있습니다:

#### 기본적인 라우트 파라미터

```php
Route::get('user/{id}', function($id)
{
    return 'User '.$id;
});
```

> **주의:** 라우트 파라미터는 `-` 문자를 포함하면 안됩니다. \(`_`\)를 사용하십시오.

#### 선택적인 라우트 파라미터

```php
Route::get('user/{name?}', function($name = null)
{
    return $name;
});

Route::get('user/{name?}', function($name = 'John')
{
    return $name;
});
```

`name` 파라미터는 옵션입니다. `name` 파라미터가 URL에 포함되어 있지 않아도 위 라우트가 작동됩니다.

#### 정규표현식으로 파라미터 제약하기

```php
Route::get('user/{name}', function($name)
{
    //
})
->where('name', '[A-Za-z]+');
```

```php
Route::get('user/{id}', function($id)
{
    //
})
->where('id', '[0-9]+');

Route::get('user/{id}/{name}', function($id, $name)
{
    //
})
->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```

## 이름이 지정된 라우트

이름이 지정된 라우트는 지정된 라우트에 대한 URL을 생성하거나 Redirect를 할 때 편리함을 제공합니다. `as` 배열 키를 통해 라우트에 이름을 지정할 수 있습니다.

```php
Route::get('user/profile', ['as' => 'profile', function()
{
    //
}]);
```

컨트롤러 액션에 대해서도 라우트 이름을 지정할 수 있습니다.

```php
Route::get('user/profile', [
    'as' => 'profile', 'uses' => 'UserController@showProfile'
]);
```

이제 URL을 생성하거나 Redirect를 하는 데 라우트 이름을 사용할 수 있습니다.

```php
$url = route('profile');

$redirect = redirect()->route('profile');
```

라우트가 파라미터를 가지고 있다면, `route` 함수의 두번째 인자로 파라미터를 전달할 수 있습니다. 주어진 파라미터는 자동으로 URL에 추가됩니다.

```php
Route::get('user/{id}/profile', ['as' => 'profile', function ($id) {
    //
}]);

$url = route('profile', ['id' => 1]);
```

## 라우트 그룹

때때로 많은 라우트들이 URL 세그먼트, 미들웨어, 네임스페이스 등과 같은 공통의 요구사항을 공유하고자 하는 경우가 있습니다. 이러한 옵션들을 모든 라우트에 개별로 각각 지정하는 대신에 라우트 그룹을 통해서 다수의 라우트에 속성을 지정할 수가 있습니다.

속성값들을 공유하는 것은 `Route::group` 메소드의 첫 번째 인자로 배열을 지정하면 됩니다.

### 라우트 미들웨어

라우트 그룹에 지정하는 배열의 `middleware` 값에 미들웨어의 목록을 정의함으로써 그룹 내의 모든 라우트에 미들웨어가 적용됩니다. 라우트 미들웨어는 배열에 정의된 순서대로 실행될것입니다:

> 주의! 라우트 미들웨어는 Http 커널의 미들웨어와는 다른 별개의 기능입니다.

```php
Route::group(['middleware' => ['foo','bar']], function()
{
    Route::get('/', function()
    {
        // Has Foo And Bar Middleware
    });

    Route::get('user/profile', function()
    {
        // Has Foo And Bar Middleware
    });
});
```

### 네임스페이스

그룹의 속성 배열에 `namespace` 파라미터를 사용하여 가룹의 모든 컨트롤러에 네임스페이스를 지정할 수 있습니다:

```php
Route::group(['namespace' => 'Admin'], function()
{
    // Controllers Within The "App\Http\Controllers\Admin" Namespace

    Route::group(['namespace' => 'User'], function()
    {
        // Controllers Within The "App\Http\Controllers\Admin\User" Namespace
    });
});
```

> **참고:** 기본적으로 `RouteServiceProvider`에서 포함하고 있는 `routes.php` 파일에는 라우트 컨트롤들을 위해서 네임스페이스가 지정되어 있습니다. 따라서 `App\Http\Controllers`의 전체 네임스페이스를 따로 지정할 필요는 없습니다.

### 라우트 접두어 지정하기

라우트 그룹의 접두어\(prefix\)는 그룹의 속성 배열에 `prefix` 옵션을 사용하여 지정합니다:

```php
Route::group(['prefix' => 'admin'], function()
{
    Route::get('users', function()
    {
        // Matches The "/admin/users" URL
    });
});
```

또한, `prefix` 파라미터를 라우트들의 공통 파라미터로 지정할 수 있습니다:

#### 라우트 prefix 안에서 URL 파라미터 등록하기

```php
Route::group(['prefix' => 'accounts/{account_id}'], function()
{
    Route::get('detail', function($account_id)
    {
        //
    });
});
```

또한, 지정된 파라미터 변수의 제약 사항을 정의할 수도 있습니다:

```php
Route::group([
    'prefix' => 'accounts/{account_id}',
    'where' => ['account_id' => '[0-9]+'],
], function() {

    // Define Routes Here
});
```

## Fixed 라우트

각 플러그인들은 자유롭게 라우트를 추가하여 사용할 수 있습니다. 하지만 서로 다른 플러그인들이 동일한 규칙의 라우트를 등록하면 문제가 발생할 수 있습니다. XE는 플러그인간 라우트의 충돌을 방지하기 위하여 각 플러그인에게 별도의 라우트 공간\(url 세그먼트\)을 할당합니다.

각 플러그인에게 할당된 라우트 규칙을 사용하려면 `Route::fixed` 메소드를 사용하십시오.

```php
Route::fixed(<plugin_id>, function() {

    // Define Routes Here
    Route::get('/', ...);

});
```

`Route::fixed` 메소드의 첫번째 파라미터는 플러그인의 아이디를 지정하면 됩니다. `Route::fixed`를 사용할 경우, 접두어\(prefix\)가 `/plugin/<plugin_id>`으로 자동으로 지정됩니다. 따라서 위의 코드는 아래의 `Route::group`을 사용한 코드와 동일합니다.

```php
Route::group(['prefix'=>'plugin/<plugin_id>'], function() {

    // Define Routes Here
    Route::get('/', ...);

});
```

하지만, 접두어에 자동으로 추가되는 `plugin`은 사이트 관리자가 설정에서 변경할 수 있는 값이므로 반드시 `Route::fixed`를 사용하십시오.

## CSRF 보호하기

XE에서는 크로스 사이트 요청 위조\([cross-site request forgeries](http://en.wikipedia.org/wiki/Cross-site_request_forgery)\)으로부터 응용 프로그램을 쉽게 보호할 수 있습니다. 크로스 사이트 요청 위조는 악의적인 공격의 하나이며 인증받은 사용자를 대신하여 허가 받지 않은 명령을 수행합니다.

XE는 사용자별 CSRF "토큰"을 자동으로 생성합니다. 이 토큰은 인증된 사용자가 실제로 XE에 요청을 보내고 있는지 식별하는 데 사용됩니다.

#### Form에 CSRF 토큰 삽입하기

```php
<input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">
```

다음처럼 Blade [템플릿]()에서 사용할 수 있습니다.

```php
<input type="hidden" name="_token" value="{{ csrf_token() }}">
```

일일이 수동으로 POST, PUT 또는 DELETE 요청에 대한 CSRF 토큰을 확인할 필요가 없습니다. XE가 자동으로 요청중인 토큰을 세션에 저장되어 있는 토큰과 일치하는지 확인할 것입니다.

## 메소드 Spoofing-속이기

HTML form은 실제로 `PUT`, `PATCH`와 `DELETE` 액션을 지원하지 않습니다. 따라서 `PUT`, `PATCH` 이나 `DELETE`로 지정된 라우트를 호출하는 HTML form을 정의한다면 `_method`의 숨겨진 필드를 지정해야 합니다.

`_method` 필드로 보내진 값은 HTTP 요청 메소드를 구분하는 데 사용됩니다. 다음 예를 참조하십시오:

```php
<form action="/foo/bar" method="POST">
    <input type="hidden" name="_method" value="PUT">
    <input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">
</form>
```

