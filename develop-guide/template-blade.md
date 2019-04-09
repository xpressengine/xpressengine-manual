# 템플릿

## 소개 <a id="undefined"></a>

XE에서 제공하고 있는 블레이드는 간결하고 강력한 템플릿 엔진입니다. 다른 인기있는 PHP 템플릿 엔진들과는 다르게, 블레이드는 여러분이 순수한 PHP 코드를 뷰에 사용하는 것을 제한하지 않습니다. 모든 블레이드 뷰는 순수한 PHP 코드로 컴파일된 후 캐싱되고, 파일이 수정되지 않을 때까지 캐싱된 파일을 사용합니다. 이는 블레이드가 본질적으로 성능상의 부담이 없음을 의미합니다. 블레이드 뷰 파일은 확장자로 `.blade.php`를 사용합니다.

## 템플릿의 상속 <a id="undefined-1"></a>

### 레이아웃 정의하기 <a id="undefined-5"></a>

블레이드를 사용할 때의 주된 장점 두가지는 _템플릿 상속_과 _섹션_입니다. 시작하기 전에 간단한 예제를 살펴보겠습니다. 첫번째로, 우리는 "master" 페이지 레이아웃을 보겠습니다. 대부분의 웹사이트는 여러 페이지에 걸쳐 동일한 레이아웃을 사용하기 때문에, 이 레이아웃을 하나의 블레이드 뷰로 정의하는 것이 편합니다.

```text
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

보는 바와 같이, 이 파일은 일반적인 HTML 마크업을 가지고 있습니다. 그런데 `@section` 과 `@yield` 지시어에 주목해 주십시오. `@section` 지시어는 이름에서도 알 수 있듯이 컨텐츠의 섹션을 정의하고 있고. 반대로 `@yield` 지시어는 주어진 섹션의 컨텐츠를 출력하고 있습니다.

이제 레이아웃은 정의했고, 이 레이아웃을 상속받을 자식 페이지를 정의해 보겠습니다.

### 레이아웃 확장하기 <a id="undefined-6"></a>

자식페이지를 정의할 때, `@extends` 지시어를 사용하여 자식 페이지에서 "상속"받을 레이아웃을 지정할 수 있습니다. 레이아웃을 상속\(`@extends`\)받는 뷰들은 `@section` 지시어를 사용해서 그 레이아웃의 섹션에 들어갈 컨텐츠를 주입해야 합니다. 앞의 예에서 보았듯이, 자식 페이지에서 주입한 섹션의 컨텐츠는 레이아웃의 `@yield` 부분에 출력될 것입니다.

```text
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

위의 예에서, `sidebar` 섹션은 레이아웃의 사이드바에 컨텐츠를 붙이기 위해 `@@parent` 지시어를 사용하고 있습니다. `@@parent` 지시어는 뷰가 렌더링 될 때, 그 레이아웃의 `sidebar` 섹션이 가지고 있는 컨텐츠로 대체될 것입니다.

블레이드 템플릿은 순수한 PHP 뷰와 마찬가지로 `view` 헬퍼를 사용하여 곧바로 반환될 수 있습니다.

```text
Route::get('blade', function () {    return view('child');});
```

## 데이터 출력하기 <a id="undefined-2"></a>

중괄호\(culry brace\)로 변수를 감싸면, 블레이드 뷰로 전달된 데이터를 출력할 수 있습니다. 예를 들어, 주어진 라우트가 있을 때:

