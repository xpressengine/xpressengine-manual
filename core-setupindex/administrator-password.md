# 관리자 비밀번호 설정
회원정보 또는 테마의 코드와 같은 민감한 페이지를 접근할때에 비밀번호를 사용할 수 있습니다.

## 기본 설정 값
기본적으로 XE를 설치하고나서 관리자 인증 비밀번호는 관리자의 로그인 비밀번호와 동일합니다.
만약 사이트 관리자를 제외하고 회원 또는 테마만 관리하는 관리자가 따로 있는 경우 비밀번호를 분리하여 관리할 수 있습니다.


## 설정 값
- expire : 인증 시간이 얼마나 유효한지를 입력합니다. (분 단위, 기본 30분)
- password : 인증 비밀번호


## 비밀번호 설정
파일 설정은 `config/production/auth.php` 파일에 `password`항목으로 지정됩니다.
```php
return [
    'admin' => [
        'session' => 'auth.admin',
        'expire' => 30,
        'password' => 'admin',
    ],
];
```

위의 내용은 관리자 아이디의 비밀번호가 admin일때 기본 생성된 관리자 비밀번호입니다.<br>
비밀번호는 아래처럼 변경할 수 있습니다.

```php
return [
    'admin' => [
        'session' => 'auth.admin',
        'expire' => 30,
        'password' => 'password!@#admin',
    ],
];
```

