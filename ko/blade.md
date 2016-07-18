# 템플릿(Blade Template)

## 소개

Blade is the simple, yet powerful templating engine provided with Laravel. Unlike other popular PHP templating engines, Blade does not restrict you from using plain PHP code in your views. All Blade views are compiled into plain PHP code and cached until they are modified, meaning Blade adds essentially zero overhead to your application. Blade view files use the `.blade.php` file extension and are typically stored in the `resources/views` directory.

XE에서 제공하고 있는 블레이드는 심플하고 강력한 템플릿 엔진입니다. 다른 인기있는 PHP 템플릿 엔진들과는 다르게 블레이드는 여러분이 순수한 PHP 코드를 뷰에 사용하는 것을 제안하지 않습니다. 모든 블레이드 뷰는 순수한 PHP 코드로 컴파일된 후 캐싱됩니다. 파일이 수정되지 않을 때까지 캐싱된 파일을 사용합니다. 이는 블레이드가 본질적으로 성능상 부하가 없음을 의미합니다. 블레이드 뷰 파일은 확장자로 `.blade.php`를 사용합니다.


## 템플릿 상속

### 레이아웃 정의하기

Two of the primary benefits of using Blade are _template inheritance_ and _sections_. To get started, let's take a look at a simple example. First, we will examine a "master" page layout. Since most web applications maintain the same general layout across various pages, it's convenient to define this layout as a single Blade view:

블레이드를 사용하는 주된 장점 두가지는 _템플릿 상속_과 _섹션_입니다. 시작하기 전에 간단한 예제를 살펴보겠습니다. 첫번째로, 우리는 "master" 페이지 레이아웃을 보겠습니다. 대부분의 웹사이트는 여러 페이지에서 걸쳐 동일한 레이아웃을 사용하기 때문에, 이 레이아웃을 하나의 블레이드 뷰로 정의하는 것이 편합니다.

```html
<!-- Stored in resources/views/layouts/master.blade.php -->

<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

보는 바와 같이, 이 파일은 일반적인 HTML 마크업을 가지고 있습니다. 그런데 `@section` 과 `@yield` 지시자에 주목해 주십시오. `@section` 지시자는 이름에서도 알 수 있듯이 컨텐츠의 섹션을 정의하고 있고. 반대로 `@yield` 지시자는 주어진 섹션의 컨텐츠를 출력하고 있습니다.

이제 레이아웃은 정의했고, 이 레이아웃을 상속받을 자식 페이지를 정의해 보겠습니다.

### 레이아웃 확장하기

자식페이지를 정의할 때, `@extends` 지시자를 사용하여 자식 페이지에서 "상속"받을 레이아웃을 지정할 수 있습니다. 레이아웃을 상속(`@extends`)받는 뷰들은 `@section` 지시자를 사용해서 그 레이아웃의 섹션에 들어갈 컨텐츠를 주입해야 합니다. 앞의 예에서 보았듯이, 자식 페이지에서 주입한 섹션의 컨텐츠는 레이아웃의 `@yield` 부분에 출력될 것입니다.

```html
<!-- Stored in resources/views/child.blade.php -->

@extends('layouts.master')

@section('title', 'Page Title')

@section('sidebar')
    @@parent

    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```



In this example, the `sidebar` section is utilizing the `@@parent` directive to append (rather than overwriting) content to the layout's sidebar. The `@@parent` directive will be replaced by the content of the layout when the view is rendered.

위의 예에서, `sidebar` 섹션은 레이아웃의 사이드바에 컨텐츠를 붙이기 위해 `@@parent` 지시자를 사용하고 있습니다. `@@parent` 지시자는 뷰가 렌더링 될 때, 그 레이아웃의 `sidebar` 섹션이 가지고 있는 컨텐츠로 대체될 것입니다.

Of course, just like plain PHP views, Blade views may be returned from routes using the global `view` helper function:

블레이드 템플릿은 순수한 PHP 뷰와 마찬가지로 `view` 헬퍼를 사용하여 곧바로 반환될 수 있습니다.

```php
Route::get('blade', function () {
    return view('child');
});
```

## 데이터 출력하기

You may display data passed to your Blade views by wrapping the variable in "curly" braces. For example, given the following route:

중괄호(culry brace)로 변수를 감싸면, 블레이드 뷰로 전달된 데이터를 출력할 수 있습니다. 예를 들어, 주어진 라우트가 있을 때:

```php
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

