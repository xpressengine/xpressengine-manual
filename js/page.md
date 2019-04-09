# Page



## Page

`XE.page()`의 기능을 사용하기 위해서는 **xe-page.js**파일을 로드하여야 합니다.

```php
// blade파일(php)에서 로드할 경우
{{ XeFrontend::js('assets/core/xe-ui-component/js/xe-page.js')->appendTo('body')->load() }}
```

### XE.page\(url, target, options, callback\)

target영역에 html을 로드하여 화면에 랜더링합니다. response로 html 및 js, css파일들의 경로를 전달 받습니다.

#### 동작 순서

1. js, css파일 로드
2. html 로드
3. callback 실행 

#### arguments

* url \(string\)
  * ajax가 호출될 url
* target \(string\)
  * html이 append될 selector 
* options \(object\)
  * data \(object\) 전송 파라미터
  * type \(string\) http method 'get'\|'post'
  * addType \(string\) target에 response html을 넣어주는 방식의 타입 `append`, `prepend`, `before`, `after`. 옵션을 명시하지 않을 경우 target영역에 html을 덮어 넣습니다.

#### callback \(function\)

html append이후에 실행될 callback

#### DOM Element에 `data-*` attruibute를 이용한 XE.page 사용 방법

xe.page.js파일을 로드하면 `data-toggle="xe-page"` attribute를 사용한 DOM에 click이벤트를 바인딩 합니다.

* `assets/core/common/xe.page.js`의 파일을 로드합니다.
* 클릭되는 DOM에 `data-toggle='xe-page'` attribute를 명시하여야 합니다.
* href or data-url에 ajax를 요청할 url정보를 명시합니다.
* data-target으로 append할 영역의 selector를 명시합니다.
* data-callback으로 callback명을 명시합니다.
* data-params로 요청시 전송할 파라미터 정보를 명시합니다. \(JSON string\)

```markup
<a href="/api/test" 
    data-toggle="xe-page" 
    data-params="{'param1':'value1'}" 
    data-target="#target" 
    data-callback="callbackFunc">[XE.page 실행]</a>
```

