# 회원 및 인증 서비스

XE는 회원정보를 저장하고 관리하는 기능을 제공합니다. 일반적인 회원 관리 기능으로 신규가입, 회원정보 수정, 회원 삭제와 같은 기능이 있습니다. 또, 인증과 관련된 로그인, 로그아웃, 


## 데이터 구조

#### 회원

회원은 사이트에서 가입 및 인증의 대상이 되는 개념으로, 일반적으로 한명의 사람이라고 생각할 수 있습니다. 회원은 사이트 내에서  권한(permission)을 부여받을 수 있는 대상이 될 수 있습니다.


#### 그룹

회원그룹은 회원을 자유롭게 그루핑할 때 사용합니다. 사이트관리자는 자유롭게 회원그룹을 생성할 수 있고, 회원을 다수의 회원그룹에 소속시킬 수 있습니다. 회원그룹은 권한을 부여받을 수 있는 대상이 될 수 있습니다.


#### 계정

일반적인 회원서비스에서는 회원(user)과 계정(account)을 동일 개념으로 처리합니다. 하지만 XE는 소셜로그인과 같은 외부 계정을 통한 가입/인증을 유연하게 적용하기 위하여 회원과 계정의 개념을 분리했습니다. 회원은 기본계정 이외에 다수의 외부 계정을 가질 수 있으며, 다수의 계정 중 하나로 회원을 인증(authentication)하고 로그인할 수 있습니다.

> XE에서는 회원의 계정정보를 등록(저장)하고 조회, 삭제하는 기능만 제공합니다. 계정정보를 이용한 가입 및 인증은 별도의 플러그인(social_login)을 통해 제공합니다.

#### 이메일

회원은 다수의 이메일을 가질 수 있습니다. 자신의 이메일 중 하나를 선택하여, 로그인할 때 사용할 수 있습니다.

#### 승인대기 이메일

회원은 하나의 승인대기 이메일을 가질 수 있습니다. 신규회원이 가입할 때 입력한 이메일은 승인대기 이메일로 등록됩니다. 승인대기 이메일은 신규회원이 이메일 인증과정을 거친후에 회원이 일반 이메일로 등록됩니다.

#### 회원등급

한명의 회원은 3개의 회원등급(최고관리자, 관리자, 일반회원) 중 하나를 부여받습니다. `최고관리자(super)`는 사이트 내에서 모든 권한을 가지는 운영자입니다. `관리자(manager)`는 일반적으로 사이트를 함께 관리하는 부운영자라고 생각할 수 있습니다. `일반회원(member)`는 사이트에 사용자인 일반적인 회원입니다.


## 회원 관리


회원 및 부가 정보(그룹, 계정, 이메일 등)를 조회하거나 처리할 때에는 `XeUser` 파사드를 사용하십시오. `XeUser` 파사드의 실제 구현은 `\Xpressengine\User\UserHandler`에 정의되어 있습니다.

### 회원 조회


#### 회원아이디로 회원조회

회원 아이디로 회원 조회할 때에는 `find` 메소드를 사용하십시오.

```php
$user = XeUser::find($id);
$username = $user->getDisplayName();
```

여러 회원을 조회할 수도 있습니다.

```php
$ids = [1,2,3];
$users = XeUser::find($ids);

foreach($users as $user) {
  ...
}
```

#### 회원 정보로 회원조회

