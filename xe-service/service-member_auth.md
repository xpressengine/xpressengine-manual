# 회원/인증

## 회원/인증\(user/auth\)

### 회원 및 인증 서비스

XE는 회원정보를 저장하고 관리하는 기능을 제공합니다. 일반적인 회원 관리 기능으로는 신규가입, 회원정보 수정, 회원 삭제와 같은 기능이 있습니다. 또, 인증 시도, 로그인, 로그아웃과 같은 인증 관련 기능도 함께 제공합니다.

회원 관리 기능을 정확히 사용하기 위해서는 회원 및 회원의 부가정보들의 데이터 구조를 정확히 파악해야 합니다.

#### 데이터 구조

**회원**

회원은 사이트에서 가입 및 인증의 대상이 되는 개념으로, 일반적으로 한명의 사람이라고 생각할 수 있습니다. 회원은 사이트 내에서 권한\(permission\)을 부여받을 수 있는 대상이 될 수 있습니다.

XE는 회원정보를 `\Xpressengine\User\Models\User` 엘로퀀트 모델로 표현하며 `user` 테이블에 저장합니다.

**계정**

일반적인 회원서비스에서는 회원\(user\)과 계정\(account\)을 동일 개념으로 처리합니다. 하지만 XE는 소셜로그인과 같은 외부 계정을 통한 가입/인증을 유연하게 적용하기 위하여 회원과 계정의 개념을 분리했습니다. 회원은 기본계정 이외에 다수의 외부 계정을 가질 수 있으며, 다수의 계정 중 하나로 회원을 인증\(authentication\)하고 로그인할 수 있습니다.

만약 소셜로그인과 같이 외부 사이트에서 제공하는 회원서비스를 사용하는 회원가입/로그인 기능을 구현하고 싶다면, 회원 계정을 잘 활용하시길 바랍니다.

XE는 계정정보를 `\Xpressengine\User\Models\UserAccount` 엘로퀀트 모델로 표현하며 `user_account` 테이블에 저장합니다.

> XE에서는 회원의 계정정보를 등록\(저장\)하고 조회, 삭제하는 기능만 제공합니다. 계정정보를 이용한 가입 및 인증은 별도의 플러그인\(social\_login\)을 통해 제공합니다.

**이메일**

한명의 회원은 다수의 '승인된 이메일'을 소유할 수 있습니다. 만약 회원이 다수의 이메일을 소유하고 있다면, 소유한 이메일 중 하나와 비밀번호를 사용하여 로그인 할 수 있습니다.

다수의 이메일 중 하나는 회원의 대표 이메일로 지정됩니다. 대표 이메일은 회원에게 이메일을 전송하거나 외부에 공개될 때 사용되는 이메일입니다.

XE는 이메일 정보를 `\Xpressengine\User\Models\UserEmail` 엘로퀀트 모델로 표현하며 `user_email` 테이블에 저장합니다.

**승인대기 이메일**

회원은 하나의 '승인대기 이메일'을 가질 수 있습니다. 신규회원이 가입할 때 입력한 이메일은 승인대기 이메일로 등록됩니다. 승인대기 이메일은 승인된 이메일과 별도의 테이블에 저장되며, 이메일 인증과정을 거친 후에 승인된 이메일로 등록됩니다.

XE는 승인대기 이메일 정보를 `\Xpressengine\User\Models\PendingEmail` 엘로퀀트 모델로 표현하며 `user_pending_email` 테이블에 저장합니다.

> 사이트 관리자가 '이메일 인증' 기능을 사용하도록 설정했을 경우, 가입시 등록한 이메일 승인대기 이메일에 등록됩니다. 만약 이메일 인증 기능을 사용하지 않는다면, 가입과 동시에 일반 이메일로 등록됩니다.

**회원등급**

