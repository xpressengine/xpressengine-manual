# 테마 제작 방법

이 문서는 테마 생성 커맨드를 사용한 보편적인 방법의 테마 제작 방법을 설명합니다.

## 빈 테마 생성

이 항목을 시작하기전에, 꼭 빈 플러그인 또는 이 테마가 담겨진 플러그인을 미리 생성해주세요.  
플러그인 생성하는 방법은 여기를 클릭 해서 볼 수 있어요.

`플러그인 > 설치된 플러그인 > 직접 설치한 플러그인` 페이지에서 테마 생성 버튼을 눌러주세요.

![&#xC870;&#xAE08; &#xB354; &#xC26C;&#xC6B4; &#xC774;&#xD574;&#xB97C; &#xC704;&#xD558;&#xC5EC;! ](../.gitbook/assets/undefined%20%281%29.gif)

본격적으로 테마를 제작해보겠습니다.

테마가 웹페이지에 출력될 때 사용될 템플릿은 `info.php` 파일의 `view` 필드에 지정되어 있습니다. 위 설정의 경우 실제로는 `views/theme.blade.php` 파일에 해당됩니다.

### 메인컨텐츠 출력하기

웹페이지가 출력될 때, 테마는 그 웹페이지의 메인컨텐츠를 반드시 출력해줘야 합니다. XE는 웹페이지를 출력할 때, 메인컨텐츠의 내용을 $content 변수에 담아 테마에 전달합니다.

메인컨텐츠가 출력될 영역에 아래와 같이 변수를 출력하십시오.

```markup
<div class="content" id="content">
{!! $content !!}
</div>
```

### 레이아웃 구성하기

테마에서 출력할 내용이 많아지면 `theme.blade.php`이 너무 복잡해집니다. 이럴 때 블레이드 문법의 `@extends`, `@section`, `@yield` `@include` 키워드를 사용하면 템플릿 파일을 분리할 수 있습니다. 레이아웃을 구성하는 블레이드 문법의 사용법은 [템플릿 문서](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/blade.md)에 자세히 설명되어 있습니다.

`theme.blade.php`에서 `@include`를 사용하여 header와 footer 영역을 분리해보았습니다.

```markup
<!-- theme.blade.php -->

@include($theme::view('header'))

<div class="content" id="content">
{!! $content !!}
</div>

@include($theme::view('footer'))
```

header와 footer에 해당하는 파일은 `views` 디렉토리에 미리 추가되어 있어야 합니다.

```text
..
└── views/
    ├── header.blade.php
    ├── footer.blade.php    
    └── theme.blade.php
```

테마의 템플릿 파일에서 뷰이름을 지정할 때에는 `$theme::view()` 메소드를 사용하십시오. 이 메소드는 `views` 디렉토리를 기준으로 하는 상대경로로 템플릿 파일의 경로를 지정할 수 있도록 도와줍니다.

### asset 파일 로드하기

#### 이미지 파일 로드하기

이미지 파일을 로드하려면 `$theme::asset()` 메소드를 사용하십시오. `assets` 디렉토리를 기준으로 파일의 상대경로를 입력해주시면 됩니다.

```markup
<!-- theme.blade.php -->

<img src="{{ $theme::asset('img/logo.png') }}" alt="로고 이미지">
```

#### css, js 파일 로드하기

css, js 파일은 `XeFrontend` 파사드\(Frontend 서비스\)를 사용하여 로드하십시오.

```markup
<!-- theme.blade.php -->

{{ XeFrontend::css(
    $theme::asset('css/theme.css')
)->load() }}
```

역시 `$theme::asset()` 메소드를 사용하면 상대경로로 파일경로를 입력할 수 있습니다.

`XeFrontend` 파사드의 자세한 사용법은 [Frontend 서비스 문서]()를 참고하십시오.

### 테마 설정 변수 사용하기

사이트 관리자가 테마 설정 페이지를 통해 지정한 설정들의 값은 테마를 출력할 때 `$config` 변수를 사용하여 참조할 수 있습니다.

```markup
<!-- theme.blade.php -->

@if($config->get('use_sidebar', false))
<div class="sidebar">
...
</div>
@endif
```

`$config` 변수는 `ConfigEntity`의 인스턴스입니다. `get` 메소드를 사용하면 손쉽게 설정값을 가져올 수 있습니다. `get` 메소드의 두번째 파라메터는 기본값\(default value\)입니다.

```php
// use_sidebar 설정 조회, use_sidebar 설정이 존재하지 않을 경우 기본값으로 false를 반환
$config->get('use_sidebar', false);
```

#### 메뉴 출력하기

XE에 등록된 메뉴도 테마 설정 변수로 저장할 수 있습니다.

만약 설정변수에 'mainMenu'라는 이름으로 메뉴가 저장되어 있다면, `menu_list` 함수를 사용하여 메뉴 정보를 가져올 수 있습니다.

```php
@foreach(menu_list($config->get('mainMenu')) as $menu)
  ...
@endforeach
```

`menu_list`는 지정된 메뉴에 속해 있는 메뉴 아이템의 목록을 배열로 반환합니다. 위의 예제에서는 `$menu` 변수가 하나의 메뉴 아이템입니다.

메뉴 아이템은 아래와 같은 프로퍼티를 가지고 있습니다. 이 프로퍼티를 사용하여 메뉴를 원하는대로 출력하십시오.

* `$menu['url']` - 메뉴의 링크주소
* `$menu['link']` - 메뉴의 링크텍스트, 만약 메뉴 이미지가 사용됐을 경우 이미지를 출력하는 태그를 출력합니다.
* `$menu['selected']` - 현재 페이지에 해당하는 메뉴일 경우 `true`를 가집니다.
* `$menu['children']` - 자식 메뉴아이템 리스트

#### 업로드한 이미지 출력하기

테마 설정페이지에서 사이트 관리자는 이미지를 업로드할 수 있습니다.

사이트 관리자가 업로드한 이미지를 출력할 때에는 아래와 같이 코드를 작성하십시오.

```markup
<img src="{{$config->get('logoImage.path')}}">
```

이미지를 저장한 변수명\(`logoImage`\)에 `.path`를 붙여서 출력하면 됩니다.

## 테마 설정

테마는 사이트 관리자는 테마를 커스터마이징할 수 있도록 테마 설정 페이지를 제공할 수 있습니다. 테마 설정 페이지를 작성하는 방법은 크게 두가지가 있습니다.

### 폼 빌더로 작성하기

폼 빌더로 작성하는 방법을 사용하면 손쉽게 테마 설정 페이지를 작성할 수 있습니다.

`info.php` 파일의 `setting` 항목에 폼에서 사용할 필드의 정보를 담은 배열을 추가합니다. `setting` 항목을 작성하는 규칙은 폼 빌 문서의 `fields` 항목을 참고하시기 바랍니다.

### 템플릿 파일로 작성하기

폼 빌더를 사용할 경우, 간편하게 테마 설정 페이지를 작성할 수는 있지만, 자유롭지 못한 단점이 있습니다. 좀 더 자유롭게 설정 페이지를 작성하고 싶다면 설정 페이지를 출력하는 블레이드 템플릿 파일을 작성하십시오. 그리고 `info.php` 파일의 `setting` 항목에 블레이드 템플릿 파일의 경로를 지정하면 됩니다.

이때 템플릿 파일은 반드시 `views` 디렉토리에 존재해야 합니다. 만약 `views/config.blade.php`를 설정 페이지의 템플릿으  
로 사용하고 싶다면, 아래와 같이 `setting` 항목을 지정합니다.

```php
<?php
return [
  ...

  'setting' => 'config',

  ...
];
```

## 테마 편집

사이트 관리자는 테마의 템플릿을 직접 편집하여 테마를 커스터마이징 할 수 있습니다. 테마 제작자는 사이트관리자가 편집할 수 있는 파일 목록을 지정해주어야 합니다.

`info.php` 파일의 `editable` 필드에 편집할 수 있는 템플릿 파일 목록을 지정해주십시오.



