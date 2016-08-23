# 문서(document)

사이트에서 생산되는 게시판, 블로그, 댓글등 다양한 형태의 컨텐츠를 저장할 저장소를 제공합니다. 
DocumentHandler 는 Document, Revision 모델을 이용해서 컨텐츠 변경이력을 관리할 수 있는 기능을 제공하고 저장소의 부하분산을 위한 Division(저장소 분할) 저장소를 제공하합니다. 이러한 기능은 instanceId 에 따라 설정을 추가해서 관리할 수 있습니다.

또한 Document 모델은 DynamicField 를 지원합니다.

DocumentHandler, InstanceManager, ConfigHandler 는 Interception Proxy로 만들어 사용되므로 인터셉트 할 수 있습니다. (인터셉트하기)

## 기본 사용법
`XeDocument`파사드로 DocumentHandler 를 사용합니다. 문서 등록, 수정, 삭제는 DocumentHandler를 이용해서 처리하고 문서 조회는 Document 모델을 직적 사용합니다. 
단, 인스턴스를 생성해 사용할 경우 Document 모델에 대한 설정을 모델에 포함시키기 위해  `XeDocument::getModel($instnaceId)` 를 사용하기를 권장합니다.

#### 모든 문서 가져오기
```php
$doc = Document::all();
```

#### Primary Key를 통해서 하나의 레코드 가져오기
```php
$doc = Document::find('073eb138-30ea-4f2b-bd22-cc7709f59e76');
var_dump($doc->content);
```

#### 인스턴스가 등록된 문서 가져오기
메뉴를 통해 인스턴스가 생성되었거나 또는 어떤 방식으로든 `InstanceManager` 를 이용해 인스턴스를 만들어 설정을 사용하는 경우는 모델을 직접사용하지 않고 `DocumentHandler`를 이용해 모델을 획득해서 사용하도록 행합니다.

```php
$model = XeDocument::getModel($instanceId);
```
이렇게 획득한 모델은 설정에 대한 정보를 포함하고 있으며 `revision`, `division` 에 대한 처리 및 Database Proxy를 이용하는 기능인 DynamiField 에 대한 처리가 가능합니다.


#### 문서 등록
문서의 등록은 Document

#### DocumentHandler

* 문서 등록, 수정, 삭제 할 때 intercept 할 수 있음 Document Model 자체적으로 CRUD 할 때 intercept를 제공할 수 없는 문제 해결
* Division 처리 및 DyanmciQuery 관련 작업을 위해 Document Model에 config를 설정해야하고 이 설정을 위해 getModel() setModelConfig() 메소드가 있음
* Division(테이블 분할)은 Document Model에서 처리
* Document Model을 이용해 Database CRUD 처리를 직접하는 경우, revision에 대한 처리를 할 수 없으며 intercept 할 수 없음

#### app binding

* xe.document 로 바인딩 되어 있음
* Document 파사드 제공

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