`name` 변수의 값을 다음과 같이 출력할 수 있습니다:

```html
Hello, {{ $name }}.
```

뷰로 전달된 변수들의 값을 출력하는 것에 별다른 제한은 없습니다. PHP 함수의 결과 값을 출력할 수도 있습니다. 블레이드의 데이터 출력 구문 안에는 어떤 PHP 코드도 들어갈 수 있습니다.

```html
The current UNIX timestamp is {{ time() }}.
```

> **주의:** `{{ }}` 구문은 자동으로 PHP의 `htmlentities` 함수로 감싼 후 출력합니다. XSS 공격을 방어하기 위함입니다.

#### 블레이드 & 자바스크립트 프레임워크

Since many JavaScript frameworks also use "curly" braces to indicate a given expression should be displayed in the browser, you may use the `@` symbol to inform the Blade rendering engine an expression should remain untouched. For example:

많은 자바스크립트 프레임웍에서도 브라우저에 출력되어야 할 데이터를 표시하기 위해 중괄호("curly" braces)를 사용하고 있습니다. 이럴 경우, `@` 기호를 사용하면 블레이드 랜더링 엔진은 이 구문을 변환하지 않고 그대로 출력할 것입니다. 예를 들어:

```php
<h1>Laravel</h1>

Hello, @{{ name }}.
```

위의 예에서 `@` 기호는 블레이드에 의해서 제거될 것이고, 나머지 `{{ name }}` 구문은 블레이드 엔진에 의해 해석되지 않고 그대로 남아있게 되어, 자바스크립트 프레임워크에 의해 변환되어 집니다.

#### 데이터가 존재하는지 확인후 출력하기

Sometimes you may wish to echo a variable, but you aren't sure if the variable has been set. We can express this in verbose PHP code like so:

가끔 변수를 출력할 때, 그 변수가 실제로 존재하는지 확신하지 못할 때도 있습니다. 이때 다음과 같이 PHP 코드를 사용할 수 있습니다.

```php
{{ isset($name) ? $name : 'Default' }}
```

이렇게 3항연산자를 사용하는 대신, 블레이드는 다음과 같은 편리한 구문을 제공합니다:

```php
{{ $name or 'Default' }}
```

위의 예에서, 만약 `$name` 변수가 존재하면 그 값이 출력될 것이고, 존재하지 않는다면 대신 `Default`라는 문자가 출력될 것입니다.

#### Escape 하지 않고 데이터 출력하기

By default, Blade `{{ }}` statements are automatically sent through PHP's `htmlentities` function to prevent XSS attacks. If you do not want your data to be escaped, you may use the following syntax:

기본적으로, `{{ }}` 구문은 XSS 공격을 방어하기 위해 자동으로 PHP의 `htmlentities` 함수를 실행후 반환합니다. 만약 데이터를 `htmlentities` 함수를 거치지 않은채 출력하고 싶다면, 다음과 같은 구문을 사용하십시오.

```php
Hello, {!! $name !!}.
```

> ** 주의:** 사용자로부터 입력 된 내용을 표시 할 때에는 escape에 대한 매우 세심한 주의가 필요합니다. 컨텐츠의 HTML 엔티티를 escape 하기위해 항상 이중 중괄호 표기법을 사용하십시오.

<a name="control-structures"></a>
## Control Structures

