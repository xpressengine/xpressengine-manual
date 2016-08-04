# 폼 출력

UI오브젝트는 자주 사용되는 UI 요소를 개발자들이 쉽게 출력할 수 있는 기능입니다. XE에서는 기본적인 폼 필드들을 출력할 수 있는 UI오브젝트들을 제공하고 있습니다. 폼 필드를 웹페이지에 출력할 때에는 `uio`함수를 사용하면 됩니다.

```php
uio($id, $args = [], $callback = null)
```

첫번째 파라미터 `$id`는 폼필드의 아이디(또는 별칭)입니다. 다음 섹션에서 폼 필드의 종류에 대해 자세히 설명합니다.

두번째 파라미터 `$args`는 폼 필드를 출력하기 위해 전달할 정보를 담은 배열입니다. 각 폼 UI오브젝트마다 전달하는 정보의 형식이 상이합니다.

세번째 파라미터 `$callback`를 사용하면 폼 UI오브젝트가 출력하는 html을 커스터마이징할 수 있습니다.

만약 한줄 텍스트를 입력받으려면 아래와 같이 작성할 수 있습니다.

```php
// in blade template file
{{ uio('formText', ['label'=>'전화번호', 'name'=>'phone', 'id'=>'inputPhone', 'class'=>'input-phone', 'placeholder'=>'전화번호를 입력하세요', 'description'=> '휴대폰 사용하여 입력하세요', 'value'=>'010-000-0000']) }}
```
위 코드는 실제로 아래와 같이 변환되어 출력됩니다.

```html
<div class="form-group">
  <label for="inputPhone">전화번호</label>
  <input type="text" class="form-control input-phone" 
         id="inputPhone" placeholder="전화번호를 입력하세요" 
         name="phone" value="010-000-0000">
   <p class="help-block">휴대폰 사용하여 입력하세요</p>
</div>
```

폼 UI오브젝트는 모두 `<div class="form-group"></div>` 태그로 감싸서 출력됩니다. 이 스타일은 [bootstrap](http://getbootstrap.com/)을 따르고 있는데, 폼 UI오브젝트는 사이트 관리 페이지에 최적화되어 있기 때문입니다. 만약 사이트 관리 페이지가 아닌 페이지에서 폼 UI오브젝트를 사용할 경우, bootstrap의 스타일시트 및 스크립트 파일을 직접 로드하고 사용하는 것을 권장합니다.


## 필드 종류

UI오브젝트는 자주 사용되는 UI 요소를 개발자들이 쉽게 출력할 수 있는 기능입니다. XE에서는 기본적인 폼 요소를 출력할 수 있는 폼 UI오브젝트를 제공하고 있습니다. 

- `formText` - `<input type="text">`를 출력

- `formPassword` - `<input type="password">`를 출력

- `formTextarea` - `<textarea>`를 출력

- `formSelect` - `<select>`를 출력, 사용자로부터 단일 선택을 받을 때 사용하십시오.

- `formCheckbox` - `<input type="checkbox">`를 출력 사용자로부터 다중 선택을 받을 때 사용하십시오.

- `formFile` - `<input type="file">` 출력, 파일을 업로드 받을 때 사용하십시오. 이 UI오브젝트는 [jQueryFileUpload](https://blueimp.github.io/jQuery-File-Upload/)를 사용합니다.

- `formImage` - `formFile`과 같이 파일 업로드를 출력합니다. 하지만 이 UI오브젝트를 사용하면 사용자가 선택한 이미지 파일을 미리보기할 수 있습니다.

- `formMenu` - XE에 등록된 메뉴 목록을 `<select>` 형식으로 출력합니다.

- `formLangText` - `formText`와 같이 한줄 텍스트 박스를 출력합니다. 하지만 이 UI오브젝트는 다국어를 입력받을 수 있습니다.

- `formLangTextarea` - 여러줄의 다국어를 출력합니다.


## 폼 빌더

폼 빌더를 사용하면 더욱 손쉽게 하나의 폼을 완성할 수 있습니다.










