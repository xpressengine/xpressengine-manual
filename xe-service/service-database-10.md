# 데이터베이스

## 데이터베이스\(database\)

XE가 자체적으로 부하분산을 처리할 수 있을까? 어떻게 하면 회원과 문서의 데이터베이스 사용을 분리시킬 수 있을까?

이 고민에서 시작이었습니다. 이미 Laravel은 훌륭한 데이터베이스 처리 방식을 제안하고 있습니다. 다만 우리는 앞서 얘기한 고민을 해결하기 위해 새로운 데이터베이스를 만들기 보다 우리가 하고자 하는 일을 해결할 수 있는 기능을 추가한 전혀 새롭지 않은\(Laravel 의 데이터베이스와 같은\) 데이터베이스를 만들고 싶었습니다.

**기본적인 사용 방법은 Laravel 과 같으며 단지** `XeDB` **파사드를 사용하면 됩니다.**

> [기본적인 데이터베이스 사용](https://laravel.kr/docs/5.2/database), [쿼리빌더](https://laravel.kr/docs/5.2/queries), [마이그레이션](https://laravel.kr/docs/5.2/migrations) 의 내용을 숙지하시기 바랍니다.

### 설정

config/xe.php 의 `database`에 가상 연결 설정을 등록합니다.

```php
'database' => [
        'default' => [
            'slave' => ['default'],
        ],
        'document' => [
            'slave' => ['default'],
        ],
        'user' => [
            'slave' => ['default'],
        ],
    ],
```

XE에서는 회원과 문서의 데이터베이스를 분리해서 사용할 수 있도록 가상 연결을 분리해서 사용하고 있습니다. `app/Providers/` 의 UserServiceProvider.php, DocumentServiceProvider.php 에서 각각 `user`, `document` 커넥션을 사용하는 코드를 확인할 수 있습니다.

### 가상 연결

여러개의 논리적인 데이터베이스 연결을 사용하여 다중 커넥션 사용을 가능하도록 하고 서버에 트래픽이 증가할 경우 패키지별 데이터베이스 연결 설정을 변경하여 부하분산을 빠르게 처리할 수 있도록 합니다. 또한 하나의 논리적인 데이터베이스 연결에 여러개의 데이터베이스 연결 설정을 할 수 있도록 하고 Query 처리할 때 랜덤하게 커넥션을 사용하도록 구현하여 부하분산을 처리합니다.

또한 물리적으로 다른 여러개의 커넥션에 대해서 트랜젝션을 사용할 수 있습니다. 여러개의 커넥션에서 각각 발생하는 트랜젝션을 관리하여 하나의 커넥션에서 처리되는 것과 같이 동작합니다.

다이나믹 쿼리를 이용해서 ProxyManager 에 등록된 Proxy 들을 사용할 수 있습니다. 다이나믹 쿼리는 데이터베이스 CRUD 처리 시 발생하여 쿼리를 조작할 수 있도록 인터페이스를 제공합니다. ProxyInterface 의 인터페이스를 QueryBuilder 에서 각 메소드에서 필요한 인터페이스를 사용합니다.

### Proxy

데이터베이스 Proxy는 쿼리 실행 실행에 앞서 쿼리를 수정할 수 있도록 인터페이스를 지원합니다. `DynamicQuery`에서 `ProxyManager`를 사용하도록 해서 inster\(\), update\(\), delete\(\), get\(\), first\(\), find\(\), paginate\(\), simplePaginate\(\) 이 사용될 때 쿼리를 수정합니다. DynamicField 는 Proxy 기능을 이용해서 데이터베이스에 쿼리할 때 설정에 따라 추가된 확장 필드의 처리를 위한 쿼리를 실행하도록 구현했습니다.

#### Proxy 사용

```php
$users = XeDB::dynamic('user')->get();
```

Proxy를 사용하기 위해서 `table()` 이 아닌 `dynamic()` 메소드를 사용합니다. `dynamic()`을 사용해 반환된 `DynamicQuery` 인스턴스는 Proxy를 사용하기 위한 상태가 됩니다.

실제 데이터베이스 연결 설정은 기존의 Laravel 설정 방법과 동일합니다. config/database.php 에 데이터베이스 연결을 수정하거나 추가하면 됩니다.

### 트랜잭션

```php
XeDB::beginTransaction();
... query ...
XeDB::commit(); // or XeDB::rollback();
```

XE3 데이터베이스는 여러개의 연결에 대해서 트랜잭션을 처리합니다. 전체 커넥션 정보를 참고하고 있는 TransactionHandler 는 트랜잭션이 시작될 때 연결되어 있는 모든 가상연결에 트랙잭션을 시작하고 또한 새로 맺는 연결도 트랜잭션이 시작되도록 처리합니다.

가상연결로 처리되는 하나 이상의 데이터베이스 연결에 대해서 트랜젝션 을 처리합니다. DB1, DB2 에 insert 처리 할 때 물리적으로 다른 두개의 데이터베이스에 트랜젝션이 처리될 수 있도록 지원합니다.

