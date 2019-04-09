# 컨트롤러

플러그인은 플러그인 클래스의 `boot` 메소드를 통해 라우트를 등록할 수 있습니다. 라우트를 등록할 때, 특정 URL의 요청을 처리할 로직을 클로저로 작성하는 대신, 별도의 컨트롤러 클래스에 작성할 수 있습니다. 성격이 비슷한 요청을 처리하기 위한 컨트롤러 클래스를 정의하십시오.

컨트롤러는 `App\Http\Controllers\Controller` 클래스를 상속받아 작성하십시오.

## 기본 컨트롤러 <a id="undefined"></a>

다음은 기본적인 컨트롤러 클래스의 예제입니다:

```php
<?php namespace App\Http\Controllers;

use App\Http\Controllers\Controller;

class UserController extends Controller {

    /**
     * Show the profile for the given user.
     *
     * @param  int  $id
     * @return Response
     */
    public function showProfile($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }

}
```

다음과 같이 컨트롤러의 액션에 라우트를 지정할 수 있습니다:

```text
Route::get('user/{id}', 'App\Http\UserController@showProfile');
```

### 컨트롤러 & 네임스페이스 <a id="and-1"></a>

컨트롤러의 네임스페이스를 지정할 때에는 반드시 전체 네임스페이스를 다 써주어야 합니다.

```text
Route::get('foo', 'Photos\AdminController@method');
```

`namespace`를 사용하면 반복되는 namespace를 생략할 수 있습니다.

```php
Route::group(['prefix' => 'photos', 'namespace' => 'Photos'], function()
{
    Route::get('admin', 'AdminController@method');
});
```

### 이름이 지정된 컨트롤러 라우트 <a id="undefined-3"></a>

클로저 라우트와 같이 컨트롤러 라우트에 이름을 지정할 수 있습니다.

```php
Route::get('foo', ['uses' => 'FooController@method', 'as' => 'name']);
```

### 컨트롤러 액션의 URL 구하기 <a id="url"></a>

컨트롤러 액션에 대한 URL을 생성하기 위해서 `action` 헬퍼함수를 사용합니다:

```php
$url = action('App\Http\Controllers\FooController@method');
```

단순히 컨트롤러의 전체 네임스페이스의 대신 클래스명만으로 URL을 생성하고 싶은 경우에는 root 컨트롤러 네임스페이스를 URL 제너레이터에 등록하면 됩니다:

```php
URL::setRootControllerNamespace('App\Http\Controllers');

$url = action('FooController@method');
```

실행중인 컨트롤러 액션의 이름을 찾고자 한다면 `currentRouteAction` 메소드를 사용하면 됩니다:

```php
$action = Route::currentRouteAction();
```

## 컨트롤러 미들웨어 <a id="undefined-1"></a>

​[미들웨어](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/docs/5.0/middleware/README.md)는 다음과 같이 컨트롤러 라우트에 지정합니다.

```php
Route::get('profile', [
    'middleware' => 'auth',
    'uses' => 'UserController@showProfile'
]);
```

덧붙여 미들웨어를 컨트롤러의 생성자에서 지정할수도 있습니다.

```php
class UserController extends Controller {

    /**
     * Instantiate a new UserController instance.
     */
    public function __construct()
    {
        $this->middleware('auth');

        $this->middleware('log', ['only' => ['fooAction', 'barAction']]);

        $this->middleware('subscribed', ['except' => ['fooAction', 'barAction']]);
    }
}
```

## 묵시적 컨트롤러 <a id="undefined-2"></a>

XE에서는 한번의 라우팅 등록으로 컨트롤러를 통해 모든 액션들을 처리할 수 있는 손쉬운 방법을 제공합니다. 먼저 `Route::controller` 메소드를 사용하여 경로를 지정합니다:

```php
Route::controller('users', 'UserController');
```

`controller` 메소드는 두 개의 인자를 넘겨 받도록 되어 있습니다. 첫 번째 인자는 컨트롤러로 제어할 URI이고, 두 번째는 컨트롤러의 클래스명을 의미합니다. 이어서 해당하는 HTTP 메소드 이름을 접두어로 \(get, post..\) 사용하는 형태로 컨트롤러의 메소드를 추가합니다:

```php
class UserController extends Controller {

    public function getIndex()
    {
        //
    }

    public function postProfile()
    {
        //
    }

    public function anyLogin()
    {
        //
    }

}
```

위의 경우에 컨트롤러의 `index` 메소드는 `users` URI에 대한 루트 주소에 대한 결과를 반환합니다.

만약 컨트롤러의 메소드가 여러개의 단어로 구성되어 진 형태라면 "-"을 통해서 접속할 수 있는 URI를 제공하게 됩니다. 예를 들어, `UserController`에 다음과 같은 액션이 정의되었다면 URI는 `users/admin-profile`과 같이 구성됩니다:

```php
public function getAdminProfile() {}
```

### 라우트에 이름 지정하기 <a id="undefined-4"></a>

컨트롤러 라우트에 어떤 “이름”을 지정하고자 한다면 `controller` 메소드의 세 번째 인자를 통해서 지정할 수 있습니다:

```php
Route::controller('users', 'UserController', [    'anyLogin' => 'user.login',]);
```

## 의존성 주입 & 컨트롤러 <a id="and"></a>

### 생성자 주입 <a id="undefined-5"></a>

XE의 서비스 컨테이너는 모든 컨트롤러의 의존성을 해결하기 위해서 사용됩니다. 그 결과 컨트롤러가 필요로 하는 의존 객체들에 대해서 생성자에서 타입힌트로 지정할 수 있게 됩니다:

```php
<?php namespace App\Http\Controllers;

use Illuminate\Routing\Controller;
use App\Repositories\UserRepository;

class UserController extends Controller {

    /**
     * The user repository instance.
     */
    protected $users;

    /**
     * Create a new controller instance.
     *
     * @param  UserRepository  $users
     * @return void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }

}

```

서비스 컨테이너가 의존성을 해결을 할 수 있다면 타입 힌트에 지정할 수는 있습니다.

### 메소드 인젝션-주입 <a id="undefined-6"></a>

생성자 주입과 더불어 컨트롤러의 메소드에서도 타입힌트를 통한 의존성 주입을 할 수 있습니다. 예를 들어, 메소드에서 `Request` 인스턴스를 타입힌트를 통해서 주입할 수 있습니다:

```php
<?php namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

class UserController extends Controller {

    /**
     * Store a new user.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $name = $request->input('name');

        //
    }

}
```

컨트롤러 메소드가 라우트 인자로부터 입력값을 받아야 한다면 간단하게 의존성 지정 뒤에 인자를 지정하면 됩니다:

```php
<?php namespace App\Http\Controllers;
​
use Illuminate\Http\Request;
use Illuminate\Routing\Controller;
​
class UserController extends Controller {
​
    /**
     * Update the specified user.
     *
     * @param  Request  $request
     * @param  int  $id
     * @return Response
     */
    public function update(Request $request, $id)
    {
        //
    }
​
}

```

[  
](https://xpressengine.gitbook.io/xpressengine-manual/ko/undefined/routing)

