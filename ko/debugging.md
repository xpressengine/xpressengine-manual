# 디버깅

XE를 운영하거나 개발하는 과정에서 오류가 발생하는 경우, 발생한 오류에 대한 자세한 정보를 필요로 합니다.

#### 디버깅 활성화
XE를 디버깅모드로 설정하면 브라우저를 통해 오류를 확인할 수 있습니다.
디버그를 활성화 하기위해서는 `config/production/app.php` 파일의 `debug` 값을 `true`로 설정해야 합니다.

현재 환경에 맞는 config 파일을 열어 값을 변경합니다.

```php
//in config/production/app.php
  ...
  'debug' => true,
  ...  
```

`.env` 파일에 `debug` 설정을 지정할 수도 있습니다.

```php
// in config/production/app.php
  ...
  'debug' => env('APP_DEBUG', true)
  ...
```

위와 같이 `config/production/app.php` 파일을 설정하고, XE의 루트디렉토리의 `.env` 파일에 아래와 같이 지정합니다.

```
// in .env file
APP_DEBUG=true
```

#### 정보확인 하기
디버깅을 위해 변수의 특정시점의 값이나 함수의 반환값등을 확인할 필요가 있을때가 있습니다.
laravel 에서 제공하는 기능을 통해 이러한 값들을 확인할 수 있습니다.

##### Helper
laravel 에서는 `dump` 와 `dd` 함수를 제공하고 있습니다.
`dump`는 php 의 내장함수인 `var_dump` 와 유사하지만 브라우저 상에서 좀 더 보기좋은 형태로 표현해줍니다. `dd` 는 dump & die 의 약어로 dump 처리 후 라이프사이클을 중단시킵니다.
```
dump($var);
dd($var);
```
여러가지 값을 동시에 확인하고자 한다면 해당 변수들을 인자로 나열하면 됩니다.
```
dump($foo, $bar, $baz);
dd($foo, $bar, $baz);
```

##### Log
Log 패키지를 이용해 로그 파일에 내용을 기록할 수 있습니다.
```
$var = 'a';
$bar = 'b';
Log::info($var . ' ' . $bar); // [0000-00-00 00:00:00] production.INFO: a b
```

> log 파일은 `storage/log/laravel.log` 에 위치하고 있습니다.

> log 파일에는 사용자의 호출에 의한 기록이외에도 장애시 발생한 exception 정보등이 기록되어 있습니다.