한명의 회원은 3개의 회원등급\(최고관리자, 관리자, 일반회원\) 중 하나를 부여받습니다. `최고관리자(super)`는 사이트 내에서 모든 권한을 가지는 운영자입니다. `관리자(manager)`는 일반적으로 사이트를 함께 관리하는 부운영자라고 생각할 수 있습니다. `일반회원(member)`는 사이트에 사용자인 일반적인 회원입니다.

회원등급은 별도의 테이블에 저장되지 않으며, 회원\(user\) 테이블의 `rating` 컬럼에 저장됩니다.

**그룹**

회원그룹은 회원을 자유롭게 그루핑할 때 사용합니다. 사이트관리자는 자유롭게 회원그룹을 생성할 수 있고, 회원을 다수의 회원그룹에 소속시킬 수 있습니다. 회원그룹은 권한을 부여받을 수 있는 대상이 될 수 있습니다.

XE는 그룹 정보를 `\Xpressengine\User\Models\UserGroup` 엘로퀀트 모델로 표현하며 `user_group` 테이블에 저장합니다. 또, 회원과 회원이 소속된 그룹의 관계는 `user_group_user` 테이블에 저장됩니다.

#### 회원 관리

회원 및 부가 정보\(그룹, 계정, 이메일 등\)를 조회하거나 처리할 때에는 `XeUser` 파사드를 사용하십시오. `XeUser` 파사드의 실제 구현은 `\Xpressengine\User\UserHandler`에 정의되어 있습니다.

**회원 조회**

회원 아이디로 회원을 조회할 때에는 `find` 메소드를 사용하십시오.

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

