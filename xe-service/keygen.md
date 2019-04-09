# 키생성\(Keygen\)

## 키생성\(keygen\)

XE는 단순하고 유추하기 쉬운 기존의 데이터베이스를 이용한 `auto_increment`를 벗어나, 유일성을 보장하면서 유추하기 어려운 키를 생성할 수 있는 기능을 제공합니다. 키의 생성은 UUID 방식으로 `ramsey/uuid`를 이용해 사용자가 간편하게 고유키를 얻을 수 있도록 해줍니다.

### 설정

키생성기의 설정은 `config/xe.php`의 `uid` 항목에 있습니다.

```php
'uid' => [
  'version' => 4,
  'namespace' => 'xe'
]
```

`version`은 사용할 UUID의 버전을 말합니다. `1, 3, 4, 5` 네가지 값 중 하나를 입력할 수 있고, 기본은 `4` 버전을 사용합니다. `namespace`는 `3, 5` 버전에서 사용될 네임스페이스를 가리킵니다.

> 각 버전의 차이는 [위키](https://en.wikipedia.org/wiki/Universally_unique_identifier)등에서 확인하실 수 있습니다.

### 사용

키의 생성은 `generate` 메서드를 통해 이루어 집니다.

```php
$keygen = app('xe.keygen');
$id = $keygen->generate();
```

생성된 키는 `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` 와 같은 형식을 가집니다.

### 모델

XE에서는 `Illuminate\Database\Eloquent\Model`을 상속한 `DynamicModel` 모델을 이용하는 경우 키생성기를 통해 고유키를 제공합니다. 키생성기에서 제공하는 키를 사용하고자 한다면 해당 모델의 `incrementnig`을 비활성해야 합니다.

```php
use Xpressengine\Database\Eloquent\DynamicModel;

class MyModel extends DynamicModel
{
  public $incrementing = false;

  ...
}
```

