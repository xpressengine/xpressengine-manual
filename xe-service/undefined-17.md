# 카운터

## 카운터\(counter\)

수를 세는 일은 중요하지도 않고 복잡하지도 않으며 자주 사용되는 기능입니다. 우리는 글 조회수를 세거나 투표, 신고등 여러가지 기능을 구현할 때 로그를 남기고 그 수를 세려고 합니다.

수를 세는 기능들은 어떤 대상을 기준\(이하 targetId\)으로 인터넷 주소\(IP\)나 회원 아이디로 구분하여 처리합니다.

카운터 패키지는 IP와 회원 아이디를 기준으로하는 두가지 방법에 대해서 수를 셀 수 있는 기능을 제공합니다.

### 카운터 인스턴스

카운터를 사용하기 위해서는 카운터 인스턴스를 획득해야 합니다. 수를 셀 때 두가지 방식을 제공합니다. 그 첫번째는 게시물 조회수를 처리할 때 처럼 targetId에 대해서 IP, 회원 아이디를 기준으로 처리하면 되는 방식입니다. 이 때 조회수를 처리하기 위해 카운터 인스턴스를 획득하는 코드는 아래와 같습니다.

```php
// 문서 조회에 사용할 카운터 반환
$readCounter = XeCounter::make($request, 'read');
```

두번째 방식은 투표수를 셀 때 사용하는 방식입니다. 투표는 targetId에 대해서 몇개의 옵션을 설정하고 각 옵션별 수를 셉니다. 그리고 IP, 회원 아이디는 targetId에 한번만 처리 되어야 합니다. 이러한 카운터의 인스턴스를 획득하는 코드는 아래와 같습니다.

```php
// 투표(찬성, 반대)에 사용할 카운터 반환
$voteCounter = XeCounter::make($request, 'vote', ['assent', dissent']);
```

세번째 인자 `['assent', dissent']`를 추가해서 사용가능합니다.

`XeCounter::make()` 로 반환된 카운터 인스턴스는 Interception Proxy 인스턴스로 intercept 할 수 있습니다.

#### 조회수 증가

```php
$doc = Document::find('id');

$readCounter = XeCounter::make($request, 'read');
// Counter는 $user 가 Guest라면 IP를 기준으로 수를 셉니다.
$readCounter->setGuest();

$user = Auth::user();
if ($readCounter->has($doc->id, $user) === true) {
  $readCounter->add($doc->id, $user);
}

$doc->readCount = $readCounter->getPoint($doc->id);
$doc->save();
```

> 조회수를 적용하기 위해서는 이와 같이 조회수를 카운터가 전달한 조회수를 문서에 직접 입력해야 합니다.

#### 투표 취소

```php
$doc = Document::find('id');

// 투표는 회원에 대해서만 동작합니다.
$voteCounter = XeCounter::make($request, 'vote', ['assent', 'dissent']);

// 인스턴스에 설정된 두개의 옵션 중에서 'assent' 에 투표
$option = 'assent';

$user = Auth::user();

// 회원에 대해서 동작하기 때문에 로그인하지 않은 회원인 경우 GuestNotSupportException 발생
try {
    $voteCounter->remove($doc, $user, $option);
} catch (GuestNotSupportException $e) {
    throw new AccessDeniedHttpException;
}

$doc->assentCount = $readCounter->getPoint($doc->id, 'assent');
$doc->save();
```

#### 투표 로그

투표에 참여한 사용자 로그를 확인합니다.

```php
$doc = Document::find('id');

// 투표는 회원에 대해서만 동작합니다.
$voteCounter = XeCounter::make($request, 'vote', ['assent', 'dissent']);

// 찬선에 투표한 회원 목록을 가져옵니다.
$users = $voteCounter->getUsers($doc->id, 'assent');
```

