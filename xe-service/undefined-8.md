# 권한

## 권한\(permission\)

XE는 관리자 시스템을 통한 동적인 권한검사를 위해 laravel 의 [Authorization](https://laravel.com/docs/5.1/authorization) 기능을 확장하여 제공하고 있습니다.

### 권한 검사 유형

permission 에서는 권한을 검사하기 위해 5가지 유형의 데이터를 가지고 있습니다.

* `RATING_TYPE`: 사용자의 등급을 나타냅니다. 등급은 `GUEST`, `MEMBER`, `MANAGER`, `SUPER` 로 구분합니다.
* `GROUP_TYPE`: 권한을 가지는 그룹들을 지정합니다.
* `USER_TYPE`: 권한을 가지는 특정 사용자들을 지정합니다.
* `EXCEPT_TYPE`: 권한을 가지지 않는 특정 사용자들을 지정합니다.
* `VGROUP_TYPE`: 권한을 가지는 가상 그룹을 지정합니다.

위 다섯가지 권한중 `EXCEPT_TYPE` 이 가장 우선됩니다. 사용자가 `EXCEPT_TYPE` 에 속하지 않는경우 나머지 중 단 하나의 유형에만 속하여도 권한을 가지는 것으로 판단합니다.

### 등록

권한을 등록할때는 `Grant` 객체를 이용합니다. `Grant` 객체에 검사하고자 하는 특정 행위를 나타내는 키워드와 각 유형에 맞는 값들을 지정하고, 권한을 구분하는 대상의 이름과 함께 등록하게 됩니다.

```php
$grant = new Grant();
$grant->set('show', Rating::MEMBER);
$grant->set('create', Grant::GROUP_TYPE, ['{groupId}']);
$grant->set('create', Grant::EXCEPT_TYPE, ['{userId}']);

app('xe.permission')->register('foo.bar', $grant);
```

하나의 행위에 해당하는 다양한 유형의 데이터를 배열을 이용하여 동시에 설정할 수 있습니다.

```php
$grant->set('create', [
  Grant::RATING_TYPE => Rating::MEMBER,
  Grant::GROUP_TYPE => ['{groupId}'],
  Grant::EXCEPT_TYPE => ['{userId}']
]);
```

### 폼

위와 같은 데이터를 브라우저를 통해 전달 받기위해 ui object 가 제공됩니다.

```markup
<div>
  {!! uio('permission', $arguments) !!}
</div>
```

이때 전달되는 인자는 배열로 다음과 같은 구조를 필요로 합니다.

```php
array(
  'mode' => 'manual', // manual or inherit
  'grant' => $grant_object? : new Grant(),      // 특정 행위에 해당하는 Grant 객체 등록 값
  'title' => 'create' // 특정 행위를 나타내는 키워드
  'groups' => []      // 전체 그룹
);
```

ui object 는 지정된 행위를 나타내는 키워드와 유형을 조합하여 input 의 이름을 제공합니다. 예를 들어 `create` 라는 행위에 대한 회원등급의 이름은 `createRating` 이 됩니다.

### 권한 등록을 위한 편의제공

관리자 페이지로부터 전달되는 권한 정보를 형식에 맞게 정제하고 등록하기에 불편함을 느낄 수 있습니다. 그래서 이런 부분에서 자유로워 질 수 있도록 클래스에 삽입되어지는 `PermissionSupport` trait 을 제공합니다.

```php
class SomeController
{
  use PermissionSupport;

  public function getSetting()
  {
    $args = $this->getPermArguments('foo.bar', ['create', 'show']);
    ...
  }

  public function postSetting(Request $request)
  {
    $this->permissionRegister($request, 'foo.bar', ['create', 'show']);
    ...
  }

  ...
}
```

### 권한검사

권한을 검사할때는 laravel 의 [Authorization](https://laravel.com/docs/5.1/authorization) 기능을 이용합니다. 첫번째 인자값에 행위에 대한 키워드를 전달하고 두번째 인자에 권한등록시 사용한 이름으로 생성된 `Instance` 객체를 전달합니다.

```php
if (Gate::denies('create', new Instance('foo.bar'))) {
  // throw exception
}
```