다양하고 복잡한 조건\(쿼리\)으로 회원을 조회할 수도 있습니다. 자세한 사용법은 [라라벨 문서](https://laravel.com/docs/5.2/eloquent)를 참조하십시오.

```php
// displayName이 'foo'인 회원 조회
$user = XeUser::where('displayName', 'foo')->first();
```

```php
// displayName이 foo로 시작하는 모든 회원 조회
$user = XeUser::where('displayName', 'like', '%foo')->get();
```

**신규 회원 추가**

`XeUser`는 복잡한 회원 생성 과정을 한번에 처리해주는 `create` 메소드를 제공합니다. `create` 메소드는 입력한 신규회원 정보에 대한 유효성 검사후 신규회원을 생성합니다. 또한 회원의 계정\(account\), 이메일\(email\) 정보도 자동으로 추가되며, 소속될 그룹에 대한 정보가 전달되었을 경우, 그룹에 추가시켜주기도 합니다.

```php
$data = [
  'displayName' => 'foo',
  'email' => 'foo@email.com',
  //...
];

$newUser = XeUser::create($data);
```

신규 회원의 계정\(account\) 정보를 같이 등록할 수도 있습니다.

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

만약, 유효성검사를 생략하거나 부가 정보\(account, email, group\)를 등록을 하지 않고, 순수하게 회원 정보만 추가하고 싶다면 `XeUser` 파사드 대신, `UserRepository`를 사용하십시오.

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

> `UserRepository`의 `create` 메소드를 곧바로 사용할 때, `password`를 암호화하지 않고 바로 저장합니다. 먼저 `password` 필드를 직접 암호화하십시오.

**회원정보 수정**

`XeUser::update` 메소드를 사용하면 회원정보를 변경할 수 있습니다. 유효성 검사 및 프로필 이미지 처리, 소속그룹의 변경도 동시에 처리합니다.

```php
$user = XeUser::find(20);
XeUser::update($user, ['displayName' => 'bar']);
```

**회원 삭제**

`XeUser::leave` 메소드를 사용하면 회원을 삭제\(탈퇴\)할 수 있습니다. 삭제할 회원의 부가정보\(account, email\)도 같이 삭제됩니다.

```php
$ids = [12,23,34];
XeUser::leave($ids); // 3명의 회원을 탈퇴시킴
```

`UserRepository`의 `delete` 메소드를 사용하여 회원을 삭제할 수도 있습니다. 단, 이 메소드를 사용하면 삭제될 회원의 부가 정보는 함께 삭제되지 않습니다.

```php
$user = XeUser::find(12);
XeUser::users()->delete($user);
```

**회원 계정 관리**

**계정 조회**

회원계정을 조회할 때에는 `UserAccountRepository`를 사용하십시오. `UserAccountRepository`는 `XeUser`파사드를 사용하여 로드할 수 있습니다.

```php
$userAccountRepository = XeUser::accounts();
```

회원이 소유한 계정 목록을 조회할 수 있습니다.

```php
// 회원 아이디로 계정 정보 조회
$userId = '123';
$accounts = XeUser::accounts()->findByUserId($accountId);
```

위 코드는 아래 코드로 대체할 수도 있습니다.

```php
$user = XeUser::find('123');
$accounts = $user->accounts;
```

`User`의 `getAccountByProvider` 메소드를 사용하면 특정 프로바이더\(소셜로그인 벤더\)의 계정을 조회할 수 있습니다.

```php
$user = XeUser::find('123');
$facebookAccount = $user->getAccountByProvider('facebook');
```

좀 더 복잡한 조건으로 계정을 조회하고 싶다면, `query`메소드를 사용하십시오.

```php
$query = XeUser::accounts()->query();
$account = $query->where('email', 'foo@facebook.com')->first();
```

**계정 생성**

회원계정을 생성할 때에는 `XeUser`파사드를 사용할 수 있습니다. `createAccount` 메소드를 사용하십시오.

```php
// 기존 회원에 계정정보 추가하기
$user = XeUser::find('123');

$accountData = [
  'email' => 'foo@facebook.com',
  'accountId' => 'facebookId',
  'provider' => 'facebook',
  'token' => '39238432893,
  'data' => '...'
];

$newAccount = XeUser::createAccount($user, $accountData);
```

**계정 수정**

회원계정을 수정할 때에도 `XeUser`파사드를 사용할 수 있습니다. `updateAccount` 메소드를 사용하십시오.

```php
$user = XeUser::find('123');
$facebookAccount = $user->getAccountByProvider('facebook');

XeUser::updateAccount($facebookAccount, [token => '2197548']);
```

**계정 삭제**

회원계정을 수정할 때에는 `XeUser::deleteAccount` 메소드를 사용하십시오.

```php
XeUser::deleteAccount($facebookAccount);
```

**회원 이메일 관리**

**이메일 조회**

회원계정을 조회할 때에는 `UserEmailRepository`를 사용하십시오. `UserEmailRepository`는 `XeUser`파사드를 사용하여 로드할 수 있습니다.

```php
$userEmailRepository = XeUser::emails();
```

이메일 주소로 이메일 정보를 조회할 수 있습니다.

```php
$email = XeUser::emails()->findByAddress('foo@bar.com');
```

특정 회원이 소유한 모든 이메일을 조회할 수 있습니다.

```php
$userId = '123';
$emails = XeUser::emails()->findByUserId($userId);
```

좀 더 복잡한 조건으로 이메일을 조회하고 싶다면, `query`메소드를 사용하십시오.

```php
// 'foo@'로 시작되는 이메일 검색
$query = XeUser::emails()->query();
$emails = $query->where('address', 'like', 'foo@%')->get();
```

승인대기 이메일을 조회할 때에는 `UserEmailRepository` 대신 'PendingEmailRepository'를 사용하십시오. `PendingEmailRepository`는 `XeUser`파사드를 사용하여 로드할 수 있습니다. `UserEmailRepository`와 동일하게 사용할 수 있습니다.

```php
$userEmailRepository = XeUser::pendingEmails();
```

**이메일 생성**

회원 이메일을 생성할 때에는 `XeUser`파사드의 `createEmail` 메소드를 사용하십시오.

```php
$user = XeUser::find('123');

$emailData = [
  'address' => 'foo@bar.com'
];

$email = XeUser::createEmail($user, $emailData, true);
```

`createEmail` 메소드의 마지막 메소드는 생성하는 이메일의 승인 여부를 지정합니다. `true`이면 승인된 이메일로 저장되며, `false`이면 승인대기 이메일로 저장됩니다.

**이메일 수정**

기등록 된 이메일\(또는 승인대기 이메일\)을 수정할 수 있습니다. `XeUser::updateEmail`을 사용하십시오.

```php
$email->address = 'foo@bar.com';
XeUser::updateEmail($email);

// or

XeUser::updateEmail($email, ['address'=>'foo@bar.com']);
```

**이메일 삭제**

이메일\(또는 승인대기 이메일\)을 삭제할 수 있습니다. `XeUser::deleteEmail`을 사용하십시오.

```php
XeUser::deleteEmail($email);
```

**회원 그룹 관리**

**그룹 조회**

...

**그룹 생성**

```php
// 그룹정보 생성
$groupData = [
  'name' => '정회원',
  'description' => '기본회원',
];

$group = XeUser::createGroup($groupData);
```

**그룹 수정**

```php
XeUser::updateGroup($group, ['name' => '기본회원']);
```

**그룹 삭제**

```php
XeUser::deleteGroup($group);
```

#### 인증

등록된 회원은 사이트에 로그인할 수 있습니다. 사이트에 로그인할 때 XE는 입력된 회원정보에 대한 인증\(authentication\) 과정을 거친후, 세션\(session\)에 로그인 한 회원의 정보를 등록합니다. 로그아웃을 할 때에는 반대로 세션에 등록했던 회원 정보를 삭제합니다.

`Auth` 파사드를 사용하면 현재 접속한 회원의 로그인, 로그아웃을 비롯한 인증과 관련된 기능을 수행할 수 있습니다.

**현재 로그인한 사용자 조회**

현재 로그인한 사용자를 조회할 때에는 `user` 메소드를 사용하십시오.

```php
$user = Auth::user();
```

또는 request 인스턴스를 사용할 수도 있습니다.

```php
$user = request()->user();
```

로그인한 사용자의 아이디\(id\)만 조회할 수도 있습니다.

```php
$loggedUserId = Auth::id();
```

**로그인 상태 조회**

`check` 메소드를 사용하면 접속 중인 사용자가 로그인을 한 상태인지 조회할 수 있습니다.

```php
if (Auth::check()) {
    // The user is logged in...
}
```

**로그인**

등록된 회원의 인스턴스를 획득하고 있다면, `login` 메소드를 사용하여 해당 회원을 로그인시킬 수 있습니다.

```php
$user = XeUser::find('123');

Auth::login($user);
```

해당 회원이 현재와 동일한 환경\(단말기의 브라우저\)에서 다음에 다시 사이트에 접속했을 때, 자동으로 로그인 처리를 하는 '로그인 유지' 기능을 사용할 수 있습니다. `login` 메소드의 두번째 파라메터로 `true`를 사용하면, 해당 회원에게 '로그인 유지' 기능을 활성화시킵니다.

```php
// Login and "remember" the given user...
Auth::login($user, true);
```

**로그인 시도**

`login` 메소드는 회원정보의 인증 과정을 거치지 않고, 회원 인스턴스를 통해 바로 로그인 처리를 합니다. 만약 회원의 입력한 정보를 인증한 후 로그인까지 시도하고 싶을 때에는 `attempt`메소드를 사용하십시오.

`attempt` 메소드를 실행하면 입력된 정보가 등록된 회원정보와 일치하는지 검사합니다. 회원정보가 일치한다면 해당 회원을 로그인시킵니다.

```php
if (Auth::attempt(['email' => $email, 'password' => $password])) {
    // Authentication passed...
    return redirect()->intended('dashboard');
}
```

만약 '로그인 유지' 기능을 사용하고싶을 때에는 두번째 파라메터로 `true`를 전달하십시오.

```php
if (Auth::attempt(['email' => $email, 'password' => $password], true)) {
    // The user is being remembered...
}
```

**로그아웃**

`logout` 메소드를 사용하십시오.

```php
Auth::logout();
```

