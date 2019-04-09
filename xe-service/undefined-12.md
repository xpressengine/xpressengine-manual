# 프론트엔드

## 프론트엔드\(frontend/assets\)

브라우저에서 어떤 페이지를 요청하면 XE는 보통 모듈 스킨, 테마, 그리고 스킨과 테마에서 사용한 위젯이나 UI오브젝트에서 생성한 html 조각들을 조합하여 하나의 html 문서를 만듭니다. 그리고 이 html 문서에는 다양한 스타일시트와 스크립트 파일이 로드되고 meta 태그 같은 태그들이 html에 추가됩니다. Frontend 서비스는 이렇게 하나의 html 문서를 출력할 때 필요한 다양한 태그를 추가해주고 관리하는 역할을 합니다.

XE에서는 요청을 처리할 때마다 매우 다양한 플러그인이 실행되며, 각각의 플러그인들은 자신이 필요한 스크립트나 스타일시트 파일을 로드하게 됩니다. 만약 Frontend와 같은 관리 주체가 없다면, 플러그인들에 의해 동일한 스크립트 파일이 중복으로 로드될 수 있습니다.

Frontend 서비스는 아래와 같은 역할을 합니다.

* html문서의 타이틀을 지정한다.
* body 태그에 특정 class를 추가한다.
* js파일을 html 특정영역\(head 또는 body의 상,하단\)에 로드한다
* css파일을 html에 로드한다.
* 이미 다른 컴포넌트에서 로드된 js, css파일을 언로드\(unload\) 한다.
* meta 태그를 html에 로드한다.
* custom 태그\(자유로운 형식의 text\)를 html에 추가한다.
* form validation을 위한 rule을 지정한다.\(브라우저에서 script를 통해 실행되는 validation\)
* script에서 사용할 언어팩을 로드한다.

위의 역할을 수행할 때, Frontend 서비스는 여러 곳에서 로드된 js파일이나 css파일의 중복을 처리해줍니다. 또, js파일이나 css파일은 로드되는 순서도 매우 중요합니다. Frontend 패키지는 파일이 로드되는 순서를 지정하는 기능도 제공합니다.

### 브라우저 타이틀 태그 지정하기

`title` 메소드를 사용하십시오.

```php
XeFrontend::title('브라우저 타이틀입니다');
```

### body 태그의 class 지정하기

`bodyClass` 메소드를 사용하십시오.

```php
// body에 'profile' class지정
XeFrontend::bodyClass('profile');
```

### js 파일 로드하기

#### 기본 사용법

`js` 메소드를 사용하여 스크립트 파일을 로드할 수 있습니다. 반드시 마지막에는 `load` 메소드를 사용해야 합니다.

```php
// xe.js파일을 body의 하단에 로드함.
XeFrontend::js('assets/core/common/js/xe.js')->load();
```

이때, `appendTo`, `prependTo` 메소드를 사용하면, html 상에 스크립트 파일이 로드되는 위치를 지정할 수 있습니다. 지정하지 않을 경우 `<body>` 태그 하단에 로드합니다.

```php
// xe.js파일을 head의 하단에 로드함.
XeFrontend::js('assets/core/common/js/xe.js')->appendTo('head')->load();

// xe.js파일을 body의 상단에 로드함.
XeFrontend::js('assets/core/common/js/xe.js')->prependTo('body')->load();
```

배열을 사용하여 다수의 파일을 동시에 로드할 수도 있습니다.

```php
XeFrontend::js([
  'assets/vendor/jquery/jquery.min.js',
  'assets/core/common/js/xe.js',
  'plugin/board/assets/js/my.js'
)->load();
```

#### 우선순위 지정

만약 스크립트 파일을 로드할 때, 반드시 먼저 로드돼야 하는 다른 스크립트 파일이 있다면 `before` 메소드를 사용하여 지정할 수 있습니다. 반대의 경우, `after` 메소드를 사용하십시오.

```php
// bootstrap.js이 로드된 이후에 xe.js파일이 로드되도록 우선순위 지정
XeFrontend::js('assets/core/common/js/xe.js')
->before('assets/vendor/bootstrap/js/bootstrap.js')
->appendTo('body')->load();
```

다수의 파일을 동시에 로드한다면, 배열에 기입된 순서대로 우선순위가 정해집니다.

```php
// 3 파일의 우선순위가 자동으로 지정됨
XeFrontend::js([
  'assets/vendor/jquery/jquery.min.js',
  'assets/core/common/js/xe.js',
  'plugin/board/assets/js/my.js'
)->load();
```

> **주의!** 만약 위의 방법을 사용하여 명시적으로 우선순위를 지정하지 않았다면, 로드된 파일들의 우선순위를 보장할 수 없습니다. 먼저 로드된 파일이라 하더라도 html상에서는 나중에 로드된 파일보다 늦게 로드될 수 있습니다.

#### 언로드

이미 로드된 스크립트 파일이라도 `unload` 메소드를 사용하여 언로드할 수 있습니다.

```php
// 로드된 xe.js파일을 언로드함.
XeFrontend::js('assets/core/common/js/xe.js')->unload();
```

### css 파일 로드하기

css 파일도 js 스크립트 파일과 사용법이 동일합니다. 단 `appendTo`, `prependTo` 메소드를 지원하지 않습니다.

```php
// xe.css파일을 로드함. 반드시 bootstrap.css가 로드된 다음에 로드되도록 우선순위를 지정
XeFrontend::js('assets/core/common/css/xe.css')->before('assets/vendor/bootstrap.css')->load();
```

### meta 태그 추가

`meta` 메소드를 사용하여 meta 태그를 지정할 수 있습니다. meta 태그의 attribute를 지정하기 위해 `name`, `charset`, `property`, `httpEquiv`, `content`를 지원합니다.

```php
// 등록하려는 meta 태그의 별칭 등록.
$alias = 'my.viewport';

XeFrontend::meta($alias)->name('viewport')
->content('width=device-width, initial-scale=1.0')->load();
```

> `alias`는 다른 곳에서 내가 입력한 meta 태그를 언로드할 때 key로 사용합니다.

### custom html 태그 추가하기

`html` 메소드를 사용하면 자유롭게 원하는 코드를 추가할 수 있습니다.

```php
$alias = 'myscript';

// script 코드를 `<body>` 하단에 추가
XeFrontend::html($alias)->content('
<script>
    $(function () {
        $('[data-toggle="tooltip"]').tooltip()
    })
</script>
')->appendTo('body')->load();
```

`unload`, `before`

### icon 파일 로드하기

`icon` 메소드를 사용할 수 있습니다.

```php
XeFrontend::icon($iconUrl)->load();
```