다양하고 복잡한 조건으로 회원을 조회할 수도 있습니다. 자세한 사용법은 [라라벨 문서](https://laravel.com/docs/5.2/eloquent)를 참조하십시오.

```php
// displayName이 foo인 회원 조회
$user = XeUser::where('displayName', 'foo')->first();
```

```php
// displayName이 foo로 시작하는 모든 회원 조회
$user = XeUser::where('displayName', 'like', '%foo')->get();
```


### 신규 회원 추가

`XeUser`는 복잡한 회원 생성 과정을 한번에 처리해주는 `create` 메소드를 제공합니다. `create` 메소드는 입력한 신규회원 정보에 대한 유효성 검사후 신규회원을 생성합니다. 또한 회원의 계정(account), 이메일(email) 정보도 자동으로 추가되며, 소속될 그룹에 대한 정보가 전달되었을 경우, 그룹에 추가시켜주기도 합니다.

```php
$data = [
  'displayName' => 'foo',
  'email' => 'foo@email.com',
  //...
];

$newUser = XeUser::create($data);
```

신규 회원의 계정(account) 정보를 같이 등록할 수도 있습니다.

```php
$data = [
  'displayName' => 'foo',
  'email' => 'foo@email.com',
  //...
]

$data['account'] = [
  'provider' => 'facebook',
  'token' => '3DIfdkwwfdsie...',
  'id' => 'id of facebook user',
  'data' => '...'
]

$newUser = XeUser::create($data);
```

신규회원이 소속될 회원그룹을 지정할 수도 있습니다.

```php
  $data = [
  'displayName' => 'foo',
  'email' => 'foo@email.com',
  'groupId' => [21,23] // 그룹아이디가 21, 23인 그룹에 회원을 소속시킴
]

$newUser = XeUser::create($data);
```

만약 유효성검사를 생략하거나 부가 정보(account, email, group)를 등록을 하지 않고, 순수하게 회원 정보만 추가하고 싶다면 `XeUser` 파사드 대신, `UserRepository`를 사용하십시오.

`UserRepository`는 `XeUser` 파사드를 사용하여 로드할 수 있습니다.

```php
// 순수 회원정보만 등록
$data = [
'displayName' => 'foo',
'email' => 'foo@email.com',
'password' => '...'
]

$newUser = XeUser::users()->create($data);
```

> `UserRepository`의 `create` 메소드를 곧바로 사용할 때, `password`를 암호화하지 않고 바로 저장합니다.
먼저 `password` 필드를 직접 암호화하십시오.


### 회원정보 수정

`XeUser::update` 메소드를 사용하면 회원정보를 변경할 수 있습니다. 유효성 검사 및 프로필 이미지 처리, 소속그룹의 변경도 동시에 처리합니다.

```php
$user = XeUser::find(20);
XeUser::update($user, ['displayName' => 'bar']);
```

### 회원삭제

`XeUser::leave` 메소드를 사용하면 회원을 삭제(탈퇴)할 수 있습니다. 삭제할 회원의 부가정보(account, email)도 같이 삭제됩니다.

```php
$ids = [12,23,34];
XeUser::leave($ids); // 3명의 회원을 탈퇴시킴
```

`UserRepository`의 `delete` 메소드를 사용하여 회원을 삭제할 수도 있습니다. 단, 이 메소드를 사용하면 삭제될 회원의 부 가 정보는 함께 삭제되지 않습니다.

```php
$user = XeUser::find(12);
XeUser::users()->delete($user);
```

### 회원 부가 정보의 처리


UserHandler에서 제공하는 생성(create), 삭제(delete), 업데이트(update) 기능은 아래 코드를 참고하십시오.

```php

// 그룹정보 생성/수정/삭제
$groupData = [
'name' => '정회원',
'description' => '기본회원',
];

$group = XeUser::createGroup($groupData);
XeUser::updateGroup($group, ['name' => '기본회원']);
XeUser::deleteGroup($group);

// 이메일정보 생성/수정/삭제
$user = XeUser::find('123');
$data = [
'address' => 'foo@email.com'
];
$confirmed = true; // 승인|승인대기 이메일 구분

$email = XeUser::createEmail($user, $data, $confirmed);
XeUser::updateEmail($email, ['address' => 'bar@email.com']);
XeUser::deleteEmail($email);

// 계정정보 생성/수정/삭제
$data = [
'provider' => 'facebook',
// ....
]

$account = XeUser::createAccount($user, array $data);
XeUser::updateAccount($account, ['data'=>'...']);
XeUser::deleteAccount($account);
```



### 회원 부가 정보의 조회

계정(account), 이메일(email), 승인대기 이메일(pending email), 그룹(group)과 같은 회원의 부가 정보를 조회할 때에는  각각의 Repository를 사용하십시오.

```php
// 각 Repository의 로드
$accountRepository = XeUser::accounts();
$emailRepository = XeUser::emails();
$pendingEmailRepository = XeUser::pendingEmails();
$groupRepository = XeUser::groups();
```







#### 회원계정(account)

XE에서 각각의 회원(user)는 여러개의 외부 계정을 가질 수 있습니다.
만약 한 회원이 facebook, google, naver 등 여러개의 외부 계정을 소유하고 있다면, 소유한 계정중 하나를 이용하여 로그인할 수 있습니다.

회원계정을 조회할 때에는 UserAccountRepository를 사용하십시오.
UserAccountRepository는 UserHandler를 통하여 가져올 수 있습니다.

```php
$accountRepository = XeUser::accounts();
```

한 회원이 소유한 계정 목록을 조회할 수 있습니다.

```php
// 회원계정아이디로 회원계정정보 조회
$userId = '123';
$accounts = XeUser::accounts()->findByUserId($accountId);
```

위 코드는 아래 코드로 대체할 수도 있습니다.

```php
$user = XeUser::users()->find('123');
$accounts = $user->accounts;
```

> UserAccountRepository는 laravel의 Eloquent 모델의 사용법을 대부분 그대로 사용할 수 있습니다.
laravel Eloquent 모델의 사용법은 [라라벨 문서](https://laravel.com/docs/5.2/eloquent)를 참조하십시오.

#### 회원 이메일(email)

XE에서 각각의 회원은 여러개의 이메일을 가질 수 있습니다.
만약 한 회원이 여러개의 이메일을 소유하고 있다면, 소유한 이메일 중 하나와 비밀번호를 사용하여 로그인 할 수 있습니다.

회원의 이메일을 조회할 때에는 UserEmailRepository를 사용하십시오.
UserEmailRepository는 UserHandler를 통하여 가져올 수 있습니다.

```php
$emailRepository = XeUser::emails();
```

이메일 주소를 사용하여 이메일정보 조회할 수 있습니다.

```php
$email = XeUser::emails()->findByAddress('myaddress@xpressengine.com');
```

#### 승인 대기중인 이메일(pending email)

회원이 소유한 이메일은 승인된 이메일과 승인 대기중인 이메일로 구분됩니다.
승인된 이메일(email)과 승인대기 이메일(pendingEmail)은 별도의 테이블에 저장됩니다.
승인된 이메일은 한 회원이 여러개 가질 수 있지만, 승인대기 이메일은 한 회원당 하나만 가질 수 있습니다.

승인대기 이메일 주소로는 로그인을 할 수 없습니다.

회원의 승인대기 이메일을 조회할 때에는 PendingEmailRepository를 사용하십시오.
PendingEmailRepository는 UserHandler를 통하여 가져올 수 있습니다.

```php
$pendingEmailRepository = XeUser::pendingEmails();
```

```php
$pendingEmail = XeUser::pendingEmails()->findByUserId('123');
```
































### 회원조회

### 신규회원 추가


### 회원삭제

### 회원정보수정


## 인증

#### 로그인

#### 로그아웃

#### 인증확인

#### 소셜로그인