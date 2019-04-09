# 다이나믹 필드 제작 방법

## 다이나믹 필드

사이트 관리자가 사용자 입력 필드를 추가할 수 있는 기능을 제공합니다. 회원, 게시판 관리 페이지에서 기능을 제공하고 있습니다. 입력 필드 추가 기능은 다른 서드 파티 플러그인 에서도 제공할 수 있습니다.

다이나믹 필드는 다양한 형태를 제공할 수 있도록 설계되었습니다. AbstractType 클래스로 데이터를 처리하기 위한 형태만 제한하고 다이나믹 필드 제작자가 유연하게 구현할 수 있도록 했습니다. 다이나믹 필드는 데이터를 처리하는 _필드 타입_과 출력에 필요한 처리를 담당하는 필드 스킨 으로 구성 됩니다. 여기는 필드 타입 에 대한 설명입니다.

### AbstractType

다이나믹 필드를 만들때 사용 되는 추상클래스 입니다. 모든 다이나믹 필드는 반드시 이 추상클래스를 사용합니다. 이것은 각 구현체가 제공하기 위한 필요한 데이터베이스 테이블 컬럼의 정의와 데이터를 처리하는데 집중할 수 있도록 해줍니다.

```php
class Address extends AbstractType {}

class Category extends AbstractType {}
```

데이터베이스 테이블 생성,삭제 그리고 데이터 등록,수정,삭제 및 검색에 필요한 요소를 구현했습니다. 제작자는 다이나믹 필드의 이름, 설명, 데이터베이스 테이블 컬럼 구성등의 정보만 처리하여 새로운 필드를 만들 수 있습니다.

### 빈 다이나믹 필드 생성 \(draft\)

다이나믹 필드 생성 커맨드를 사용하려면 우선 플러그인이 마련되어 있어야 합니다. 플러그인 생성은 [플러그인 개발 시작하기](../d50c-b7ec-adf8-c778/plugin-generation.md)를 참고 바랍니다.

아래 커맨드로 빈 다이나믹 필드를 만들 수 있습니다.

```text
$ php artisan make:field <plugin> <name>
```

`plugin`은 다이나믹 필드가 위치할 플러그인 이름입니다. 플러그인 디렉토리 명을 입력합니다.  
`name`에는 다이나믹 필드의 아이디를 입력합니다.

#### 컴포넌트 아이디

컴포넌트 아이디는 아래와 같은 규칙으로 작성합니다.

```text
fieldType/<plugin>@<name>
```

`plugin`은 플러그인 디렉토리 이름이고 `name`는 다이나믹 필드의 아이디 입니다.

### 다이나믹 필드 등록

커맨드를 사용할 경우, 자동으로 등록됩니다. 플러그인의 `composer.json` 파일에 아래와 같이 컴포넌트 정보가 등록되어 있습니다.

```javascript
// plugins/my_plugin/composer.json
...
  "extra": {
        "xpressengine": {
            "title": "my plugin",
            "component": {
                "fieldType/my_plugin@my_field": {
                    "class": "GilDongHong\\XePlugin\\MyPlugin\\DynamicFields\\MyField\\MyFieldField",
                    "name": "my_field fieldType",
                    "description": "The fieldType supported by My_plugin plugin."
                }
            }
        }
    },

    ...
```

### 관리자 설정 페이지

다이나믹 필드를 생성할 때 사용자로 부터 입력값이 필요하다면 `getSettingsView()` 메소드를 구현 합니다. 제작자는 `$config`를 이용해 설정 입력 폼을 추가할 수 있습니다. 카테고리 다이나믹 필드\(`/app/FieldTypes/Category.php`\) 클래스를 참고하세요.

