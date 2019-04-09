# 유효성검사

## 유효성검사\(validation\)

### 소개

XE는 유입되는 데이터의 유효성을 검사하기 위한 다양한 방법을 제공합니다.

### 유효성검사 살펴보기

유효성 검사 기능에 대해 알아보기 위해서, form을 확인한 뒤 사용자에게 에러 메세지를 보여주는 예제를 살펴보도록 하겠습니다.

#### 라우트 정의하기

우선 다음의 라우트들이 정의되어 있다고 가정해 보겠습니다:

```php
// Display a form to create a blog post...
Route::get('post/create', 'PostController@create');

// Store a new blog post...
Route::post('post', 'PostController@store');
```

`GET` 라우트는 사용자가 새로운 블로그 포스트를 생성하기 위한 form을 나타낼 것이고, `POST` 라우트는 데이터베이스에 새로운 블로그 포스트를 저장할 것입니다.

#### 컨트롤러 생성하기

다음으로, 이 라우트들을 다루는 간단한 컨트롤러를 살펴보겠습니다. 지금은 `store` 메소드를 비어있는 채로 둘 것입니다:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class PostController extends Controller
{
    /**
     * Show the form the create a new blog post.
     *
     * @return Response
     */
    public function create()
    {
        return view('post.create');
    }

    /**
     * Store a new blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        // Validate and store the blog post...
    }
}
```

#### 유효성검사 로직 작성하기

이제 새로운 블로그 포스트에 대해 유효성을 검사하는 로직을 `store` 메소드에 채워넣을 준비가 되었습니다. 베이스 컨트롤러\(`App\Http\Controllers\Controller`\) 클래스를 살펴보면 클래스가 `ValidatesRequests` 트레이트-trait을 사용한다는 것을 알 수 있습니다. 이 트레이트\(trait\)는 모든 컨트롤러에서 편리하게 사용할 수 있는 `validate` 메소드를 제공합니다.

`validate` 메소드는 HTTP 요청의 유입과 유효성 검사 룰의 집합을 전달 받습니다. 유효성 검사 룰들을 통과하게되면 코드는 계속해서 정상적으로 실행될 것입니다. 하지만 유효성 검사를 통과하지 못할 경우, 예외\(exception\)가 던져지고 적절한 오류 응답이 사용자에게 자동으로 보내질 것입니다. 전통적인 HTTP 요청의 경우, 리다이렉트 응답이 생성될 것이며 AJAX 요청에는 JSON 응답이 보내질 것입니다.

`validate` 메소드에 대해 더 잘 이해하기 위해, 다시 `store` 메소드로 돌아가 보겠습니다:

```php
/**
 * Store a new blog post.
 *
 * @param  Request  $request
 * @return Response
 */
public function store(Request $request)
{
    $this->validate($request, [
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
    ]);

    // The blog post is valid, store in database...
}
```

위에서 볼 수 있듯이, 간단하게 유입되는 HTTP 요청과 유효성 검사 룰들을 `validate` 메소드로 전달하면 됩니다. 이 때에도 유효 확인이 실패하면 적절한 응답이 생성될 것입니다. 유효성 검사를 통과하면 컨트롤러는 계속해서 정상적으로 수행합니다.

**중첩된 속성에 대한 유의사항**

HTTP 요청이 "중첩된" 파라미터를 가지고 있다면 ".\(점\)" 문법을 사용하여 유효성 검사 규칙을 지정할 수 있습니다:

```php
$this->validate($request, [
    'title' => 'required|unique:posts|max:255',
    'author.name' => 'required',
    'author.description' => 'required',
]);
```

#### 유효성 검사 에러 표시하기

그럼 입력되는 요청\(request\)의 파라미터들이 유효성 검사를 통과하지 못하는 경우에는 어떻게 될까요? 이 경우 앞서 언급한대로, 자동으로 사용자를 이전의 위치로 리다이렉트합니다. 또한 모든 유효성 확인 에러는 자동으로 세션에 임시 저장될 것입니다.

`GET` 라우트에서도 에러 메세지를 뷰와 명시적으로 출력하지 않아도 됩니다. 왜냐하면, 기본적으로 세션에 저장된 유효성 확인 에러를 출력합니다.

이 예제에서, 유효성 검사를 통과하지 못할 경우 사용자는 컨트롤러의 `create` 메소드로 리다이렉트 될것이고, XE는 세션에 저장된 유효성 확인 에러를 확인한 후, 토스트 팝업\(팝업레이어 형식\)으로 오류 메시지를 출력합니다.

#### AJAX 요청과 유효성 검사

이 예제에서는, 어플리케이션에 전통적인 form을 이용하여 데이터를 보냈습니다. 하지만 많은 어플리케이션이 AJAX 요청을 사용합니다. AJAX 요청 중에서 `validate` 메소드를 사용한다면 라라벨은 리다이렉트 응답을 생성하지 않습니다. 대신 라라벨은 유효성 검사의 모든 실패 에러들을 포함하는 JSON 응답을 생성할 것입니다. 이 JSON 응답은 422 HTTP 상태 코드와 함께 보내질 것입니다.

### 다른 유효성 검사 방법

#### 수동으로 Validator 생성하기

컨트롤러에서 제공하는 `validate` 메소드를 사용하고 싶지 않다면 `Validator` 파사드를 사용하여 validator 인스턴스를 수동으로 생성할 수 있습니다. 파사드에 `make` 메소드를 사용하면 새로운 validator 인스턴스가 생성됩니다:

```php
<?php

namespace App\Http\Controllers;

use Validator;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class PostController extends Controller
{
    /**
     * Store a new blog post.
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
        ]);

        if ($validator->fails()) {
            return redirect('post/create')
                        ->withErrors($validator)
                        ->withInput();
        }

        // Store the blog post...
    }
}
```

`make` 메소드로 전달되는 첫번째 인자는 유효성 검사를 받을 데이터입니다. 두번째 인자는 데이터에 적용되어야 하는 유효성 검사 규칙들입니다.

요청\(request\) 정보가 유효성 검사를 통과하지 못한 것을 확인였다면 `withErrors` 메소드로 세션에 에러 메세지를 임시저장\(flash\) 할 수 있습니다. 이 메소드를 사용하면 리다이렉트 후에 `$errors` 변수가 자동으로 뷰에서 공유되어 손쉽게 사용자에게 보여질 수 있습니다. `withErrors` 메소드는 validator, `MessageBag`, 혹은 PHP `array`를 전달 받습니다.

**이름이 지정된 Error Bags**

한 페이지 안에서 여러개의 form을 가지고 있다면 에러들의 `MessageBag`에 이름을 붙여 지정한 form에 맞는 에러 메세지를 조회할 수 있도록 할 수 있습니다. 단순히 `withErrors`에 이름을 두번째 인자로 전달하면 됩니다:

```php
return redirect('register')
            ->withErrors($validator, 'login');
```

그러면 `$errors` 변수에서 지정된 `MessageBag` 인스턴스에 접근할 수 있습니다.

```php
{{ $errors->login->first('email') }}
```

### 에러 메시지 사용하기

`Validator` 인스턴스의 `messages` 메소드를 호출하면, 에러 메시지를 편하게 사용할 수 있는 다양한 메소드를 가진 `MessageBag` 인스턴스를 받을 수 있습니다.

**하나의 필드에 대한 첫번째 에러 메시지 조회하기**

특정 필드에 대한 첫번째 에러 메세지를 조회하려면 `first` 메소드를 사용하면 됩니다:

```php
$messages = $validator->errors();

echo $messages->first('email');
```

**하나의 필드에 대한 모든 에러 메세지 조회하기**

간단하게 하나의 필드에 대한 모든 에러 메세지를 조회하고 싶다면 `get` 메소드를 사용하면 됩니다:

```php
foreach ($messages->get('email') as $message) {
    //
}
```

**모든 필드에 대한 모든 에러 메세지 조회하기**

모든 필드에 대한 모든 에러 메세지를 조회하기 위해서는 `all` 메소들 사용하면 됩니다:

```php
foreach ($messages->all() as $message) {
    //
}
```

**하나의 필드에 대하여 에러 메시지가 존재하는지 검사하기**

```php
if ($messages->has('email')) {
    //
}
```

**특정 형식으로 에러 메시지 획득하기**

```php
echo $messages->first('email', '<p>:message</p>');
```

**특정 형식으로 모든 에러 메시지 획득하기**

```php
foreach ($messages->all('<li>:message</li>') as $message) {
    //
}
```

#### 사용자 지정\(커스텀\) 에러 메세지

필요하다면 기본적인 에러 메세지 대신에 커스텀 에러 메세지를 유효성 검사에 사용할 수 있습니다. 커스텀 메세지를 지정하는 데에는 여러가지 방법이 있습니다. 먼저 `Validator::make` 메소드에 커스텀 메세지를 세번째 인자로 전달할 수 있습니다:

```text
$messages = [
    'required' => 'The :attribute field is required.',
];

$validator = Validator::make($input, $rules, $messages);
```

다음의 예에서 `:attribute` 플레이스 홀더는 유효성 검사를 받는 필드의 실제 이름으로 대체됩니다. 유효성 검사 메세지에서 다른 플레이스 홀더들 또한 활용할 수 있습니다. 예를 들어:

```text
$messages = [
    'same'    => 'The :attribute and :other must match.',
    'size'    => 'The :attribute must be exactly :size.',
    'between' => 'The :attribute must be between :min - :max.',
    'in'      => 'The :attribute must be one of the following types: :values',
];
```

**주어진 속성에 대해 커스텀 메세지 지정하기**

종종 하나의 특정 필드에 대해서만 커스텀 오류 메세지를 지정해야 하는 경우가 있습니다. 이것은 ".\(점\)" 표기법을 통해서 할 수 있습니다. 속성의 이름을 먼저 지정하고, 규칙을 명시하면됩니다:

```text
$messages = [
    'email.required' => 'We need to know your e-mail address!',
];
```

**언어 파일에 커스텀 메세지 지정하기**

많은 경우에서, `Validator`에 직접 메세지를 전달하는 대신, 언어 파일에 속성에 따른 커스텀 메세지를 지정하기 원할 수 있습니다. 이렇게 하기 위해서는 `resources/lang/xx/validation.php` 언어 파일의 `custom` 배열에 메제지를 추가하면 됩니다.

```text
'custom' => [
    'email' => [
        'required' => 'We need to know your e-mail address!',
    ],
],
```

### 사용가능한 유효성 검사 규칙들

다음은 모든 유효성 검사 규칙과 그들의 함수 목록입니다.

* [Accepted](service-validataion.md#rule-accepted)
* [Active URL](service-validataion.md#rule-active-url)
* [After \(Date\)](service-validataion.md#rule-after)
* [Alpha](service-validataion.md#rule-alpha)
* [Alpha Dash](service-validataion.md#rule-alpha-dash)
* [Alpha Numeric](service-validataion.md#rule-alpha-num)
* [Array](service-validataion.md#rule-array)
* [Before \(Date\)](service-validataion.md#rule-before)
* [Between](service-validataion.md#rule-between)
* [Boolean](service-validataion.md#rule-boolean)
* [Confirmed](service-validataion.md#rule-confirmed)
* [Date](service-validataion.md#rule-date)
* [Date Format](service-validataion.md#rule-date-format)
* [Different](service-validataion.md#rule-different)
* [Digits](service-validataion.md#rule-digits)
* [Digits Between](service-validataion.md#rule-digits-between)
* [E-Mail](service-validataion.md#rule-email)
* [Exists \(Database\)](service-validataion.md#rule-exists)
* [Image \(File\)](service-validataion.md#rule-image)
* [In](service-validataion.md#rule-in)
* [Integer](service-validataion.md#rule-integer)
* [IP Address](service-validataion.md#rule-ip)
* [JSON](service-validataion.md#rule-json)
* [Max](service-validataion.md#rule-max)
* [MIME Types \(File\)](service-validataion.md#rule-mimes)
* [Min](service-validataion.md#rule-min)
* [Not In](service-validataion.md#rule-not-in)
* [Numeric](service-validataion.md#rule-numeric)
* [Regular Expression](service-validataion.md#rule-regex)
* [Required](service-validataion.md#rule-required)
* [Required If](service-validataion.md#rule-required-if)
* [Required Unless](service-validataion.md#rule-required-unless)
* [Required With](service-validataion.md#rule-required-with)
* [Required With All](service-validataion.md#rule-required-with-all)
* [Required Without](service-validataion.md#rule-required-without)
* [Required Without All](service-validataion.md#rule-required-without-all)
* [Same](service-validataion.md#rule-same)
* [Size](service-validataion.md#rule-size)
* [String](service-validataion.md#rule-string)
* [Timezone](service-validataion.md#rule-timezone)
* [Unique \(Database\)](service-validataion.md#rule-unique)
* [URL](service-validataion.md#rule-url)

**accepted**

필드의 값이 _yes_, _on_, _1_, 또는 _true_이어야 합니다. 이 것은 "이용약관" 동의와 같은 필드의 검사에 유용합니다.

**active\_url**

필드의 값이 PHP 함수 `checkdnsrr`에 기반하여 올바른 URL이어야 합니다.

**after:date**

필드의 값이 주어진 날짜 이후여야 합니다. 이때 날짜는 `strtotime` PHP 함수를 통해 생성된 값입니다.

```text
'start_date' => 'required|date|after:tomorrow'
```

`strtotime`에 의해 계산될 날짜 문자열을 전달하는 대신 날짜와 비교할 다른 필드를 명시할 수 있습니다:

```text
'finish_date' => 'required|date|after:start_date'
```

**alpha**

필드의 값이 완벽하게 \(숫자나 기호가 아닌\) 알파벳\[자음과 모음\] 문자로 이루어져야 합니다.

\(역자주: 영문 알파벳만을 의미하지 않고, 숫자나 기호가 아닌경우에 해당하여, 한글도 허용합니다.\)

**alpha\_dash**

필드의 값이 \(숫자나 기호가 아닌\) 알파벳\[자음과 모음\] 문자 및 숫자와 dash\(-\), underscore\(\_\)로 이루어져야 합니다.

**alpha\_num**

필드의 값이 완벽하게 \(숫자나 기호가 아닌\) 알파벳\[자음과 모음\] 문자 및 숫자로 이루어져야 합니다.

**array**

필드의 값이 반드시 PHP 배열 형태이어야 합니다.

**before:date**

필드의 값이 반드시 주어진 날짜보다 앞서야 합니다. 날짜는 `strtotime` PHP 함수를 통해 비교됩니다.

**between:min,max**

필드의 값이, 주어진 _min_ 과 _max_의 사이의 값이어야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](service-validataion.md#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

**boolean**

필드의 값이 반드시 boolean으로 캐스팅될 수 있어야 합니다. 허용되는 값은 `true`, `false`, `1`, `0`, `"1"`, `"0"` 입니다.

**confirmed**

필드의 값이 `foo_confirmation`의 매칭되는 필드를 가져야 합니다. 예를 들어 만약 필드가 `password`라면, `password_confirmation`라는 필드가 입력값 중에 있어야 합니다.

**date**

필드의 값이 `strtotime` PHP 함수에서 인식할 수 있는 올바른 날짜여야 합니다.

**dateformat:\_format**

필드의 값이 반드시 주어진 _format_과 일지해야 합니다. 주어진 포맷은 `date_parse_from_format` PHP 함수에 의해서 연산될 것입니다. 필드의 유효성을 검사할 때에는 `date`와 `date_format` 중 **하나만** 사용해야 합니다.

**different:field**

필드의 값이 주어진 _field_의 값과 달라야 합니다.

**digits:value**

필드의 값이 반드시 _숫자_여야 하고, 길이가 _value_이어야 합니다.

**digitsbetween:\_min,max**

필드의 값이 주어진 _min_과 _max_ 사이의 길이를 가져야 합니다.

**email**

필드의 값이 이메일 주소 형식이어야 합니다.

**exists:table,column**

필드의 값이 주어진 데이터베이스 테이블에 존재하는 값이어야 합니다.

**exists 룰의 기본 사용법**

```text
'state' => 'exists:states'
```

**특정 컬럼명 지정하기**

```text
'state' => 'exists:states,abbreviation'
```

쿼리문의 "where" 구문에 추가될 더 많은 조건을 지정할 수도 있습니다:

```text
'email' => 'exists:staff,email,account_id,1'
```

"where" 구분에 `NULL` 혹은 `NOT_NULL`을 전달할 수도 있습니다:

```text
'email' => 'exists:staff,email,deleted_at,NULL'

'email' => 'exists:staff,email,deleted_at,NOT_NULL'
```

**image**

이미지 파일\(jpeg, png, bmp, gif, svg\)이어야 합니다.

**in:foo,bar,...**

필드의 값이 주어진 목록에 포함돼 있어야 합니다.

**integer**

필드의 값이 정수여야 합니다.

**ip**

필드의 값이 IP 주소여야 합니다.

**json**

필드의 값이 유효한 JSON 문자열이어야 합니다.

**max:value**

필드의 값이 반드시 _value_보다 작거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](service-validataion.md#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

**mimes:foo,bar,...**

파일의 MIME 타입이 주어진 확장자 리스트 중에 하나와 일치해야 합니다.

**MIME 룰의 기본 사용법**

```text
'photo' => 'mimes:jpeg,bmp,png'
```

여러분은 확장자를 지정하기만 하면 되지만, 이 경우 파일의 컨텐트를 읽고 MIME 타입을 추정함으로써 이 파일의 MIME의 유효성을 검사합니다.

MIME 타입과 그에 상응하는 확장의 전체 목록은 다음의 위치에서 확인하실 수 있습니다: [http://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types](http://svn.apache.org/repos/asf/httpd/httpd/trunk/docs/conf/mime.types)

**min:value**

필드의 값이 반드시 _value_ 보다 크거나 같아야 합니다. 문자열, 숫자, 그리고 파일이 [`size`](service-validataion.md#rule-size) 룰에 의해 같은 방식으로 평가될 수 있습니다.

**notin:\_foo,bar,...**

필드의 값이 주어진 목록에 존재하지 않아야 합니다.

**numeric**

필드의 값이 숫자여야 합니다.

**regex:pattern**

필드의 값이 주어진 정규식 표현과 일치해야 합니다.

**참고:** `regex` 패턴을 사용할 때, 특히 정규 표현식에 파이프 문자열이 있다면, 파이프 구분자를 사용하는 대신 배열 형식을 사용하여 룰을 지정할 필요가 있습니다.

**required**

입력 값 중에 해당 필드가 존재해야 하며 비어 있어서는 안됩니다. 필드는 다음의 조건 중 하나를 충족하면 "빈\(empty\)" 것으로 간주됩니다:

* 값이 `null`인 경우.
* 값이 비어있는 문자열인 경우.
* 값이 비어있는 배열이거나, 비어있는 `Countable` 객체인경우 
* 값이 경로없이 업로드된 파일인 경우

**requiredif:\_anotherfield,value,...**

만약 _filed_의 값이 _value_중의 하나와 일치한다면, 해당 필드가 반드시 존재해야 합니다.

**requiredunless:\_anotherfield,value,...**

_anotherfield_가 어떤 _value_와 동일하지 않은 이상 필드가 존재해야 합니다.

**requiredwith:\_foo,bar,...**

다른 지정된 필드중 하나라도 존재한다면, 해당 필드가 반드시 존재해야 합니다.

**requiredwith\_all:\_foo,bar,...**

다른 지정된 필드가 모두 존재한다면, 해당 필드가 반드시 존재해야 합니다.

**requiredwithout:\_foo,bar,...**

다른 지정된 필드중 하나라도 존재하지 않으면, 해당 필드가 반드시 존재해야 합니다.

**requiredwithout\_all:\_foo,bar,...**

다른 지정된 필드가 모두 존재하지 않으면, 해당 필드가 존재해야 합니다.

**same:field**

필드의 값이 주어진 _field_의 값과 일치해야 합니다.

**size:value**

필드의 값이 주어진 _value_와 일치하는 크기를 가져야 합니다. 문자열 데이터에서는 문자의 개수가 _value_와 일치해야 합니다. 숫자형식의 데이터에서는 주어진 정수값이 _value_와 일치해야 합니다. 파일에서는 킬로바이트 형식의 파일 사이즈가 _size_와 일치해야 합니다.

**string**

필드의 값이 반드시 문자열이어야 합니다.

**timezone**

필드의 값이 `timezone_identifiers_list` PHP 함수에서 인식 가능한 유효한 timezone 식별자여야 합니다.

**unique:table,column,except,idColumn**

필드의 값이 주어진 데이터베이스 테이블에서 고유한 값이어야 합니다. 만약 `column`이 지정돼 있지 않다면 필드의 이름이 사용됩니다.

**특정 컬럼명 지정하기:**

```text
'email' => 'unique:users,email_address'
```

**특정 데이터베이스 커넥션**

때때로, 여러분은 Validator에 의해서 생성되는 데이터베이스 쿼리에 사용자가 지정한 커넥션을 필요로 할지도 모릅니다. 위에서의 검증 규칙 `unique:users` 에서는 데이터베이스를 쿼리하기 위해 기본 데이터 베이스 커넥션이 사용됩니다. 이를 재지정하려면 테이블 이름 후에 "." 표기법으로 커넥션을 지정하십시오:

```text
'email' => 'unique:connection.users,email_address'
```

**주어진 ID에 대해서 유니크 규칙을 무시하도록 강제하기:**

때때로 유니크 검사를 할 때 특정 ID를 무시하고자 할 수 있습니다. 예를 들어 사용자 이름, 이메일 주소 그리고 위치를 포함하는 "프로필 업데이트" 화면이 있습니다. 물론 이메일 주소가 고유하다는 것을 확인하고 싶을 것입니다. 하지만 사용자가 이름 필드만 바꾸고 이베일 필드를 바꾸지 않는다면 사용자가 이미 이메일 주소의 주인이기 때문에 유효 검사 오류가 던져지지 않아야 합니다. 유효 검사 오류는 사용자가 다른 사용자가 이미 사용하고 있는 이메일 주소를 제공할 때에만 던져져야 합니다. 유니크 규칙에 사용자 ID를 무시하라고 강제하기 위해서는 세번째 파라미터로 ID를 전달해야 합니다:

```text
'email' => 'unique:users,email_address,'.$user->id
```

테이블이 `id`가 아닌 primary 키 컬럼 이름을 사용한다면 그 이름을 네번째 파라미터로 지정하면 됩니다:

```text
'email' => 'unique:users,email_address,'.$user->id.',user_id'
```

**추가적인 Where 구문 추가하기:**

더 추가되는 조건은 쿼리에 추가될 "where" 구문에 대해서 지정될 것입니다.

```text
'email' => 'unique:users,email_address,NULL,id,account_id,1'
```

위의 룰에서는, `account_id`가 `1`인 데이터 만이 유니크 검사에 포함됩니다.

**url**

필드는 반드시 PHP `filter_var` 함수에 따라 유효한 URL이어야 합니다.

