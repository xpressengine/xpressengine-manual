# 설정



## 설정\(config\)

XE에서 제공하는 config는 laravel에서 제공되는 config와는 다르게 데이터베이스에 정보를 담으며 계층을 가지고 상위 정보를 참조합니다. 이때 각 계층은 "."\(dot\) 으로 구분합니다.

### 등록

config 는 해당 설정을 나타내는 이름과 그에 매칭되는 배열을 통해 등록됩니다.

```php
XeConfig::add('foo', ['var1' => 'a', 'var2' => 'b']);
XeConfig::add('foo.bar', ['var1' => 'A']);
XeConfig::set('foo.baz', ['var2' => 'B']);
```

`add`는 새로운 설정정보를 등록하는 메서드입니다. 만약 같은 이름의 설정이 이미 등록되어져 있는 경우 Exception이 발생하게 됩니다.

`set`은 기존 설정의 존재 유무와 상관 없이 값을 등록합니다. `set`의 경우 기존에 같은 이름의 설정이 존재하는 경우 메서드 호출시 전달된 배열의 키에 해당하는 값만을 갱신해 줍니다.

```php
XeConfig::add('foo', ['var1' => 'a', 'var2' => 'b']);
XeConfig::set('foo', ['var1' => 'A']);
echo XeConfig::getVal('foo.var1'); // A
echo XeConfig::getVal('foo.var2'); // b
```

설정을 등록하는 방법은 `add`, `set` 이외에도 `put`, `modify`, `setVal`이 있습니다.

`put`은 기존에 같은 이름을 가지는 설정이 먼저 등록되어 있어야 정상적으로 동작합니다. 또한 `set` 과는 다르게 기존의 설정들을 메서드 호출시 전달된 배열 값으로 모두 대체합니다.

```php
XeConfig::add('foo', ['var1' => 'a', 'var2' => 'b']);
XeConfig::put('foo', ['var1' => 'A']);
echo XeConfig::getVal('foo.var1'); // A
echo XeConfig::getVal('foo.var2'); // null
```

`modify`는 config 객체를 통해 수정하는 기능입니다.

```php
$config = XeConfig::get('foo');
$config->set('var1', 'A');
XeConfig::modify($config);
```

`setVal`은 설정의 특정 항목의 값만 등록하는 메서드입니다.

```php
XeConfig::setVal('foo.var1', 'A');
```

### 조회

등록된 설정을 조회할때는 특정 설정키에 해당하는 값을 받거나, config 객체를 반환 받은후 값을 조회하는 방법이 있습니다.

```php
XeConfig::add('foo', ['var1' => 'a', 'var2' => 'b']);
echo XeConfig::getVal('foo.var1'); // a
// 또는
$config = XeConfig::get('foo');
echo $config->get('var1'); // a
```

만약 등록된 설정이 없는 경우 특정값을 반환 받고 싶으면 다음 인자에 값을 전달하면 됩니다.

```php
XeConfig::add('foo', ['var1' => 'a', 'var2' => 'b']);
echo XeConfig::getVal('foo.var3', 'c'); // c
// 또는
$config = XeConfig::get('foo');
echo $config->get('var3', 'c'); // c
```

### 계층조회

config는 계층을 가지고 있고 이를 이용하여 존재하지 않는 설정은 부모에 해당하는 설정을 조회하여 결과를 반환합니다.

```php
XeConfig::add('foo', ['var1' => 'a', 'var2' => 'b']);
XeConfig::add('foo.bar', ['var1' => 'A']);
echo XeConfig::getVal('foo.bar.var2'); // b
```

> 설정을 조회할때 두번째 인자로 default 값을 전달하는 경우 최상위 설정에서도 해당하는 결과가 없는 경우에 default 값이 반환되어 집니다.

