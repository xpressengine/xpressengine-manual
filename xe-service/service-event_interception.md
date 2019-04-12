# 이벤트/인터셉션

## 이벤트/인터셉션\(event/interception\)

플러그인 개발자가 플러그인에서 필요한 기능을 구현하기 위해서는 XE가 실행되는 여러 시점에 끼어들어 XE의 행동을 바꾸거나 추가적인 행동을 할 수 있어야 합니다.

예를 들어, 사이트에 새로운 회원이 가입할 때, 가입한 회원에게 가입축하 메일을 보내는 기능을 플러그인으로 만들 수 있습니다. 이 기능을 구현하려면 XE가 회원가입을 처리할 때, 플러그인이 끼어들어 메일을 전송하는 코드를 실행할 수 있어야 합니다.

이러한 '끼어들기'를 일반적으로 'hook' 또는 'event'라고 칭합니다. XE에서는 라라벨에서 기본적으로 제공하는 'event' 방식과 XE에서 새롭게 제공하는 'interception' 방식으로 '끼어들기'를 지원합니다.

### 이벤트

이벤트는 옵저버 패턴으로 구현됩니다. 특정 이벤트에 대하여 리스너를 등록해 놓으면, 이벤트가 발생했을 때, 등록한 리스너가 차례대로 실행됩니다.

#### 이벤트 리스너 등록

이벤트가 발생했을 때 실행될 리스너는 `Event` 파사드나 `Illuminate\Contracts\Events\Dispatcher` contract의 구현을 이용해 등록할 수 있습니다

```php
/**
 * Register any other events for your plugin
 *
 * @param  \Illuminate\Contracts\Events\Dispatcher  $events
 * @return void
 */
public function boot(DispatcherContract $events)
{
    $events->listen('event.name', function ($foo, $bar) {
        //
    });
}

// or

public function boot()
{
    \Event::listen('event.name', function ($foo, $bar) {
        //
    });
}
```

**이벤트 리스너를 클래스로 정의**

클로저 형식의 이벤트 리스너 대신 클래스 형식으로 이벤트 리스너를 정의할 수 있습니다.

```php
Event::listen('auth.login', 'LoginHandler');
```

기본적으로 이벤트가 발생했을 경우, 이벤트 리스너 클래스의 `handle`메소드가 호출됩니다.

```php
class LoginHandler {

    public function handle($data)
    {
        //
    }
}
```

`handle` 메소드 대신 다른 메소드를 사용할 수도 있습니다. 리스너 등록시 메소드명을 아래와 같이 기입하십시오.

```php
Event::listen('auth.login', 'LoginHandler@onLogin');
```

**와일드카드 이벤트 리스너**

`*`를 와일드카드로 사용하여 리스너를 등록하면 동일한 리스너에서 여러 개의 이벤트에 대응 할 수 있습니다. 와일드카드 리스너는 전체 이벤트 데이터 배열을 하나의 인자로 받아들입니다:

```php
$events->listen('event.*', function (array $data) {
    //
});
```

**리스너 우선순위 지정**

리스너의 우선순위를 지정할 수 있습니다. 우선순위가 높은 리스너가 먼저 실행되며, 동일한 우선순위일 경우, 먼저 등록된 리스너가 먼저 실행됩니다.

```php
Event::listen('auth.login', 'LoginHandler', 10);

Event::listen('auth.login', 'OtherHandler', 5);
```

#### 이벤트 발생시키기

이벤트를 발생시키기려면 `Event` 파사드의 `fire` 메소드를 사용하십시오.

```php
$args = [...];
Event::fire('event.name', $args)
```

**이벤트 전달 중단하기**

경우에 따라서 이벤트가 다른 리스너에게 전달되는 것을 중단 하기를 원할 수도 있습니다. 이러한 경우에는 리스너가 `false`를 반환하면 됩니다.

```php
Event::listen('auth.login', function($event)
{
    // Handle the event...

    return false;
});
```

#### XE 이벤트 목록

아래 목록은 XE에서 제공하는 다양한 코어 레벨의 이벤트의 목록입니다. 플러그인의 `boot` 메소드에서 아래 이벤트가 발생할 때 실행할 리스너를 등록하여 쓸 수 있습니다.

