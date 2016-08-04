# config 디렉토리

conifg 디렉토리는 XE를 구동 시키기 위한 다양한 값들이 작성되어진 파일들이 위치하는 곳입니다. XE에서는 사용자들이 코어 업데이트로부터 자유로워질 수 있도록 Cascading 방식의 config 확장 기능을 지원합니다.

#### Cascading config
이것은 사용자의 웹 애플리케이션에 지정된 환경변수에 따라 서로 다른 설정을 사용할 수 있도록 하는 방법중 하나입니다. 이 방식은 지정된 환경변수와 같은 이름의 config 디렉토리 내 하위 디렉토리에 정의된 설정 값을 사용하도록 하고 있습니다.
 
```
config/
  |- production/
       |- app.php
       |- database.php
       |- mail.php
  |- app.php
  |- auth.php
  |- database.php
  ...
```
config 디렉토리는 위와 같은 구조를 가지고 있습니다. 
같은 이름을 가지는 `config/app.php` 와 `config/production/app.php` 파일을 열어보면 아래와 같은 내용을 확인할 수 있습니다.
* config/app.php
```
return [
    'debug' => env('APP_DEBUG', false),
    ...
```
* config/production/app.php
```
return [
    'debug' => false,
    ...
```
`config/app.php` 파일에서는 debug 값을 env함수에 의해 환경변수 `APP_DEBUG` 가 정의 되어있는 경우 해당 값을, 그렇지 않은경우 `false` 가 지정되도록 되어있습니다. 그리고 `config/production/app.php` 파일에서는 debug 값이 `false` 로 지정되어 있습니다.
이 상황에서 만약 사용자의 환경이 `production` 이고 `config/production/app.php` 의 debug 값을 `true` 로 지정했다면 해당 웹 애플리케이션에서 설정된 debug 값은 `config/app.php` 의 값을 무시하고 `true` 로 지정되는 것입니다.

#### 환경변수의 지정
환경변수는 웹 애플리케이션 root 위치에 `.env` 파일에서 지정할 수 있습니다.
* .env
```
APP_ENV=production
```

만약 사용자가 `local` 개발환경에 대한 설정을 별도로 지정하여 사용하고 싶은 경우, `.env` 파일에 `APP_ENV` 항목의 값을 `local` 로 지정하고 config 디렉토리내에 `local` 디렉토리를 생성하여 원하는 설정을 해당 디렉토리에 위치시키면 됩니다.
> XE설치시 최초 웹 애플리케이션의 환경 값은 `production` 입니다.

.env 에 대한 내용은 [다음](링크)에서 자세히 확인하실 수 있습니다.

#### config caching
config 파일에는 `env`, `storage_path`와 같은 함수들을 사용하여 값을 지정하고 있습니다.
이는 config 로 부터 값을 얻고자 할때 해당 함수가 실행되어야 함을 의미합니다. 그래서 laravel 에서는 좀 더 빠르게 설정값을 불러올 수 있도록 cache 파일을 생성할 수 있는 command 를 제공하고 있습니다.
```
$ php artisan config:cache
```
console 화면에서 위 명령어를 실행하면 config 디렉토리내에 설정된 모든 값이 cache 파일로 생성되어집니다.

이미 생성된 config cache 파일을 제거하고 싶은 경우 아래 명령어를 통해 제거가 가능합니다.
```
$ php artisan config:clear
```