# .env
.env 파일은 웹 애플리케이션이 위치한 곳이 어떤 환경인지를 지정하고 현재 환경에서 사용되어질 환경변수들이 작성된 파일입니다.

.env 파일은 다음과 같이 작성되어 집니다.
```
APP_ENV=local
APP_KEY=SomeRandomString
APP_DEBUG=true
APP_LOG_LEVEL=debug
APP_URL=http://localhost

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
...
```
위 내용에서 `APP_ENV` 는 현재 환경을 가리키는 항목이고 기타 나머지는 config 에서 사용되어질 설정값들입니다.

#### 환경변수 추가하기
.env 에서 정의되는 환경변수는 php 파일내에 `env` 함수를 사용하여 작성되어져야 합니다.
```
  'mysql' => [
      'driver'    => 'mysql',
      'host'      => env('DB_HOST', 'localhost'),
      'database'  => env('DB_DATABASE', 'forge'),
      'username'  => env('DB_USERNAME', 'forge'),
      'password'  => env('DB_PASSWORD', ''),
      'port'      => env('DB_PORT', '3306'),
      'charset'   => 'utf8',
      'collation' => 'utf8_unicode_ci',
      'prefix' => 'xe_',
      'strict'    => false,
  ],
```
위 설정에 있는 `host`, `database`, `username`, `password`, `port` 는 .env 파일에 작성되어 값을 지정할 수 있지만, 나머지 `charset`, `prefix` 등은 .env 파일을 통해 지정할 수 없습니다.

만약 기존에 존재하는 설정이외에 추가로 설정이 추가되고, 이 설정들이 .env 파일에 의해 값이 지정되길 원한다면, 해당 설정 값을 `env` 함수를 이용해 작성하면 됩니다.
```
    'new_setting' => env('NEW_SETTING', '');
```
그리고 .env 파일에 해당 값을 지정해 주면 됩니다.
```
NEW_SETTING=MySetting
```

> XE는 설치시 .env 파일이 존재하지 않습니다.
> 현재 시스템의 환경을 지정하거나 환경변수를 추가하고자 하는 경우
> 웹 애플리케이션 root 위치에 .env 파일을 생성하여 사용할 수 있습니다.

