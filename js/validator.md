# Validator

## Validator

back-end에서 정의한 validation 룰을 front-end에서 사용할 수 있도록 다음과 같이 해야합니다. 이와 같이 지정한 룰은 front-end Validator에서 활용됩니다.

```php
XeFrontend::rule('myRuleName', [
    'email' => 'required|email',
    'password' => 'required'
]);
```

### 폼 검증 룰의 정의

#### data-rule-alert-type \(form\)

유효성 체크를 통과하지 못할 경우 보여질 메시지형태를 정의합니다. 지원하는 메시지 출력 형태는 아래와 같습니다.

* toast
  * XE.toast 팝업의 형태로 메시지가 노출됩니다.
* form
  * 해당 필드 요소하단에 메시지가 노출됩니다.

```markup
<form data-rule-alert-type="toast">
...
</form>
```

#### data-valid \(input element\)

입력 값을 검사할 rule을 지정합니다.

```markup
<input type="text" name="email" data-valid="required|email" />
```

지원하는 룰은 아래 목록에서 확인할 수 있습니다.

* required
  * 필수 요소일 경우 사용합니다.
* checked:min-max
  * checkbox, raido 타입에 사용됩니다.
  * **첫번째 엘리먼트**에만 해당 요소가 정의되어야 합니다.
  * min, max는 number 타입으로 지정합니다. radio button에서는 필수 체크시 **checked:1**로 표기 합니다.
* alpha
  * 알파벳으로만 사용되는 필드 값을 체크합니다.
* alphanum 또는 alpha\_num
  * 알파벳 또는 숫자 값을 체크합니다.
* min
  * 최소 입력 글자를 체크합니다
* max
  * 최대 입력 글자를 체크합니다
* email
  * 이메일 형식인지 체크합니다.
* url
  * url형식으로 입력되었는지 체크합니다.
* numeric
  * 숫자값만 입력되었는지 체크합니다.
* between:min,max
  * 필드 값이 최소, 최대에 만족하는지 체크합니다.
* accepted
  * 필드의 값이 yes, on, 1, 또는 _true_이어야 합니다.
* alpha\_dash
  * 필드의 값이 \(숫자나 기호가 아닌\) 알파벳\[자음과 모음\] 문자 및 숫자와 dash\(-\), underscore\(\_\)로 이루어져야 합니다.
* array
  * 필드의 값이 배열형태이어야 합니다.
* boolean
  * 필드의 값이 반드시 true, false, 1, 0, "1", "0" 이어야 합니다.
* date
  * 필드의 값이 strtotime PHP 함수에서 인식할 수 있는 올바른 날짜여야 합니다.
* date\_format:format
  * 필드의 값이 반드시 주어진 format과 일지해야 합니다.
* digits:value
  * 필드의 값이 반드시 숫자여야 하고, 길이가 value이어야 합니다
* digits\_between:min,max
  * 필드의 값이 주어진 min과 max사이의 길이를 가져야 합니다.
* filled
  * 필드가 존재하는 경우 값이 비어있으면 안됩니다.
* integer
  * 필드의 값이 정수여야 합니다.
* ip
  * 필드의 값이 IP 주소여야 합니다.
* mimes:foo,bar...
  * 파일의 MIME 타입이 주어진 확장자 리스트 중에 하나와 일치해야 합니다.
* regex:pattern
  * 필드의 값이 주어진 정규식 표현과 일치해야 합니다.
* json
  * 필드의 값이 유효한 JSON 문자열이어야 합니다.
* string
  * 필드의 값이 반드시 문자열이어야 합니다.

#### data-valid-name \(input element\)

validation 실패 시 'resources/lang/ko\|en/validation.php' 파일에 정의된 다국어를 사용하여 `:attribute` 기본값으로 엘리먼트의 name속성을 사용하고 있습니다. validation 실패 메시지에서 치환되는 `:attribute`를 input element의 name 대신 사용자 정의할 수 있습니다.

```markup
<!-- XE form sample -->
<form id='form' action="/users" method="POST" data-rule-alert-type="toast">
  <input type="text" name="name" data-valid="required" data-valid-name="이름(비실명)" />
  <input type="password" name="current_password" data-valid="required" data-valid-name="현재 비밀번호" />
  <input type="password" name="password" data-valid="required" data-valid-name="새 비밀번호" />
  <input type="password" name="password_confirmation" data-valid="required" data-valid-name="새 비밀번호(확인)" />
</form>
```

```javascript
XE.formValidate($('#form'));
// => 'password_confirmation은(는) 필수입력 항목입니다.'
// 대신 '새 비밀번호(확인)은(는) 필수입력 항목입니다.' 메시지 출력
```

#### data-valid-message \(input element\)

'새 비밀번호\(확인\)은\(는\) 필수입력 항목입니다.'와 같은 정돈하지 않은 듯한 메시지를 사용자 정의하여 변경할 수 있습니다.

```markup
<!-- XE form sample -->
<form>
  <input type="password" name="password_confirmation" data-valid="required" data-valid-message="새 비밀번호는 동일하게 입력해주세요" />
</form>
// => '새 비밀번호는 동일하게 입력해주세요'
```

### XE.Validator.setRules\(ruleName, rules\)

rule을 정의하고 등록합니다. 필요한 rule의 다국어 메시지가 로드되지 않은 상태일경우 ajax로 필요 메시지를 요청하는 로직이 포함되어 있습니다. 룰세팅 이후에 `XE.validator.init` 메소드를 호출합니다.

* ruleName \(string\)
  * 정의 하는 rule의 명칭입니다. form요소의 data-rule과 동일하게 작성되어야 하며 form마다 다르게 작성될 수 있습니다.
* rules \(object\)
  * 유효성 체크를 위한 rule파라미터 입니다. 폼 필드의 name속성과 룰들이 등록됩니다.

    `ex){"id":"required|between:10,20","file":"mimes:jpg,gif,png|between:0,2048","name":"required"}`
* XE.validator.init\(ruleName\)
  * \[data-rule=ruleName\]으로 정의된 폼 요소에 submit이벤트시 해당 폼의 유효성 체크를 할 수 있도록 이벤트를 바인딩 합니다.
* ruleName \(string\)

```javascript
XE.Validator.setRules('formCheckRule', {
 "id":"required|between:10,20",
 "file":"mimes:jpg,gif,png|between:0,2048",
 "name":"required"
});
```

### XE.Validator.getRuleName\($form\)

해당 폼 요소의 ruleName을 리턴합니다.

* $form \(jquery object\)

### XE.Validator.check\($form\)

해당 폼에 정의된 rule을 체크합니다.

* $form \(jquery object\)

### XE.Validator.validate\($form, name, rule\)

$form 폼 요소의 name필드가 rule의 형태로 유효한지 체크합니다.

* $form \(jquery object\)
  * form element
* name \(string\)
  * 필드명
* rule \(string\)
  * rule

### XE.Validator.put\(name, callback\)

유효성을 체크할 validator를 추가합니다.

* name \(string\)
  * 추가할 validator 명칭 `ex)requiredAlpha`
* callback \(function\)
  * validation이 실행될 때 호출할 callback. validation이 실패할 경우 false를 리턴하는 로직이 포함되어야 합니다.

### XE.Validator.error\($element, message, replaceStrMap\)

유효성체크 실패 시 호출하는 함수로 오류 메시지를 노출합니다.

* $element \(jquery object\)
  * 오류를 노출할 element
* message \(string\)
  * 오류 메시지
* replaceStrMap \(object\)
  * 오류 메시지중 치환된 문자열 object

