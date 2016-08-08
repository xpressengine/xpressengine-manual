# config 디렉토리

`conifg` 디렉토리에는 XE에서 필요한 다양한 설정 정보가 포함된 파일들이 위치합니다. XE는 사용자들이 XE 코어 업데이트로부터 자유로워질 수 있도록 Cascading 방식의 `config` 확장 기능을 지원합니다.

#### Cascading config
이것은 사용자의 XE에 지정된 환경변수에 따라 서로 다른 설정을 사용할 수 있도록 하는 방법중 하나입니다. 이 방식은 지정된 환경변수와 같은 이름의 `config` 디렉토리 내 하위 디렉토리에 정의된 설정 값을 사용하도록 하고 있습니다.

```
config/
├── production
│   ├── app.php
│   ├── database.php
│   └── mail.php
├── app.php
├── auth.php
├── database.php
...
└── xe.php
```

`config` 디렉토리는 위와 같은 구조를 가지고 있습니다.
같은 이름을 가지는 `config/app.php` 와 `config/production/app.php` 파일을 열어보면 아래와 같은 내용을 확인할 수 있습니다.

```
// in config/app.php
return [
    'debug' => env('APP_DEBUG', false),
    ...
```
```
// in config/production/app.php
return [
    'debug' => false,
    ...
```

`config/app.php` 파일에서는 `env` 함수를 사용하여 `debug` 설정을 하고 있습니다. 환경변수 `APP_DEBUG` 가 정의되어 있는 경우 해당 값을, 그렇지 않은경우 `false` 가 지정되도록 되어있습니다. 그리고 `config/production/app.php` 파일에서는 `debug`를 `false`로 설정하고 있습니다.

이 상황에서 만약 사용자의 환경이 `production`이고 `config/production/app.php` 의 `debug` 값을 `true` 로 지정했다면, XE는 `config/app.php`의 설정을 무시하고 `true`를 `debug` 설정으로 사용합니다.

#### 환경변수의 지정

환경변수는 XE 루트 디렉토리에 위치하고 있는 `.env` 파일에서 지정할 수 있습니다.

```
// in .env file
APP_ENV=production
```

만약 사용자가 `local` 개발환경에 대한 설정을 별도로 지정하여 사용하고 싶은 경우, `.env` 파일의 `APP_ENV` 항목에 `local` 로 지정하고, config 디렉토리 내에 `local` 디렉토리를 생성하여 원하는 설정을 해당 디렉토리에 위치시키면 됩니다.
> XE를 처음 설치할 때, 환경변수는 `production`으로 지정됩니다.

`.env` 에 대한 내용은 [다음 문서](env.md)에서 자세히 확인하실 수 있습니다.

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