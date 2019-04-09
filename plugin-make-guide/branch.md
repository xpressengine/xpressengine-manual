# 버전 관리

플러그인은 처음 설치될 때, 필요에 따라 플러그인에서 사용할 데이터베이스 테이블을 생성하거나, 설정을 저장하기도 하고, 필요한 파일을 미리 생성해 놓기도 합니다. 이런 작업들은 플러그인이 실행될 때마다 매번 필요한 작업이 아니라 처음 설치될 때 단 한번만 실행돼야 하는 작업입니다. 플러그인이 업데이트 되었을 경우에도 마찬가지입니다. 플러그인에 새로운 기능이 플러그인에 추가되었다면 데이터베이스 테이블을 변경해야 할 수도 있습니다.

이렇게 플러그인이 설치되고 업데이트될 때, 또는 플러그인이 삭제될 때 실행되어야 하는 코드는 `plugin.php` 파일의 플러그인 클래스에 작성하십시오.

## 플러그인 설치 과정

플러그인 클래스는 설치와 관련된 두개의 메소드를 가지고 있습니다. `checkInstalled`와 `install` 메소드입니다.

```php
<?php
namespace MyPlugin;

use Xpressengine\Plugin\AbstractPlugin;
use Schema;

class Plugin extends AbstractPlugin
{
  public function checkInstalled($installedVersion = null)
  {
      // 플러그인이 설치된 상태인지 체크하는 코드를 작성합니다.
  }

  public function install()
  {
      // 플러그인이 설치될 때 필요한 코드를 작성합니다.
  }

}
```

`checkInstalled` 메소드는 플러그인이 활성화될 때마다 호출됩니다. 만약 이 메소드가 `true`를 반환하면 XE는 이 플러그인이 이미 XE에 설치된 상태로 간주하고 곧바로 플러그인을 활성화시킵니다. 반대로 이 메소드가 `false`를 반환하면 XE는 이 플러그인이 아직 XE에 설치가 안 된 상태라고 판단합니다.

만약 필요한 테이블이 생성되어 있는지 검사하고 싶다면 아래와 같이 작성하면 됩니다.

```php
public function checkInstalled($installedVersion = null)
{
    // 테이블이 존재하는지 검사, 없으면 false를 반환
    return Schema::hasTable('table_name');
}
```

XE는 `checkInstalled` 메소드의 리턴 값이 `false`일 경우, `install` 메소드를 호출합니다.

```php
public function install()
{
    // 플러그인이 설치될 때 필요한 코드를 작성합니다.
    Schema::create('table_name', function ($table) {
      $table->engine = 'InnoDB';
      $table->increments('id');
      $table->string('name', 200);
    });
}
```

## 플러그인 업데이트 과정

플러그인 클래스는 업데이트와 관련된 두개의 메소드를 가지고 있습니다. `checkUpdated`와 `update` 메소드입니다. 두 메소드는 앞서 설명한 `checkInstalled`와 `install` 메소드와 비슷한 작동과정을 가집니다.

```php
<?php
namespace MyPlugin;

use Xpressengine\Plugin\AbstractPlugin;
use Schema;

class Plugin extends AbstractPlugin
{
  public function checkUpdated($installedVersion = null)
  {
      // 최신버전이 적용된 상태인지 체크합니다.
  }

  public function update()
  {
      // 플러그인의 최신버전을 적용하기 위한 코드를 작성합니다.
  }

}
```

`checkUpdated` 메소드는 현재 XE에 적용된 플러그인의 버전을 파라메터로 받습니다.

