# 테마 제작 가이드

테마 컴포넌트는 다른 컴포넌트와는 달리 테마 생성 커맨드를 제공합니다. 테마 생성 커맨드를 사용하면, 전문적인 PHP 지식이 없어도 테마를 제작할 수 있도록 테마를 구성하는 여러가지 파일을 자동으로 생성해 주고, 테마를 XE에 등록까지 해줍니다.


## 빈 테마 생성

테마 생성 커맨드를 사용하려면 우선 테마가 소속될 플러그인이 마련되어 있어야 합니다. 아직 플러그인이 준비되지 않았다면, [플러그인 생성하기](plugin-generation.md) 문서를 참고하여 플러그인을 생성하길 바랍니다.

플러그인이 준비되었다면 아래 커맨드를 사용하여 빈 테마를 생성합니다.

```php
$ php artisan make:theme <path> <title>
```

`path`는 테마가 위치할 디렉토리의 경로입니다. 플러그인의 디렉토리 이름을 포함한 경로를 입력해줍니다.

`title`에는 테마의 제목을 지정해 주십시오. 지정한 테마 제목은 사이트 관리자에서 테마의 이름으로 표시됩니다.

만약 `my_plugin` 플러그인에 테마를 넣고, 테마 이름을 `First Theme`로 지정하고 싶다면, 아래와 같이 커맨드를 실행하시면 됩니다. 커맨드를 실행하면 생성되는 테마의 개략적인 정보를 터미널에서 볼 수 있습니다.

```
$ php artisan make:theme my_plugin/theme 'First Theme'

[New theme info]
  plugin:        my_plugin
  path:          my_plugin/theme
  class file:    my_plugin/theme/Theme.php
  class name:    SungbumHong\MyPlugin\Theme
  id:            theme/my_plugin@theme
  title:         First Theme
  description:   The Theme supported by My_plugin plugin.

 Do you want to add theme? [yes|no]:
 > yes

Generating autoload files
Theme is created successfully.
```

생성된 테마는 아래의 디렉토리 구조를 가집니다. `plugins/my_plugin/theme/` 디렉토리는 테마의 모든 파일이 담겨 있는 '테마 디렉토리'입니다. 
```
plugins/my_plugin/theme/
├── Theme.php
├── assets/
│   └── css/
│       └── theme.css
├── info.php
└── views/
    ├── gnb.blade.php
    └── theme.blade.php
```


#### assets 파일

테마에 필요한 `.js`나 `.css` 파일 그리고 이미지 파일과 같은 asset 파일을 담는 디렉토리입니다.

#### views 디렉토리

테마를 출력할 때 사용하는 템플릿 파일을 저장하는 디렉토리입니다. 템플릿 파일은 blade 템플릿 언어로 작성되어야 합니다. blade 템플릿 언어의 사용법은 [템플릿]() 문서에 자세히 기술되어 있습니다.

#### Theme.php 파일
`Theme.php`는 테마클래스 파일입니다. 테마 생성 커맨드로 생성된 테마의 클래스는 `\Xpressengine\Theme\GenericTheme` 클래스를 상속받고 있습니다. 또, `GenericTheme` 클래스는 테마 컴포넌트의 추상클래스인 `\Xpressengine\Theme\AbstractTheme`를 상속받고 있습니다.

```
\Xpressengine\Theme\AbstractTheme
└── \Xpressengine\Theme\GenericTheme
    └── Theme(Theme.php)
```

PHP 개발자가 아닌 테마 제작자가 테마 클래스를 직접 작성하는 것은 쉬운 일이 아닙니다.  `GenericTheme` 클래스는 테마 제작자들에게 규격화 된 테마 구조를 제공함으로써 테마 제작을 손쉽게 할 수 있도록 도와줍니다. `Theme.php` 파일을 처음 생성한 다음부터는 거의 직접 수정할 필요가 없습니다. `Theme.php`를 직접 수정하는 대신, `info.php`를 수정하십시오.

일반적으로 `Theme.php` 파일을 직접 수정하지 않아도 원하는 테마를 제작하는 데에는 문제가 없습니다. 다만, 좀더 고수준의 테마를 제작하고 싶거나, 특별한 기능을 테마에 추가하고 싶다면 `Theme.php` 파일을 직접 수정하셔도 됩니다. `Theme.php` 파일을 수정하면 훨씬 자유롭고 편하게 테마를 제작할 수 있습니다. 물론 그 전에 테마 클래스의 각 메소드가 갖는 역할과 원리를 잘 이해하고 있어야 합니다.

#### info.php 파일

`info.php` 파일은 테마의 구동에 필요한 여러가지 정보를 입력하는 파일입니다. 간단한 php 문법으로 쉽게 작성할 수 있습니다.

```php
<?php
return [
    'view' => 'theme',
    'setting' => [
        ...
    ],
    'support' => [
        'mobile' => true,
        'desktop' => true
    ],
    'editable' => [
        'views' => [
            'theme.blade.php',
            'gnb.blade.php',
        ],
        'assets' => [
            'css/theme.css'
        ]
    ]
];
```

