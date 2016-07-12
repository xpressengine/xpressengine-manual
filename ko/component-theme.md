# 테마 제작 가이드

테마 컴포넌트는 다른 컴포넌트와는 달리 테마 생성 커맨드를 제공합니다. 테마 생성 커맨드를 사용하면, 전문적인 PHP 지식이 없어도 테마를 제작할 수 있도록 테마를 구성하는 여러가지 파일을 자동으로 생성해 주고, 테마를 XE에 등록해줍니다.


## 테마 생성

빈 테마를 생성하려면 우선 테마가 소속될 플러그인이 있어야 합니다. 만약 플러그인이 아직 준비되지 않았다면, [플러그인 생성하기](plugin-generation.md) 문서를 참고하시기 바랍니다.

플러그인이 준비되었다면 아래 커맨드를 사용하여 빈 테마를 생성합니다.

```php
$ php artisan make:theme <path> <title>
```

`path`는 테마가 위치할 디렉토리의 경로입니다. 이 경로는 플러그인 디렉토리이름을 포함하여 입력해줍니다.

`title`은 테마의 제목입니다. 지정한 테마 제목은 사이트 관리자에서 테마의 이름으로 표시됩니다.

만약 `my_plugin` 플러그인에 테마를 넣고, 테마 이름을 `First Theme`로 지정하고 싶을 경우 아래와 같이 커맨드를 실행합니다.

```php
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

생성되는 테마의 간단한 정보를 터미널에서 볼 수 있습니다.

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


#### assets

테마에 필요한 `.js`나 `.css` 파일 그리고 이미지 파일과 같은 assets 파일을 담는 디렉토리입니다.

#### views

테마를 출력할 때 사용하는 템플릿 파일을 저장하는 디렉토리입니다. 템플릿 파일은 blade 템플릿 언어로 작성되어야 합니다. blade 템플릿 언어의 사용법은 [템플릿]() 문서에 자세히 기술되어 있습니다.

#### Theme.php
`Theme.php`는 테마클래스 파일입니다. 테마 생성 커맨드로 생성된 테마의 클래스는 `\Xpressengine\Theme\GenericTheme` 클래스를 상속받고 있습니다. `GenericTheme` 클래스는 테마 컴포넌트의 추상클래스인 `\Xpressengine\Theme\AbstractTheme`를 상속받고 있습니다.

```
\Xpressengine\Theme\AbstractTheme
└── \Xpressengine\Theme\GenericTheme
    └── Theme(Theme.php)
```

PHP 개발자가 아닌 테마 제작자가 테마 클래스를 직접 작성하는 것은 쉬운 일이 아닙니다.  `GenericTheme` 클래스는 테마 제작자들에게 규격화 된 테마 구조를 제공함으로써 테마 제작을 손쉽게 할 수 있도록 도와줍니다. `Theme.php` 파일을 처음 생성한 다음부터는 거의 직접 수정할 필요가 없습니다. `Theme.php`를 직접 수정하는 대신, `info.php`를 수정하시면 됩니다.

일반적으로 `Theme.php` 파일을 직접 수정하지 않아도 원하는 테마를 제작하는 데에는 문제가 없습니다. 다만, 매우 고도화 된 테마를 제작하고 싶거나, 특별한 기능을 테마에 추가하고 싶다면 `Theme.php` 파일을 직접 수정하셔도 됩니다. 훨씬 자유롭고 편하게 테마를 제작할 수 있습니다. 물론 그 전에 테마 클래스의 각 메소드의 역할과 원리를 이해하고 있어야 합니다.

#### info.php

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

`view` 필드는 테마를 출력할 때 사용할 템플릿 파일을 지정하는 필드입니다. 위 설정의 경우 `views` 디렉토리에 들어있는 `theme.blade.php`을 지정한 것입니다.

`setting` 필드에는 테마 설정 페이지에서 사용할 설정 항목에 대한 정보를 지정합니다.

`support` 필드에는 이 테마가 데스크탑 버전과 모바일 버전을 각각 지원하는지를 지정합니다.

`editable` 필드에는 테마 편집 페이지에서 편집할 수 있는 파일의 목록을 지정합니다.


## 테마 출력

테마가 웹페이지에 출력될 때 사용될 템플릿은 `info.php` 파일의 `view` 필드에 지정되어 있습니다. 위 설정의 경우 실제로는 `views/theme.blade.php` 파일에 해당됩니다.

### 메인컨텐츠 출력하기

웹페이지가 출력될 때, 테마는 그 웹페이지의 메인컨텐츠를 출력해줘야 합니다. 메인컨텐츠는 `$content` 변수에 담겨 있습니다.

`theme.blade.php` 파일에서 메인컨텐츠가 출력될 위치에 아래와 같이 삽입하십시오.

```
{!! $content !!}
```

### 템플릿 파일 분리하기

테마에서 출력할 내용이 많아지면 `theme.blade.php`이 너무 복잡해집니다. 이럴 때 블레이드 문법의 `@extends`, `@section`, `@yield` `@include` 키워드를 잘 사용하면 템플릿 파일을 분리할 수 있습니다.

`theme.blade.php`에서 `@include`를 사용하여 header와 footer 영역을 분리해보았습니다.

```php
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

테마의 템플릿 파일에서 뷰이름을 지정할 때, `$theme::view()` 메소드를 사용하십시오. 이 메소드는 템플릿 파일의 경로를 지정할 때, `views` 디렉토리를 기준으로 하는 상대경로로 지정할 수 있도록 도와줍니다.



## 테마 편집

## 테마 설정




