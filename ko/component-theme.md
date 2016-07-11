# 테마 제작 가이드

테마 컴포넌트는 다른 컴포넌트와는 달리 테마 생성 커맨드를 제공합니다. 테마 생성 커맨드를 사용하면, 전문적인 PHP 지식이 없어도 테마를 제작할 수 있도록 테마를 구성하는 여러가지 파일을 자동으로 생성해 주고, 테마를 XE에 등록해줍니다.


## 테마 생성

빈 테마를 생성하려면 우선 테마가 위치할 플러그인이 있어야 합니다. 만약 플러그인이 아직 준비되지 않았다면, [플러그인 생성하기](plugin-generation.md)를 참고하세요.

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

생성된 테마는 아래의 디렉토리 구조를 가집니다.
```
plugins/my_plugin/theme/
├── Theme.php
├── assets
│   └── css
│       └── theme.css
├── info.php
└── views
    ├── gnb.blade.php
    └── theme.blade.php
```




테마 출력

테마 편집

테마 설정