`view` 필드는 테마를 출력할 때 사용할 템플릿 파일을 지정하는 필드입니다. 위 설정의 경우 `theme`로 지정되어 있습니다. 이는 `views` 디렉토리에 들어있는 `theme.blade.php`을 지정한 것입니다.

`setting` 필드에는 테마 설정 페이지에서 사용할 설정 항목에 대한 정보를 지정합니다.

`support` 필드에는 이 테마가 데스크탑 버전과 모바일 버전을 지원하는지를 지정합니다.

`editable` 필드에는 테마 편집 페이지에서 편집할 수 있는 파일의 목록을 지정합니다.


## 테마 출력

테마가 웹페이지에 출력될 때 사용될 템플릿은 `info.php` 파일의 `view` 필드에 지정되어 있습니다. 위 설정의 경우 실제로는 `views/theme.blade.php` 파일에 해당됩니다.

### 메인컨텐츠 출력하기

웹페이지가 출력될 때, 테마는 그 웹페이지의 메인컨텐츠를 반드시 출력해줘야 합니다. XE는 웹페이지를 출력할 때, 메인컨텐츠의 내용을 $content 변수에 담아 테마에 전달합니다.

메인컨텐츠가 출력될 영역에 아래와 같이 변수를 출력하십시오.

```html
<div class="content" id="content">
{!! $content !!}
</div>
```

### 레이아웃 구성하기

테마에서 출력할 내용이 많아지면 `theme.blade.php`이 너무 복잡해집니다. 이럴 때 블레이드 문법의 `@extends`, `@section`, `@yield` `@include` 키워드를 사용하면 템플릿 파일을 분리할 수 있습니다.

`theme.blade.php`에서 `@include`를 사용하여 header와 footer 영역을 분리해보았습니다.

```html
<!-- theme.blade.php -->

@include($theme::view('header'))

<div class="content" id="content">
{!! $content !!}
</div>

@include($theme::view('footer'))
```

header와 footer에 해당하는 파일은 `views` 디렉토리에 미리 추가되어 있어야 합니다.

```
..
└── views/
    ├── header.blade.php
    ├── footer.blade.php    
    └── theme.blade.php
```

테마의 템플릿 파일에서 뷰이름을 지정할 때에는 `$theme::view()` 메소드를 사용하십시오. 이 메소드는 `views` 디렉토리를 기준으로 하는 상대경로로 템플릿 파일의 경로를 지정할 수 있도록 도와줍니다.


### 테마 설정 변수 사용하기

사이트 관리자가 테마 설정 페이지를 통해 지정한 설정들의 값은 테마를 출력할 때 `$config` 변수를 사용하여 참조할 수 있습니다.

```html
@if($config->get('use_sidebar', false))
<div class="sidebar">
...
</div>
@endif
```

`$config` 변수는 `ConfigEntity`의 인스턴스입니다. `get` 메소드를 사용하면 손쉽게 설정값을 가져올 수 있습니다. `get` 메소드의 두번째 파라메터는 기본값(default value)입니다.

```php
// use_sidebar 설정 조회, use_sidebar 설정이 존재하지 않을 경우 기본값으로 false를 반환
$config->get('use_sidebar', false);
```


### asset 파일 로드하기

#### 이미지 파일 로드하기

이미지 파일을 로드하려면 `$theme::asset()` 메소드를 사용하십시오. `assets` 디렉토리를 기준으로 파일의 상대경로를 입력해주시면 됩니다.

```html
<img src="{{ $theme::asset('img/logo.png') }}" alt="로고 이미지">
```

#### css, js 파일 로드하기

css, js 파일은 `XeFrontend` 파사드(Frontend 서비스)를 사용하여 로드하십시오.

```php
{{ XeFrontend::css(
    $theme::asset('css/theme.css')
)->load() }}
```

역시 `$theme::asset()` 메소드를 사용하면 상대경로로 파일경로를 입력할 수 있습니다.

`XeFrontend` 파사드의 자세한 사용법은 [Frontend 서비스 문서]()를 참고하십시오.


### 메뉴 출력하기

...

### 업로드한 이미지 출력하기

...

## 테마 설정

테마는 사이트 관리자는 테마를 커스터마이징할 수 있도록 테마 설정 페이지를 제공할 수 있습니다. 테마 설정 페이지를 작성하는 방법은 크게 두가지가 있습니다.

### 폼빌더로 작성하기

...


### 템플릿 파일로 작성하기

...




## 테마 편집

사이트 관리자는 테마의 템플릿이나 스타일시트(css) 파일을 직접 편집하여 테마를 커스터마이징 할 수 있습니다. 테마 제작자는 사이트관리자가 편집할 수 있는 파일 목록을 지정해주어야 합니다.

`info.php` 파일의 `editable` 필드에 편집할 수 있는 파일 목록을 지정해주십시오.
