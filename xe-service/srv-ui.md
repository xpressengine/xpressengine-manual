# UI오브젝트/폼빌더

## UI오브젝트/폼빌더

XE에서는 화면에 자주 출력되는 UI 요소들이 있습니다. 텍스트 입력 필드나 셀렉트 박스\(select\)와 같은 기본적인 폼 요소들이 있고, 테마 선택기나 메뉴 선택기, 또는 권한 설정 UI와 같이 XE에서만 사용되는 특별한 요소들도 있습니다. UI오브젝트는 이렇게 자주 사용되는 UI 요소를 개발자들이 쉽게 출력할 수 있는 방법을 제공합니다.

### 기본 사용법

`uio()` 함수를 사용하여 UI오브젝트를 출력할 수 있습니다.

만약 한줄 텍스트 폼 필드를 출력하려면 아래와 같이 작성할 수 있습니다.

```php
// in blade template file
{{ uio('formText', 
      ['label'=>'전화번호', 
       'name'=>'phone', 
       'id'=>'inputPhone', 
       'description'=> '휴대폰 사용하여 입력하세요', 
       'value'=>'010-000-0000'
]) }}
```

위 코드는 실제로 아래와 같이 변환되어 출력됩니다.

```markup
<div class="form-group">
  <label for="inputPhone">전화번호</label>
  <input type="text" class="form-control" 
         id="inputPhone"
         name="phone" value="010-000-0000">
   <p class="help-block">휴대폰 사용하여 입력하세요</p>
</div>
```

**uio 함수**

`uio` 함수의 원형입니다.

```php
uio($id, $args = [], $callback = null)
```

첫번째 파라미터 `$id`는 UI오브젝트의 아이디\(또는 별칭\)입니다.

두번째 파라미터 `$args`는 UI오브젝트를 출력하기 위해 전달할 정보를 담은 배열입니다. 각 UI오브젝트마다 전달하는 정보의 항목은 상이합니다.

세번째 파라미터 `$callback`를 사용하면 UI오브젝트가 출력하는 html을 커스터마이징할 수 있습니다.

### 기본 제공되는 UI오브젝트

XE는 아래 목록과 같이 UI오브젝트를 기본으로 제공하고 있습니다.

