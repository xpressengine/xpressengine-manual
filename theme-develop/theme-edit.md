# 테마 편집
## 테마 편집

사이트 관리자는 테마의 템플릿을 직접 편집하여 테마를 커스터마이징 할 수 있습니다. 테마 제작자는 사이트관리자가 편집할 수 있는 파일 목록을 지정해주어야 합니다.

`info.php` 파일의 `editable` 필드에 편집할 수 있는 템플릿 파일 목록을 지정해주십시오.

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

### 메뉴 출력하기

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

### 업로드한 이미지 출력하기

테마 설정페이지에서 사이트 관리자는 이미지를 업로드할 수 있습니다.

사이트 관리자가 업로드한 이미지를 출력할 때에는 아래와 같이 코드를 작성하십시오.

```markup
<img src="{{$config->get('logoImage.path')}}">
```

이미지를 저장한 변수명\(`logoImage`\)에 `.path`를 붙여서 출력하면 됩니다.