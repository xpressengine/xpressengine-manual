# 이벤트 및 인터셉션

플러그인 개발자가 플러그인에서 필요한 기능을 구현하기 위해서는 XE가 실행되는 여러 시점에 플러그인이 끼어들어 XE의 행동을 바꾸거나 추가적인 행동을 할 수 있어야 합니다.

예를 들어, 사이트에 새로운 회원이 가입할 때, 가입한 회원에게 가입축하 메일을 보내는 기능을 플러그인으로 만들 수 있습니다. 코어가 회원가입을 처리할 때, 플러그인이 끼어들어 메일을 전송해야 합니다.

이러한 '끼어들기'를 일반적으로 'hook' 또는 'event'라고 칭합니다. XE에서는 라라벨에서 기본적으로 제공하는 'event' 방식과 XE에서 새롭게 제공하는 'interception' 방식으로 '끼어들기'를 지원합니다.

## 이벤트

이벤트는 옵저버 패턴으로 구현됩니다. 특정 이벤트에 대하여 리스너를 등록해 놓으면, 이벤트가 발생했을 때, 등록한 리스너가 실행됩니다.

### 이벤트 리스너 등록

이벤트가 발생했을 때 실행될 리스너는 `Event` 파사드나  `Illuminate\Contracts\Events\Dispatcher` contract의 구현을 이용해 등록할 수 있습니다

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
#### 와일드카드 이벤트 리스너

`*`를 와일드카드로 사용하여 리스너를 등록하면 동일한 리스너에서 여러 개의 이벤트에 대응 할 수 있습니다. 와일드카드 리스너는 전체 이벤트 데이터 배열을 하나의 인자로 받아들입니다:

```php
$events->listen('event.*', function (array $data) {
    //
});
```

### 이벤트 발생시키기

이벤트를 발생시키기려면 `Event` 파사드의 `fire` 메소드를 사용하십시오.

```php
$args = [...];
\Event::fire('event.name', $args)
```