| 별칭 | 설명 |
| :--- | :--- |
| `formText` | `<input type="text">`를 출력 |
| `formPassword` | `<input type="password">`를 출력 |
| `formTextarea` | `<textarea>`를 출력 |
| `formSelect` | `<select>`를 출력, 사용자로부터 단일 선택을 받을 때 사용하십시오. |
| `formCheckbox` | `<input type="checkbox">`를 출력 사용자로부터 다중 선택을 받을 때 사용하십시오. |
| `formFile` | `<input type="file">` 출력, 파일을 업로드 받을 때 사용하십시오. 이 UI오브젝트는 [jQueryFileUpload](https://blueimp.github.io/jQuery-File-Upload/)를 사용합니다. |
| `formImage` | `formFile`과 같이 파일 업로드를 출력합니다. 하지만 이 UI오브젝트를 사용하면 사용자가 선택한 이미지 파일을 미리보기할 수 있습니다. |
| `formMenu` | XE에 등록된 메뉴 목록을 `<select>` 형식으로 출력합니다. |
| `formLangText (=langText)` | `formText`와 같이 한줄 텍스트 박스를 출력합니다. 하지만 이 UI오브젝트는 다국어를 입력받을 수 있습니다. |
| `formLangTextarea (=langTextArea)` | 여러줄의 다국어를 출력합니다. |
| `permission` | 권한설정 UI를 출력합니다. |
| `themeSelect` | 테마 선택 UI를 출력합니다. |
| `captcha` | 캡챠 입력 UI를 출력합니다. |

이 UI오브젝트들 이외에 여러 플러그인이 등록한 UI오브젝트를 사용할 수도 있습니다.

#### UI오브젝트 두번째 파라미터 사용 예제

**formText, formLangText**

```php
$args = [
    'label'=>'전화번호', 
    'name'=>'phone', 
    'id'=>'inputPhone', 
    'description'=> '휴대폰 사용하여 입력하세요', 
    'value'=>'010-000-0000'
]
```

**formPassword**

```php
$args = [
    'label'=>'비밀번호', 
    'name'=>'password', 
    'id'=>'password', 
    'description'=> '영문 + 숫자 조합 8자리 이상 입력하세요.', 
]
```

**formTextarea, formLangTextarea**

```php
$args = [
    'label'=>'Copyright', 
    'name'=>'copyright', 
    'id'=>'footer_copyright', 
    'description'=> '푸터하단 문구를 입력하세요.', 
]
```

**formSelect**

```php
$args = [
    'label'=>'Colorset', 
    'name'=>'layout_colorset', 
    'id'=>'layout_colorset', 
    'class'=>'layout_colorset',
    'description'=> '색상을 선택하세요.', 
    'options' => [
        'blue' => '파랑색',
        'red' => '붉은색',
        'green' => '초록색'
    ]
]
```

options는 3가지의 형태로 사용할 수 있습니다.

```php
'options' => [
    'blue' => '파랑색',
    'red' => '붉은색',
    'green' => '초록색'
]
```

```php
//key의 값이 value의 값과 같음
'options' => [
    'blue',
    'red',
    'green'
]
```

```php
//
'options' => [
    ['text' => '파랑색', value => 'blue', selected => true],
    ['text' => '붉은색', value => 'red'],
    ['text' => '초록색', value => 'green']
]
```

**formCheckbox**

```php
$args = [
    'label'=>'취미',
    'name'=>'hobby',
    'id'=>'hobby',
    'class'=>'hobby',
    'description'=> '취미를 선택하세요.',
    'options' => [
        'basketball' => '농구',
        'baseball' => '야구',
        'football' => '축구'
    ]
]
```

options는 checkboxes라는 프로퍼티명으로도 사용가능하며 formSelect와 같이 3가지의 형태로 사용할 수 있습니다.

```php
'options' => [
    'basketball' => '농구',
    'baseball' => '야구',
    'football' => '축구'
]
```

```php
'options' => [
    'basketball',
    'baseball',
    'football'
]
```

```php
'options' => [
    ['text' => '농구', value => 'basketball', checked => true],
    ['text' => '야구', value => 'baseball'],
    ['text' => '축구', value => 'football']
]
```

**formMenu**

```php
$args = [
    'label'=>'메인 메뉴',   
]
```

### 폼 빌더\(폼 UI오브젝트\)

폼은 웹페이지에서 사용자에게 입력을 받기 위해 매우 빈번히 사용됩니다. 특히 테마나 위젯, 스킨과 같은 컴포넌트는 사이트 관리자에게 폼으로 구성되어 있는 설정 페이지를 제공하므로 개발자가 설정 페이지를 작성해주어야 합니다. 이런 개발자의 수고를 줄이기 위해 UI오브젝트 중에는 폼 작성을 편하게 할 수 있도록 도와주는 UI오브젝트들이 있습니다.

#### 폼 필드 UI오브젝트

폼은 `text`, `textarea`, `checkbox`와 같은 다양한 '폼 필드'들로 구성됩니다. UI오브젝트 중 별칭\(alias\)이 `form*` 형식인 UI오브젝트들이 바로 '폼 필드 UI오브젝트'입니다. 폼 필드 UI오브젝트를 사용하면 손쉽게 폼 필드를 출력할 수 있습니다.

#### 폼 UI오브젝트

또, UI오브젝트 중에 별칭\(alias\)이 `form`인 UI오브젝트가 있는데, 이 UI오브젝트는 조금 특별합니다. 이 UI오브젝트는 하나의 완성된 폼을 출력합니다. 물론, 폼에 대한 기본정보\(action, method 등\)와 폼을 구성하는 필드의 목록은 파라미터로 입력받습니다.

폼 UI오브젝트도 다른 UI오브젝트와 같이 `uio` 함수로 사용할 수 있습니다.

```php
{{ uio('form', $formData); }}
```

두번째 파라미터인 `$formData`는 아래와 같은 형식을 가집니다.

```php
$formData = [
  'fields' => [
    'title' => [
        '_type' => 'text',
        '_section' => '섹션1',
        'label' => '제목',
        'placeholder' => '제목을 입력하세요.',
    ],
    'content' => [
        '_type' => 'textarea',
        '_section' => '섹션1',
        'label' => '내용',
        'placeholder' => '내용을 입력하세요.',
    ],
  ],
  'value' => [
    'title' => 'foo..',
    'content' => 'bar..'
  ],
  'action' => '/',
  'method' => 'POST',
];
```

위 코드의 출력은 아래와 같습니다.

```markup
<form method="POST" enctype="multipart/form-data" action="/">
    <div class="panel form-section-섹션1">
        <div class="panel-heading">
            <div class="pull-left"><h3>섹션1</h3></div>
        </div>
        <div class="panel-body">
            <div class="row">
                <div class="col-md-6 form-col-title">
                    <div class="form-group">
                        <label for="">제목</label>
                        <input type="text" class="form-control" id="" placeholder="제목을 입력하세요." name="title" value="foo..">
                        <p class="help-block"></p>
                    </div>
                </div>
                <div class="col-md-6 form-col-content">
                    <div class="form-group">
                        <label for="">내용</label>
                        <textarea class="form-control" rows="3" placeholder="내용을 입력하세요." name="content">bar..</textarea>
                    </div>
                </div>
            </div>
        </div>
    </div>
</form>
```

두번째 파라미터로 전달한 배열은 다음 항목을 제공해야 합니다.

`fields`

폼이 포함하고 있는 필드들의 목록입니다. 이 배열의 아이템 하나는 폼 필드 하나의 정보를 나타냅니다. 아이템의 키는 폼필드의 `name` 애트리뷰트로 사용됩니다.\(위의 예에서는 `title`, `content`에 해당\)

또 아이템 하나의 정보에서 `_type`, `_section`은 각각 폼필드의 타입과 폼필드가 출력될 그룹\(섹션\)명을 지정합니다. 그 이외의 정보들은 모두 지정된 폼필드를 설정할 때 전달됩니다.

`_type`에 지정되는 값은 폼 필드 UI오브젝트의 별칭에서 접미사인 `form`을 제외한 나머지 부분의 별칭만 사용됩니다. 위의 예제에서 첫번째 필드인 `title`의 경우, 타입\(\_type\)이 `text`로 지정돼 있습니다. `text`타입에 해당하는 폼 필드 UI오브젝트는 `formText` UI오브젝트입니다. `label` 및 `placeholder` 정보는 `formText` UI오브젝트에게 그대로 전달됩니다.

`value`

폼이 포함하고 있는 필드들이 가지는 값\(value\)의 목록입니다

