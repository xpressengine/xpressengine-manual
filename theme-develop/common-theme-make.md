# 테마 제작 방법

테마를 만들기 위해서는, 플러그인에 테마를 포함시켜야 합니다. 플러그인 생성을 먼저 한 후 아래의 순서를 따라해주세요.

XE에서는 터미널(SSH)와, 웹 환경에서 테마를 생ㅅ어할 수 있는 방법을 제공하고 있습니다.

## 빈 테마 생성 (터미널 환경)
```php
$ php artisan make:theme <plugin> <name>
```

`plugin` 은 테마가 소속될 플러그인입니다. 플러그인이 위치한 디렉토리 이름을 입력해줍니다.

`name`에는 테마의 제목을 지정해 주십시오. 지정한 테마 제목은 사이트 관리자에서 테마의 이름으로 표시됩니다.

만약 `my_plugin` 플러그인 [기본 플러그인 생성](../plugin-develop/common-plugin-make.md)의 예제를 그대로 사용합니다.\)에 테마를 넣고, 테마 이름을 `First Theme`로 지정하고 싶다면, 아래와 같이 커맨드를 실행하시면 됩니다.
커맨드를 실행하면 생성되는 테마의 개략적인 정보를 터미널에서 볼 수 있습니다.

```text
$ php artisan make:theme my_plugin 'First Theme'

[New theme info]
  plugin:        my_plugin
  path:          src/Themes/FirstTheme
  class file:    src/Themes/FirstTheme/FirstThemeTheme.php
  class name:    GilDongHong\XePlugin\MyPlugin\Themes\FirstTheme\FirstThemeTheme
  id:            theme/my_plugin@first theme
  title:         FirstTheme theme
  description:   The Theme supported by My_plugin plugin.

 Do you want to add theme? [yes|no]:
 > yes

Generating autoload files
Theme is created successfully.
```


## 빈 테마 생성 (웹 환경)
>주의   
>본 환경은 XE 3.0.2 이후로는 지원하지 않습니다.

`플러그인 > 설치된 플러그인 > 직접 설치한 플러그인` 페이지에서 테마 생성 버튼을 눌러주세요.

![&#xC870;&#xAE08; &#xB354; &#xC26C;&#xC6B4; &#xC774;&#xD574;&#xB97C; &#xC704;&#xD558;&#xC5EC;! ](../.gitbook/assets/undefined%20%281%29.gif)

본격적으로 테마를 제작해보겠습니다.

테마가 웹페이지에 출력될 때 사용될 템플릿은 `info.php` 파일의 `view` 필드에 지정되어 있습니다. 위 설정의 경우 실제로는 `views/theme.blade.php` 파일에 해당됩니다.

### 컨텐츠를 출력하는 방법
테마를 만들때 게시판이나 위젯, 페이지 등을 보여줘야 합니다.
XE는 웹페이지를 출력할때 컨텐츠의 내용을 $content 변수에 담아 테마에 전달해 보여줍니다.

컨텐츠가 출력될 파일의 영역에 아래와 같이 변수를 출력해주세요.

```markup
<div class="theme_example_content" id="content_presenter">
{!! $content !!}
</div>
```

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

`XeFrontend` 파사드의 자세한 사용법은 [Frontend 서비스 문서](../developer-docs/service.md)를 참고하십시오.
