# 서비스

## 소개

XE가 앞서 설명한 라이프사이클에 따라 요청을 처리하고 응답하는 과정을 수행하는 동안, 우리는 매우 다양한 작업을 실행해야 합니다. 때로는 데이터베이스에서 자료를 검색하거나 자료를 저장하기도 하고, 이메일을 전송하거나 세션이나 쿠키를 다뤄야 할 때도 있습니다. 개발자들이 이런 작업을 수행하는 코드를 모두 직접 구현한다면 매우 힘든 개발이 될 것입니다.

XE는 개발자들이 원하는 기능을 쉽게 구현할 수 있도록 많은 서비스를 제공합니다. 아래 목록은 라라벨이 기본으로 제공하는 프레임워크 레벨의 주요 서비스 목록입니다.

- Auth
- Cache
- Config
- Console
- Container
- Cookie
- Database
- Encryption
- Events
- Filesystem
- Hashing
- Log
- Mail
- Pagination
- Queue
- Redis
- Routing
- Session
- Translation
- Validation
- View

XE는 라라벨이 제공하는 위 서비스 이외에도 문서(document), 댓글(comment), 파일(storage), 회원(user) 관리와 같은 CMS 어플리케이션 레벨의 서비스를 많이 제공합니다. XE에서 제공하는 서비스은 아래 목록과 같습니다.

- Captcha
- Category
- Config
- Counter
- Database
- Document
- DynamicField
- Editor
- Interception
- Keygen
- Media
- Menu
- Module
- Permission
- Plugin
- Presenter
- Register
- Routing
- Seo
- Settings
- Site
- Skin
- Storage
- Tag
- Temporary
- Theme
- ToggleMenu
- Translation
- Trash
- UIObject
- User
- Widget


각각의 서비스가 제공하는 기능과 용도를 충분히 알고 있다면 훨씬 편하게 플러그인을 개발할 수 있습니다.
본 매뉴얼의 서비스 카테고리에서 각 서비스의 기능과 사용법은 자세히 안내하고 있습니다.

## 서비스컨테이너

XE에서 제공하는 모든 서비스는 XE가 부팅되는 과정에서 '서비스 컨테이너'에 모두 등록됩니다. 