In addition to template inheritance and displaying data, Blade also provides convenient short-cuts for common PHP control structures, such as conditional statements and loops. These short-cuts provide a very clean, terse way of working with PHP control structures, while also remaining familiar to their PHP counterparts.

#### If Statements

You may construct `if` statements using the `@if`, `@elseif`, `@else`, and `@endif` directives. These directives function identically to their PHP counterparts:

    @if (count($records) === 1)
        I have one record!
    @elseif (count($records) > 1)
        I have multiple records!
    @else
        I don't have any records!
    @endif

For convenience, Blade also provides an `@unless` directive:

    @unless (Auth::check())
        You are not signed in.
    @endunless

#### Loops

In addition to conditional statements, Blade provides simple directives for working with PHP's supported loop structures. Again, each of these directives functions identically to their PHP counterparts:

    @for ($i = 0; $i < 10; $i++)
        The current value is {{ $i }}
    @endfor

    @foreach ($users as $user)
        <p>This is user {{ $user->id }}</p>
    @endforeach

    @forelse ($users as $user)
        <li>{{ $user->name }}</li>
    @empty
        <p>No users</p>
    @endforelse

    @while (true)
        <p>I'm looping forever.</p>
    @endwhile

#### Including Sub-Views

Blade's `@include` directive, allows you to easily include a Blade view from within an existing view. All variables that are available to the parent view will be made available to the included view:

    <div>
        @include('shared.errors')

        <form>
            <!-- Form Contents -->
        </form>
    </div>

Even though the included view will inherit all data available in the parent view, you may also pass an array of extra data to the included view:

    @include('view.name', ['some' => 'data'])

> **Note:** You should avoid using the `__DIR__` and `__FILE__` constants in your Blade views, since they will refer to the location of the cached view.

#### Rendering Views For Collections

You may combine loops and includes into one line with Blade's `@each` directive:

    @each('view.name', $jobs, 'job')

The first argument is the view partial to render for each element in the array or collection. The second argument is the array or collection you wish to iterate over, while the third argument is the variable name that will be assigned to the current iteration within the view. So, for example, if you are iterating over an array of `jobs`, typically you will want to access each job as a `job` variable within your view partial.

You may also pass a fourth argument to the `@each` directive. This argument determines the view that will be rendered if the given array is empty.

    @each('view.name', $jobs, 'job', 'view.empty')

#### Comments

Blade also allows you to define comments in your views. However, unlike HTML comments, Blade comments are not included in the HTML returned by your application:

    {{-- This comment will not be present in the rendered HTML --}}

<a name="service-injection"></a>
## Service Injection

The `@inject` directive may be used to retrieve a service from the Laravel [service container](/docs/{{version}}/container). The first argument passed to `@inject` is the name of the variable the service will be placed into, while the second argument is the class / interface name of the service you wish to resolve:

    @inject('metrics', 'App\Services\MetricsService')

    <div>
        Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
    </div>

<a name="extending-blade"></a>
## Extending Blade

Blade even allows you to define your own custom directives. You can use the `directive` method to register a directive. When the Blade compiler encounters the directive, it calls the provided callback with its parameter.

The following example creates a `@datetime($var)` directive which formats a given `$var`:

    <?php

    namespace App\Providers;

    use Blade;
    use Illuminate\Support\ServiceProvider;

    class AppServiceProvider extends ServiceProvider
    {
        /**
         * Perform post-registration booting of services.
         *
         * @return void
         */
        public function boot()
        {
            Blade::directive('datetime', function($expression) {
                return "<?php echo with{$expression}->format('m/d/Y H:i'); ?>";
            });
        }

        /**
         * Register bindings in the container.
         *
         * @return void
         */
        public function register()
        {
            //
        }
    }

As you can see, Laravel's `with` helper function was used in this directive. The `with` helper simply returns the object / value it is given, allowing for convenient method chaining. The final PHP generated by this directive will be:

    <?php echo with($var)->format('m/d/Y H:i'); ?>