```text
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

`name` 변수의 값을 다음과 같이 출력할 수 있습니다:

```text
Hello, {{ $name }}.
```

뷰로 전달된 변수들의 값을 출력하는 것에 별다른 제한은 없습니다. PHP 함수의 결과 값을 출력할 수도 있습니다. 블레이드의 데이터 출력 구문 안에는 어떤 PHP 코드도 들어갈 수 있습니다.

```text
The current UNIX timestamp is {{ time() }}.
```

> **주의:** `{{ $foo }}` 구문은 자동으로 PHP의 `htmlentities` 함수로 감싼 후 출력합니다. XSS 공격을 방어하기 위함입니다.

#### 블레이드 & 자바스크립트 프레임워크 <a id="and"></a>

Since many JavaScript frameworks also use "curly" braces to indicate a given expression should be displayed in the browser, you may use the `@` symbol to inform the Blade rendering engine an expression should remain untouched. For example:

많은 자바스크립트 프레임웍에서도 브라우저에 출력되어야 할 데이터를 표시하기 위해 중괄호\("curly" braces\)를 사용하고 있습니다. 이럴 경우, `@` 기호를 사용하면 블레이드 랜더링 엔진은 이 구문을 변환하지 않고 그대로 출력할 것입니다. 예를 들어:

```text
<h1>Laravel</h1>​Hello, @{{ name }}.
```

위의 예에서 `@` 기호는 블레이드에 의해서 제거될 것이고, 나머지 `{{ name }}` 구문은 블레이드 엔진에 의해 해석되지 않고 그대로 남아있게 되어, 자바스크립트 프레임워크에 의해 변환되어 집니다.

#### 데이터가 존재하는지 확인후 출력하기 <a id="undefined-7"></a>

Sometimes you may wish to echo a variable, but you aren't sure if the variable has been set. We can express this in verbose PHP code like so:

가끔 변수를 출력할 때, 그 변수가 실제로 존재하는지 확신하지 못할 때도 있습니다. 이때 다음과 같이 PHP 코드를 사용할 수 있습니다.

```text
{{ isset($name) ? $name : 'Default' }}
```

이렇게 3항연산자를 사용하는 대신, 블레이드는 다음과 같은 편리한 구문을 제공합니다:

```text
{{ $name or 'Default' }}
```

위의 예에서, 만약 `$name` 변수가 존재하면 그 값이 출력될 것이고, 존재하지 않는다면 대신 `Default`라는 문자가 출력될 것입니다.

#### 이스케이프하지 않고 데이터 출력하기 <a id="undefined-8"></a>

By default, Blade `{{ }}` statements are automatically sent through PHP's `htmlentities` function to prevent XSS attacks. If you do not want your data to be escaped, you may use the following syntax:

기본적으로, `{{ }}` 구문은 XSS 공격을 방어하기 위해 자동으로 PHP의 `htmlentities` 함수를 실행후 반환합니다. 만약 데이터를 `htmlentities` 함수를 거치지 않은채 출력하고 싶다면, 다음과 같은 구문을 사용하십시오.

```text
Hello, {!! $name !!}.
```

> **주의:** 사용자로부터 입력 된 내용을 표시 할 때에는 escape에 대한 매우 세심한 주의가 필요합니다. 컨텐츠의 HTML 엔티티를 escape 하기위해 항상 이중 중괄호 표기법을 사용하십시오.

## 제어문 <a id="undefined-3"></a>

블레이드는 템플릿 상속 및 데이터 출력과 더불어, 조건문이나 반복문과 같이 일반적인 PHP 제어문의 실행을 위해 편리하고 간결한 구문을 제공합니다. 이 구문들은 매우 깔끔하고 간단하면서도, 대응되는 PHP 제어문들과 비슷한 모습을 띄고 있습니다.

#### If문 <a id="if"></a>

`if` 문은 `@if`, `@elseif`, `@else`와 `@endif` 지시어를 사용하여 구성할 수 있습니다. 이 지시어들은 각각 대응되는 PHP 구문과 동일하게 작동합니다.

```text
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```

편의성을 위해, 블레이드는 `@unless` 지시어를 제공합니다.

```text
@unless (Auth::check())    You are not signed in.@endunless
```

#### 반복문 <a id="undefined-9"></a>

조건문과 더불어, 블레이드는 PHP가 지원하는 반복문 기능을 하는 간단한 지시어를 제공합니다. 다시 한번 말하지만, 각각의 지시어들은 대응하는 PHP 구문과 동일한 기능을 합니다.

```text
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
```

#### 서브 뷰 포함하기 <a id="undefined-10"></a>

`@include` 지시어는 손쉽게 블레이드 뷰를 현재의 뷰에 포함\(include\)시킬수 있도록 도와줍니다. 부모 뷰에서 사용할 수 있는 모든 변수는 포함된 서브 뷰에서도 사용할 수 있습니다.

```text
<div>
    @include('shared.errors')

    <form>
        <!-- Form Contents -->
    </form>
</div>
```

서브 뷰는 부모 뷰의 모든 데이터를 상속받겠지만, 그 외에 더 많은 데이터를 배열 형식으로 전달할 수 있습니다.

```text
@include('view.name', ['some' => 'data'])
```

> **주의:** 블레이드 뷰 안에서 `__DIR__`과 `__FILE__`과 같은 상수를 사용하지 마십시오. 블레이드 뷰는 캐싱된 뷰의 위치를 참조하기 때문입니다.

#### 컬렉션을 뷰에서 렌더링하기 <a id="undefined-11"></a>

블레이드의 `@each` 지시어를 사용하면 반복문\(loop\)과 `include` 구문을 한 줄로 합칠 수 있습니다.

```text
@each('view.name', $jobs, 'job')
```

첫번째 인자는 배열이나 컬렉션의 각 요소를 렌더링하기 위한 서브 뷰의 이름입니다. 두번째 인자는 반복 처리하는 배열이나 컬렉션이며 세번째 인수는 뷰에서의 반복값이 대입되는 변수의 이름입니다. 예를 들어 `jobs` 배열을 반복 처리하려면 보통 서브 뷰에서 각 과제를 `job` 변수로 접근해야 할 것입니다.

또한 `@each` 지시어로 네번째 인수를 전달할 수도 있습니다. 이 인자는 특정 배열이 비었을 경우 렌더링될 뷰를 결정합니다.

```text
@each('view.name', $jobs, 'job', 'view.empty')
```

#### 주석 <a id="undefined-12"></a>

블레이드는 또한 뷰에 주석을 정의할 수 있습니다. 하지만 HTML 주석과는 다르게, 블레이드 주석은 브라우저로 전송되는 HTML에 포함되어 있지 않습니다:

```text
{{-- This comment will not be present in the rendered HTML --}}
```

## 서비스 주입하기 <a id="undefined-4"></a>

`@inject` 지시어는 서비스 컨테이너로부터 서비스를 조회하는 데에 사용될 수 있습니다. `@inject`에 전달된 첫번째 인자는 서비스가 할당될 변수의 이름이며 두번째 인자는 주입하려는 서비스의 클래스/인터페이스의 이름입니다:

```text
@inject('metrics', 'App\Services\MetricsService')​<div>    Monthly Revenue: {{ $metrics->monthlyRevenue() }}.</div>
```

