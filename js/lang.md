# Lang

## XE.Lang.trans\( id \[, parameter\] \)

back-end로부터 전달된 다국어 목록에서 지정된 ID의 다국어를 반환합니다.

* id \(string\)
  * 다국어가 정의된 key값을 지정합니다. \( 필수 요소 \)
* parameter \(object\)
  * object타입으로 2번째 인자에 사용하면 해당 번역에 지정된 문자열을 치환합니다.

back-end에서 다국어를 front-end에 전달하기 위해 아래와 같이 두 가지 방법을 사용할 수 있습니다. `expose_trans` PHP 함수 또는 blade 지시자로 사용할 수 있습니다.

```php
// PHP helper 함수 (core/src/Xpressengine/Support/helpers.php)
expose_trans('xe::name'); // '이름'

// blade directive
@expose_trans('xe::msgHello') // '안녕하세요. :msg'
```

front-end에 전달되지 않은 다국어는 namespace\(구분자 `::` 앞의 문자열\)를 제거한 key를 반환합니다\('xe::msgHello'은 'msgHello'\).

```javascript
//javascript
var name = XE.Lang.trans('xe::name');
var msg = XE.Lang.trans('xe::msgHello', {msg: '반갑습니다.'});
console.log(name) // 이름 or Name
console.log(msg) // '안녕하세요. 반갑습니다.'
```

