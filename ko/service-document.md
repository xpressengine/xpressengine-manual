# 문서(document)

#### 개요

Document 는 사이트에서 발생하는 컨텐츠를 저장할 저장소를 제공합니다. 컨텐츠 변경이력을 관리하기 위한 Revision 저장소와 컨텐츠 저장소 부하분산을 위한 Division(저장소 분할) 저장소를 제공합니다.

#### 목적

CMS 에서 사용되는 게시판, 블로그와 같은 컨텐츠를 다루는 플러그인이 사용하기 위한 컨텐츠 저장소를 제공합니다. 컨텐츠 변경 이력 관리자와 컨텐츠 저장소 분할을 Config 로 제어할 수 있습니다.


#### DocumentHandler
* 문서 등록, 수정, 삭제 할 때 intercept 할 수 있음 Document Model 자체적으로 CRUD 할 때 intercept 를 제공할 수 없는 문제 해결
* Division 처리 및 DyanmciQuery 관련 작업을 위해 Document Model 에 config 를 설정해야하고 이 설정을 위해 getModel() setModelConfig() 메소드가 있음
* Division(테이블 분할)은 Document Model 에서 처리
* Document Model 을 이용해 Database CRUD 처리를 직접하는 경우 revision 에 대한 처리를 할 수 없으며 intercept 할 수 없음

#### app binding
* xe.document 로 바인딩 되어 있음
* Document facade 제공
## 사용법

#### Instance 생성
```php
XeDocument::createInstance('newInstanceId');
```

#### 문서 등록
```php
$inputs = ['id'=>$id', 'instanceId'=>'instance-id', 'title'=>'title', 'content'=>'content' ...];
XeDocument::add($inputs);
```

#### 문서 수정
```php
$model = XeDocument::getModel('instance-id');
$doc = $model->find('document-id');

$doc->title = 'changed title';

XeDocument::update($doc);
```

#### 문서 삭제
```php
$model = XeDocument::getModel('instance-id');
$doc = $model->find('document-id');

XeDocument::remove($doc);
```

#### 문서 조회
```php
$model = XeDocument::getModel('instance-id');
$doc = $model->find('document-id');

$doc = XeDocument::get('document-id', 'instance-id');

// instance id 를 넘겨주지 않으면 항상 documents table 에서 조회
$doc = XeDocument::get('document-id');
```

#### 문서 수 조회
```php
// 전체 문서 수 조회회
$count = Document::count();

// 인스턴스의 전체 문서 수 조회
$model = XeDocument::getModel('instance-id');
$count = $model->count('instance-id');
```

#### 문서 목록 조회
```php
$perPage = 10;

$model = XeDocument::getModel('instance-id');

$model->paginate($perPage);
```