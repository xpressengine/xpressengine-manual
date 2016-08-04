# 폼 출력

폼은 웹페이지에서 사용자에게 입력을 받기 위해 매우 빈번히 사용됩니다. 특히 테마나 위젯, 스킨과 같은 컴포넌트는 사이트 관리자에게 자신만의 설정 페이지를 제공합니다. XE는 UI오브젝트를 통해 폼 페이지를 구성하기 위한 손쉬운 방법을 제공합니다.

UI오브젝트는 자주 사용되는 UI 요소를 개발자들이 쉽게 출력할 수 있는 기능입니다. XE에서는 기본적인 폼 요소를 출력할 수 있는 폼 UI오브젝트를 제공하고 있습니다. 

폼 UI오브젝트를 웹페이지에 출력할 때에는 `uio`함수를 사용하면 됩니다.

```php
// in blade template
{{ uio($uiobject, $argument, $callback) }}
```

만약 텍스트 인풋을 출력하려면 아래와 같이 작성할 수 있습니다.

```php
{{ uio('formText', ['label'=>'전화번호', 'name'=>'phone', 'id'=>'inputPhone', 'class'=>'input-phone', 'placeholder'=>'전화번호를 입력하세요', 'description'=> '휴대폰 사용하여 입력하세요', 'value'=>'010-000-0000') }}
```
위 코드는 실제로 아래와 같이 변환되어 출력됩니다.

```html
<div class="form-group">
    <label for="" class="hidden"></label>
    <input type="text" class="form-control" id="" placeholder="">
    <p class="help-block"></p>
</div>

```



## 폼 UI오브젝트 출력

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











