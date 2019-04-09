# 서비스

## 소개

XE가 앞서 설명한 라이프사이클에 따라 요청을 처리하고 응답하는 과정을 수행하는 동안, 우리는 매우 다양한 작업을 실행해야 합니다. 때로는 데이터베이스에서 자료를 검색하거나 자료를 저장하기도 하고, 이메일을 전송하거나 세션이나 쿠키를 다뤄야 할 때도 있습니다. 개발자들이 이런 작업을 수행하는 코드를 모두 직접 구현한다면 매우 힘든 개발이 될 것입니다.

XE는 개발자들이 원하는 기능을 쉽게 구현할 수 있도록 많은 서비스를 제공합니다. 아래 목록은 라라벨이 기본으로 제공하는 프레임워크 레벨의 주요 서비스 목록입니다.

* Auth
* Cache
* Config
* Console
* Container
* Cookie
* Database
* Encryption
* Events
* Filesystem
* Hashing
* Log
* Mail
* Pagination
* Queue
* Redis
* Routing
* Session
* Translation
* Validation
* View

XE는 라라벨이 제공하는 위 서비스 이외에도 문서\(document\), 파일\(storage\), 회원\(user\) 관리와 같은 CMS 어플리케이션 레벨의 서비스를 많이 제공합니다. XE에서 제공하는 서비스은 아래 목록과 같습니다.

* Captcha
* Category
* Config
* Counter
* Database
* Document
* DynamicField
* Editor
* Frontend
* Interception
* Keygen
* Media
* Menu
* Module
* Permission
* Plugin
* Presenter
* Register
* Routing
* Seo
* Settings
* Site
* Skin
* Storage
* Tag
* Temporary
* Theme
* ToggleMenu
* Translation
* Trash
* UIObject
* User
* Widget

각각의 서비스가 제공하는 기능과 용도를 충분히 알고 있다면 훨씬 편하게 플러그인을 개발할 수 있습니다. 본 매뉴얼의 서비스 카테고리에서 각 서비스의 기능과 사용법을 자세히 안내하고 있습니다.

## 서비스 로드하기

### 파사드를 사용하여 로드하기

내 코드에서 필요한 서비스를 로드하는 방법으로 XE는 파사드를 제공합니다. 파사드는 어떤 서비스를 대신하는 프록시 역할하며, Static 클래스 형식으로 사용할 수 있습니다.

```php
// User 서비스 사용
$user = \XeUser::find($userId);
```

위 예제에서 `XeUser`는 파사드입니다. 이 파사드를 통해 User 서비스를 사용할 수 있습니다. 위 코드와 같이 파사드의 `find` 메소드를 호출하면 실제로는 `\Xpressengine\User\UserHandler` 클래스의 인스턴스의 `find` 메소드가 호출됩니다.

### 서비스컨테이너를 사용하여 로드하기

파사드를 사용하는 대신, 서비스 컨테이너로부터 직접 서비스를 로드할 수 있습니다. XE에서 제공하는 모든 서비스는 '서비스 컨테이너'에 등록됩니다. 내 코드에서 어떤 서비스를 로드해야할 때, 서비스 컨테이너에게 그 서비스를 달라고 요청하면, 서비스 컨테이너는 요청받은 서비스를 반환해 줍니다. 보통 `app()` 함수나 `App` 파사드를 사용하여 서비스 컨테이너로부터 필요한 서비스를 로드합니다.

```php
// User 서비스 로드
$userHandler = app('xe.user');

// or
$userHandler = \App::make('xe.user');

// User 서비스 사용
$user = $userHandler->find($userId);
```

보통 서비스는 특정 클래스의 싱글톤 인스턴스입니다. 그 클래스는 개발자가 서비스를 이용할 때 편하게 사용할 수 있는 메소드\(인터페이스\)를 가지고 있습니다.

사실, 서비스 컨테이너는 서비스 관리에 국한된 역할을 하는 것은 아닙니다. 정확히 말하면, 서비스 컨테이너는 다양한 클래스의 의존성을 관리하는 강력한 도구 입니다. 서비스 컨테이너에 대하여 더 알고싶다면, [이 문서](http://xpressengine.github.io/laravel-korean-docs/docs/5.0/container/)를 참고하세요.