| Event | Parameter\(s\) |
| :--- | :--- |
| auth.attempt | $credentials, $remember, $login |
| auth.login | $user, $remember |
| auth.logout | $user |
| cache.missed | $key |
| cache.hit | $key, $value |
| cache.write | $key, $value, $minutes |
| cache.delete | $key |
| connection.{name}.beginTransaction | $connection |
| connection.{name}.committed | $connection |
| connection.{name}.rollingBack | $connection |
| illuminate.query | $query, $bindings, $time, $connectionName |
| illuminate.queue.after | $connection, $job, $data |
| illuminate.queue.failed | $connection, $job, $data |
| illuminate.queue.stopping | null |
| mailer.sending | $message |
| router.matched | $route, $request |
| composing:{view name} | $view |
| creating:{view name} | $view |
| xe.editor.option.building | $editor |
| xe.editor.option.builded | $editor |
| xe.editor.render' | $editor |
| xe.editor.compile' | $editor |

### 인터셉션

이벤트 방식을 사용하는 경우, 특정 시점에 이벤트를 발생시키기 위해 이벤트를 fire하는 코드를 일일이 작성해주어야 합니다. 만약 게시판 플러그인에서 게시판에 글이 처음 등록될 때, 또는 수정, 삭제 될 때마다 다른 플러그인에서 '끼어들기'를 할 수 있도록 해주려면 게시글의 등록, 수정, 삭제에 대한 이벤트를 fire하는 코드를 3군데에 작성해주어야 합니다. 이벤트 방식 대신 인터셉션을 사용하면 이벤트를 fire하는 코드를 매번 작성하는 번거로움을 줄여줄 수 있습니다.

XE3는 인터셉션을 구현하기 위하여 AOP를 사용하였습니다. AOP는 Aspect Oriented Programming의 약어입니다.

#### 어드바이저\(리스너\) 등록

인터셉션 방식에서 어드바이저는 이벤트 방식의 리스너와 비슷합니다. 만약 사이트에 회원이 가입할 때, 메일 전송 코드가 실행되도록 하려면 `intercept` 함수를 사용하여 어드바이저를 등록할 수 있습니다.

```php
// 가입 축하 메일 보내기 등록
intercept('XeUser@create', 'welcome_mail::send_mail', function($createUser, array $data) use ($mailer) {

    // 회원가입 코드를 실행
    $member = $createUser($data);

    // 메일 전송
    $mailer->sendWelcomeMail($member->email, $member->getDisplayName);

    // 회원가입 처리 결과 반환
    return $member;
});
```

`intercept` 함수의 원형은 아래와 같습니다.

```php
intercept($pointCut, $name, Closure $advice)
```

첫번째 파라메터 `$pointCut`은 이벤트 서비스의 이벤트명과 동일한 역할을 합니다. 즉, '끼어들기'를 할 대상 메소드를 칭하며, 위의 예에서는 `XeUser@create`에 해당합니다. 이는 `XeUser`의 `create` 메소드를 뜻합니다.

AOP에서 '끼어들기'를 하는 주체를 어드바이저\(Advisor\)라고 합니다. 이벤트 서비스의 리스너와 같은 개념입니다. 두번째 파라메터인 `$name`은 이 어드바이저의 '이름'입니다. 원하는 이름을 직접 지정하십시오. 단 이 이름은 다른 어드바이저의 이름과 중복되지 않아야 합니다. 중복을 회피하기 위하여 해당 플러그인의 아이디\(디렉토리명\)를 이름의 접두사로 사용하십시오. `welcome_mail`이 플러그인 아이디에 해당합니다. ::을 사용하여 접두사를 연결하십시오.

세번째 파라메터 `$advice` '끼어들기'를 한 후 실행될 코드입니다. 클로저 형식으로 입력해야 합니다. 이 클로저 내부에서는 반드시 타겟 메소드\(회원가입메소드\)를 호출해 주어야 합니다. 타겟 메소드는 클로저의 첫번째 파라메터\(`$createUser`\)를 사용하여 호출할 수 있습니다. 타겟 메소드를 호출하기 전이나 후에 원하는 코드를 추가하여 실행시킬 수 있습니다. 위의 예에서는 회원가입 처리를 한 후에 해당 회원에게 메일을 전송하는 코드가 추가되어 있습니다.

이 클로저에 대해 자세히 설명하면, 이 클로저의 첫번째 파라메터는 타겟 메소드입니다. 위의 예에서 `$createUser`가 이에 해당합니다. 클로저 내부에서 항상 이 타겟 메소드를 호출해주어야 합니다. 또, 클로저는 타겟 메소드의 리턴값을 다시 리턴해야 합니다. 물론 필요에 따라 리턴값을 변경해도 됩니다. 두번째 파라메터부터는 대상 메소드가 호출될 때 받은 파라메터를 그대로 전달받습니다. 위의 예에서는 `$data`에 해당하며, 가입할 회원의 정보가 담겨있습니다. 타겟 메소드에 따라 파라메터의 수와 내용이 달라집니다.

