# 다이나믹 필드 스킨 제작 방법

## 다이나믹 필드 스킨

사이트 관리자는 다이나믹 필드를 이용해서 사용자 입력 필드를 추가할 수 있습니다. 추가된 다이나믹 필드는 데이터베이스 테이블 구조 및 데이터 입출력을 제어하는 [_필드 타입_](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/component-dynamicField.html)과 디자인 요소를 처리하는 _필드 스킨_으로 구분됩니다.

_필드 스킨_은 등록, 수정, 검색 폼, 상세 보기 등의 디자인을 구성할 수 있도록 구조를 제공합니다.

### AbstractSkin

스킨을 만들때 사용하는 추상클래스 입니다.

```php
class DefaultSkin extends AbstractSkin {}
```

### 빈 스킨 생성 \(draft\)

스킨 생성 커맨드를 사용하려면 플러그인과 대상 다이나믹 필드가 마련되어 있어야 합니다. 플러그인 생성과 다이나믹 필드 생성은 [다이나믹 필드 매뉴얼](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/component-dynamicField.html)을 참고 바랍니다.

아래 커맨드로 스킨을 만들 수 있습니다.

```text
$ php artisan make:fieldSkin <plugin> <name> <dynamic_field_id>
```

`plugin`는 스킨이 위치할 플러그 경로입니다. 플러그인 디렉토리 명을 입력합니다.

`name`에는 스킨의 아이디를 입력합니다.

`dynamic_field_id`에는 스킨이 사용될 대상의 다이나믹필드아이디를 입력합니다. `SampleType::getId()`를 통해 확인합니다. 해당 다이나믹필드가 있는 플러그인의 `composer.json`파일을 통해서도 확인할 수 있습니다.

#### 컴포넌트 아이디

컴포넌트 아이디는 아래와 같은 규칙으로 작성합니다.

```text
<dynamic_field_id>/fieldSkin/<plugin>@<name>
```

`dynamic_field_id` 스킨이 사용될 대상 다이나믹 필드 아이디 입니다.

`plugin`는 플러그인 디렉토리 이름입니다.

`name` 스킨의 id입니다.

### 스킨 등록

커맨드를 사용할 경우, 자동으로 등록됩니다. 플러그인의 composer.json 파일에 아래와 같이 컴포넌트 정보가 등록되어 있습니다.

```javascript
// plugins/my_plugin/composer.json
...
  "extra": {
        "xpressengine": {
            "title": "my plugin",
            "component": {
                "fieldType/my_plugin@my_field/fieldSkin/my_plugin@my_skin": {
                    "class": "GilDongHong\\XePlugin\\MyPlugin\\DynamicFieldSkins\\MySkin\\MySkin",
                    "name": "MySkin fieldSkin",
                    "description": "The fieldSkin supported by My_plugin plugin."
                }
            }
        }
    },

    ...
```

### 디자인 파일 처리

AbstractSkin 추상 클래스는 제작자가 스킨구현에 집중할 수 있도록 blade 템플릿 파일에서 사용할 데이터를 처리하여 제공합니다. 제작자가 구현체 클래스에 `getPath()`를 구현하여 블레이드 템플릿 파일이 있는 디렉토리 경로를 설정하면 이 추상 클래스는 스킨에서 사용해야할 설정과 데이터베이스 데이터를 전달합니다. 커맨드를 이용해 생성할 경우 자동으로 생성된 블레이드파일의 경로를 지정하고 있습니다.

#### 전달 데이터

AbstractSkin 이 View 로 전달하는 데이터는 아래와 같습니다.

* `$config` 다이나믹 필드를 만들때 관리자에서 입력한 설정값 입니다.
* `$data` 데이터베이스 테이블에 등록된 정보 입니다.
* `$key` 다이나믹 필드에서 정의한 데이터베이스 컬럼 이름과 실제 사용하는 입력 필드 이름의 정보입니다.

스킨에서 만들어야 하는 템플릿 파일은 아래와 같습니다. 커맨드를 이용해 생성할 경우 자동으로 비어있는 블레이드파일이 `views` 디렉토리안에 생성되어있습니다.

* `create.blade.php` 회원 등록, 새글 작성에 사용합니다. 
* `edit.blade.php` 회원 정보 수정, 글 수정에 사용합니다.
* `show.blade.php` 회원 정보 보기, 작성된 글 보기 페이지에 사용 합니다.
* `search.blade.php` 게시판 리스트의 상세 검색 폼에 사용합니다.
* `settings.blade.php` 관리자에서 다이나믹 필드를 생성할 때 설정 값을 입력 받기위해 사용합니다.

