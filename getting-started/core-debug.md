# 문제 해결\(디버깅\)


<blockquote class="caution">
    <p>이 문서에서는 창작자, 기술기업에게 문제해결을 위하여 전달해야하는 내용이 있을때 사용할 수 있는 문서입니다.<br>
        디버깅 모드를 활성화 해 놓으면, 일반 사용자도 오류가 발생할 경우, 브라우저에서 오류의 자세한 내용을 볼 수 있으므로, 보안상 문제가 될 수 있습니다.
        실 서비스 운영시에는 민감정보가 노출되지 않도록 유의하여 사용해 주시기 바랍니다.
    </p>
</blockquote>


XE3가 예상대로 설치되지 않거나 작동하지 않으면, 사용자 환경 시스템 요구 사항을 충족하는지 확인해야합니다 . XE3가 실행해야하는 항목이 누락되면 먼저 해결해야합니다.

그런 다음 QnA와 커뮤니티를 검색하려면 몇 분이 걸릴 수 있습니다. 누군가가 이미 문제를 보고 했을 가능성이 있으며 해결 방법이 있거나 사용할 수 있습니다. 철저하게 검색했지만 문제에 대한 정보를 찾을 수 없는 경우 문제 해결을 시작할 시간입니다.



## 디버깅 모드 활성화

XE3를 디버깅모드로 설정하면 오류가 발생한 경우, 브라우저에 오류의 자세한 정보가 바로 출력됩니다.
디버그 모드를 활성화 하기위해서는 `config/production/app.php` 파일의 `debug` 값을 `true`로 설정해야 합니다.

현재 환경에 맞는 config 파일을 열어 값을 변경합니다.

```php
//in config/production/app.php
  ...
  'debug' => true,
  ...
```

`.env` 파일에 `debug` 모드를 지정할 수도 있습니다.

```php
// in config/production/app.php
  ...
  'debug' => env('APP_DEBUG', true)
  ...
```

위와 같이 `config/production/app.php` 파일을 설정하고, XE3의 루트디렉토리의 `.env` 파일에는 아래와 같이 작성합니다.

```text
# in .env file
APP_DEBUG=true

```



## 변수값 확인하기\(dump\)

디버깅을 위해 특정시점에서 변수의 값이나 함수의 반환값 등을 확인할 필요가 있습니다. XE3는 변수의 값을 브라우저에 곧바로 출력하거나 로그 파일에 기록해 놓을 수 있는 기능을 제공합니다.

### 브라우저에 출력하기

XE3는 `dump` 와 `dd` 함수를 제공하고 있습니다. `dump`는 php 의 내장함수인 `var_dump` 와 유사하지만 브라우저 상에서 좀 더 보기 좋은 형태로 표현해 줍니다. `dd` 는 dump & die 의 약어로 dump 처리 후 라이프사이클을 중단시킵니다.

```php
dump($var);
dd($var);
```

여러가지 값을 동시에 확인하고자 한다면 해당 변수들을 인자로 나열하면 됩니다.

```php
dump($foo, $bar, $baz);
dd($foo, $bar, $baz);
```

## 로그파일에 기록하기
XE3에서는 디버그 모드를 활성화 할 경우 별다른 설정이 없다면, 최대 5개의 로그 파일을 날짜별로 저장하는 daily 설정을 기본으로 두고 있습니다.

XE3는 php의 강력한 로깅시스템인 monolog를 사용하고 있습니다.
``single`` , ``daily`` , ``syslog`` , ``errorlog`` 타입 설정을 활용하면 파일 하나에 로깅하는것, 일별로 로깅하는것, 시스템 자체에도 로깅할 수 있습니다.

```php
// in config/app.php

//Single Mode laravel.log
'log' => env('APP_LOG', 'single'),

//Daily Mode - laravel-yyyy-mm-dd.log
'log' => env('APP_LOG', 'daily'),

//Syslog Mode
'log' => env('APP_LOG', 'syslog'),

//Errorlog Mode
'log' => env('APP_LOG', 'errorlog'),

```

Log 파사드를 이용해 로그 파일에 내용을 기록할 수 있습니다. 로그 파일의 위치는 `storage/log/laravel-yyyy-mm-dd.log`입니다.

```text
$var = 'a';
$bar = 'b';
\Log::info($var . ' ' . $bar); // [0000-00-00 00:00:00] production.INFO: a b
```

<blockquote class="safe">
    <p>log 파일에는 사용자의 호출에 의한 기록 이외에도 장애시 발생한 오류 정보 등이 기록되어 있습니다.</p>
</blockquote>