```php
function($createUser, array $data) {

  // 항상 타겟 메소드(첫번째 파라메터)를 호출해주어야 한다.
  $member = $createMember($data);

  // 항상 타겟 메소드의 반환값을 다시 반환해야 한다.
  return $member;
}
```

#### 어드바이저 간의 우선순위 지정

다수의 어드바이저가 등록돼 있다면, 어드바이저 간의 실행순서가 중요할 수 있습니다. `intercept` 함수를 호출시 다른 어드바이저와의 우선순위를 지정할 수 있습니다.

```php
// email_checker::check가 실행된 후 실행.
intercept('XeUser@create', ['welcome_mail::send_mail' => 'foo@bar'], $closure);
```

우선순위는 두번째 파라미터에 배열형태로 지정할 수 있습니다. 위의 `intercept` 함수는 배열의 키 이름인 `welcome_mail::send_mail` 어드바이저를 등록하고 있습니다. 동시에 배열의 값\(value\)을 이용해여 선행되어야 하는 어드바이저 `foo@bar`를 지정하고 있습니다.

위 코드에서 우선순위 관계 더욱더 명시적으로 작성할 수 있습니다. 중첩된 배열의 키에 'before'를 사용하십시오.

```php
// 명시적으로 before 사용
intercept(
  'XeUser@create', 
  ['welcome_mail::send_mail' => ['before' => 'foo@bar'], 
  $closure
);

// 다수의 어드바이저와 우선순위 지정
intercept(
  'XeUser@create', 
  ['welcome_mail::send_mail' => ['before' => ['foo@bar', 'foo@baz']], 
  $closure
);
```

반대로 `after`를 사용하면, 현재 어드바이저보다 나중에 실행되어야 할 어드바이저를 등록할 수도 있습니다.

```php
// after 사용
intercept(
  'XeUser@create', 
  ['welcome_mail::send_mail' => ['after' => ['foo@bar']], 
  $closure
);
```

인터셉션 서비스는 `intercept` 함수를 통해 등록받은 어드바이저들의 우선순위를 파악한 후, 순서대로 호출해 줍니다. 어드바이저들과 타겟 메소드는 데코레이션 패턴으로 실행됩니다. 이는 [미들웨어](http://xpressengine.github.io/laravel-korean-docs/docs/5.0/middleware/)와 거의 동일한 방식입니다.

#### 프록시 생성

위의 회원가입 예제에서 보면, `XeUser@create`를 포인트컷으로 지정하고 있습니다. 위 예제의 코드는 `XeUser` 파사드\(`\Xpressengine\User\UserHandler`\)의 `create` 메소드가 실행될 때, 자동으로 호출됩니다.

이렇게 어떤 클래스의 메소드\(public 메소드만 해당\)가 실행될 때, 자동으로 등록된 어드바이저들이 호출되도록 하려면, 그 클래스 프록시 클래스를 생성하고, 프록시 클래스를 대신 사용해야 합니다.

프록시 클래스는 `XeInterception` 파사드의 `proxy` 메소드를 사용하여 생성할 수 있습니다.

```php
// 프록시 클래스 생성
$proxyClass = XeInterception::proxy('\Xpressengine\User\UserHandler', 'XeUser');

// 원본 클래스 대신 프록시 클래스 사용
$userHandler = new $proxyClass();
```

위 코드에서는 원본 클래스인 `\Xpressengine\User\UserHandler` 클래스의 프록시 클래스를 생성합니다. 그리고 원본 클래스 대신 프록시 클래스의 인스턴스를 생성하여 사용합니다. 마지막 파라미터에는 원본 클래스의 별칭\(alias\)를 지정할 수 있습니다. `UserHandler` 클래스는 `XeUser` 파사드를 통해 많이 사용됩니다. 위의 경우 파사드명을 별칭으로 등록했습니다. `intercept` 함수를 사용할 때, 원본클래스명 뿐만 아니라 별칭을 사용할 수도 있습니다.

아래의 두 코드 모두 사용할 수 있습니다.

```php
// 별칭을 사용
intercept('XeUser@create', ...);

// 원본클래스명을 그대로 사용
intercept('Xpressengine\User\UserHandler@create', ...);
```

