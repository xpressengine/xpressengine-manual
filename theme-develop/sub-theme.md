# 서브테마 활용
## 서브테마 구성

테마에서 출력해야할 내용이 많아지거나, 설정에 따라서 보여줘야할 항목이 다르다면 `theme.blade.php`이 너무 복잡해집니다.
이때 블레이드 문법의  `@extends`, `@section`, `@yield` `@include` 키워드를 사용하면 템플릿 파일을 분리할 수 있습니다. 
레이아웃을 구성하는 블레이드의 사용법은 [템플릿 문서](../developer-docs/template-blade.md)에 자세히 설명되어 있습니다.

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