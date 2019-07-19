# 회원가입 입력폼 추가

XE는 사용자로부터 별도의 정보를 입력받기 위한 입력 폼을 추가할 수 있도록 구조화되어 있기 때문에, 서드파티 플러그인은 회원가입시 기본적으로 요구하는 정보 이외에 별도의 졍보를 사용자로부터 입력받을 수도 있습니다.

## 회원정보 입력 페이지

XE3에서는 회원가입시 회원정보를 입력할 수 있는 필드를 추가할 수 있습니다. 사이트 관리자는 사용할 출력할 입력폼을 '사이트 관리 &gt; 회원 &gt; 설정 &gt; 가입 설정'에서 선택할 수 있고, 순서를 지정할 수도 있습니다.

XE3는 회원정보 입력 페이지에 기본적으로 회원정보를 입력받는 폼과 약관의 동의를 받는 폼등을 제공합니다. 플러그인은 기본적으로 제공하는 폼 이외에도 별도의 입력 폼\(또는 UI\)를 추가할 수 있습니다. 이메일 인증 기능에서는 이메일 인증 코드를 입력받는 폼을 추가할 수 있고, 캡차 기능에서는 캡차 UI를 추가할 수 있습니다.

### 회원정보 입력 필드 추가하기

회원정보를 입력하는 필드를 추가하기 위해선 다음과 같은 코드가 작성되어야 합니다.

```php
use Xpressengine\User\Parts\DefaultPart;
use Xpressengine\User\Parts\AgreementPart;

UserHandler::addRegisterPart(DefaultPart::class);
UserHandler::addRegisterPart(AgreementPart::class);
```

위 코드에서는 기본정보를 입력하는 항목과 약관의 동의를 받는 항목을 등록하였습니다. 추가하는 각 영역은 클래스로 작성되어야 하며 `Xpressengine\User\Parts\RegisterFormPart` 클래스를 상속받아야 합니다.

기본정보를 입력받는 항목의 클래스는 다음과 같이 작성되어 있습니다.

```php
use Illuminate\Support\Arr;

class DefaultPart extends RegisterFormPart
{
    const ID = 'default-info';

    const NAME = 'xe::defaultInfo';

    const DESCRIPTION = 'xe::descDefaultInfo';

    protected static $implicit = true;

    protected static $view = 'register.forms.default';

    protected function data()
    {
        $passwordConfig = $this->service('config')->get('xe.user.password');
        $passwordLevel = Arr::get($passwordConfig['levels'], $passwordConfig['default']);

        return compact('passwordLevel');
    }

    public function rules()
    {
        return [
            'email' => 'required|email',
            'display_name' => 'required',
            'password' => 'required|confirmed|password',
        ];
    }
}
```

* 상수 `ID`는 각 항목들을 관리하기 위한 고유 키워드입니다.
* 상수 `NAME` 과 `DESCRIPTION` 은 설정페이지에서 표시되기 위한 텍스트입니다. 다국어 형태로 작성하는것을 권장합니다.
* `$implicit` 는 `true`로 설정하는 경우 on/off 할 수 없고 가입시 항상 표시됩니다.
* `$view`는 UI가 구현된 파일을 지정합니다. 기본적으로 스킨에 종속되어 사용되며 지정된 스킨의 키는 `user/auth` 입니다.
* `data()` 메서드에는 `$view`에 지정된 UI에서 사용되어야 할 데이터를 반환합니다.
* `rules()` 메서드는 유효성검사 항목들을 반환합니다.

등록 직후에는 off 상태이므로 설정페이지로 이동하여 on 상태로 변경하여야 정보를 입력받을 수 있습니다.

## 회원정보 저장 과정

사용자가 회원정보 입력 페이지에서 정보를 입력하고 가입 버튼을 누르면 서버에서는 회원 가입 처리를 합니다. 이 과정에서 플러그인은 사용자가 정확하게 입력을 했는지, 정상적인 인증을 거친 사용자인지 확인하게 됩니다.

기본적으로 앞서 작성된 입력항목 클래스의 `rules()` 메서드에 작성된 룰을 기반으로 유효성검사를 수행하게 됩니다. 만약 특별한 형식의 유효성검사가 필요하다면 `validate()` 메서드를 구현하여 해결할 수 있습니다.

