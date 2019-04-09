# 문서

## 문서\(document\)

사이트에서 생산되는 게시판, 블로그, 댓글등 다양한 형태의 컨텐츠를 저장할 저장소를 제공합니다. DocumentHandler 는 Document, Revision 모델을 이용해서 컨텐츠 변경이력을 관리할 수 있는 기능을 제공하고 저장소의 부하분산을 위한 Division\(저장소 분할\) 저장소를 제공합니다. 이러한 기능은 instanceId 에 따라 설정을 추가해서 관리할 수 있습니다.

또한 Document 모델은 DynamicField 를 지원합니다.

DocumentHandler, InstanceManager, ConfigHandler 는 Interception Proxy로 만들어 사용되므로 인터셉트 할 수 있습니다. \(인터셉트하기\)

### 기본 사용법

`XeDocument`파사드로 DocumentHandler 를 사용합니다. 문서 등록, 수정, 삭제는 DocumentHandler를 이용해서 처리하고 문서 조회는 Document 모델을 직접 사용합니다. 단, 인스턴스를 생성해 사용할 경우 Document 모델에 대한 설정을 모델에 포함시키기 위해 `XeDocument::getModel($instnaceId)` 를 사용하기를 권장합니다.

#### 모든 문서 가져오기

```php
$doc = Document::all();
```

#### Primary Key를 통해서 하나의 레코드 가져오기

```php
$doc = Document::find($documentId);
var_dump($doc->content);
```

#### 인스턴스 생성을 통해 등록된 문서 가져오기

메뉴를 통해 인스턴스가 생성되었거나 또는 어떤 방식으로든 `InstanceManager` 를 이용해 인스턴스를 만들어 설정을 사용하는 경우에는 모델을 직접 사용하지 말고 `DocumentHandler`를 통해 획득한 모델을 사용하도록 해야합니다.

```php
$model = XeDocument::getModel($instanceId);
// 10개의 문서를 가져옵니다.
$model->paginate(10);
```

이렇게 획득한 모델은 설정에 대한 정보를 포함하고 있으며 `revision`, `division` 에 대한 처리 및 Database Proxy를 이용하는 기능인 DynamiField 에 대한 처리가 가능합니다.

#### 문서 등록

```php
$params['instanceId'] = $instanceId;
$params['title'] = '제목';
$params['content'] = '내용';
$inputs['userId'] = Auth::user()->getId();
$inputs['writer'] = Auth::user()->getDisplayName();

XeDocument::add($params);
```

문서 등록은 Document 모델을 직접 사용하지 않고 DocumentHandler 를 통해서 처리합니다. 이는 문서 저장할 때 인터셉트할 수 있는 포인트를 제공하며 Document 가 입력값 검사 및 `revision` 을 처리합니다.

#### 수정하기

Document를 통해 문서를 직접가져오는 경우는 아래와 같이 `XeDocument::setModelConfig()`를 이용해서 모델에 설정을 삽입해야 `DocumentHandler` 에서 설정에 따른 처리를 이상없이 수행할 수 있습니다.

```php
$doc = Document::find($documentId);
$doc->title = '제목 수정';

// Document 모델($doc)에 설정 삽입
XeDocument::setModelConfig($doc, $doc->instanceId);
XeDocument::put($doc);
```

일반적인 상태에서 문서를 찾을 때 라이프 사이클 상에서 instanceId 를 확보할 수 있다고 가정한다면 아래 형식의 코드를 사용할 수 있습니다.

```php
$doc = XeDocument::getModel($instanceId)->find($documentId);
$doc->title = '제목 수정';

XeDocument::put($doc);
```

#### 삭제하기

```php
$doc = Document::find($documentId);

// Document 모델($doc)에 설정 삽입
XeDocument::setModelConfig($doc, $doc->instanceId);
XeDocument::remove($doc);
```

```php
// '937c2ec7' 은 인스턴스 아이디
$doc = XeDocument::getModel($instanceId)->find($documentId);
XeDocument::remove($doc);
```

### 인스턴스

관리자 &gt; 사이트 메뉴에서 게시판을 만들 때 게시판 플러그인은 Document 서비스 를 사용하기 위해 Document 인스턴스를 만듭니다. Document 를 사용하기 위한 설정 및 그에 필요한 주변 요소를 생성해서 실제 사용가능한 형태로 만드는 것입니다.

#### 인스턴스 생성

```php
$params['revision'] = false;
$params['division'] = true;
XeDocument::createInstance($instanceId, $params);
```

Document 에 설정 요소를 `$params`로 전달합니다.

#### 인스턴스 제거

```php
XeDocument::destroyInstance($instanceId);
```

생성된 인스턴스를 제거하며 인스턴스 아이디로 저장된 문서를 삭제합니다. 이것은 게시판 삭제와 같은 처리를 할 때 사용됩니다.

### Document 모델

데이터를 저장할 때 사용하는 이것은 DynamicQuery 를 처리하기 위해 Config를 등록하는 기능과 문서를 처리할 때 유용한 다양한 메소드를 지원합니다.

#### 테이블 부하분산

`Document::setConfig()` 하는 과정에서 테이블 부하분산 처리를 위해 실제 사용할 테이블 이름을 변견할 수 있도록 지원합니다. 이 기능은 `DocumentHandler::setModelConfig()` 할 때 처리됩니다.

#### 다양한 상태값

상태를 표시하기 위한 다양한 컬럼을 갖고 있습니다.

* status : 문서의 상태를 저장합니다. \(휴지통, 임시저장, 비공개, 공개, 공지\)
* approved : 승인에 대한 설정을 저장합니다. \(거절됨, 기다림, 허용됨\)
* published : 발행에 대한 설정을 저장합니다. \(거절됨, 기다림, 예약발행, 발행됨\)
* display : 보여지는 상태에 대한 설정을 저장합니다. \(숨김, 비밀, 보여짐\)

이 상태값들을 복합적으로 사용하여 플러그인 자체적으로 다양한 방식의 상태를 표현할 수 있습니다. 그 중에 일반적으로 사용되는 `보여짐`, `숨김`, `발행` 등 다양한 형태의 상태값들을 제안하기 위한 메소드들이 추가되어 있습니다.

각 설정은 숫자로 정의되어 있으며 between 쿼리를 이용해서 더 다양한 설정을 사용할 수 있기를 바라고 있습니다. 서드파티 플러그인에서 Document 모델을 확장하는 `ExtendDocument` 모델을 만들고 상태를 추가하여 더 다양한 형태의 문서 상태 처리 방법이 제공될 수 있기를 바랍니다.

#### format

문서를 출력할 때 어떤 형태의 포멧으로 처리해야할 지 알려주기 위해서 글 저장할 때 format 을 등록하도록 하고 있습니다. 이것은 스마트 에디터를 사용하거나 혹은 마크다운으로 작성된 문서를 구분하고 저장된 내용을 출력할 때 어떤 방식으로 처리해야할 지 결정할 때 유용하게 사용될 것입니다. 현재의 포멧은 HTML 형식만 정의하고 있습니다.

#### reply

```php
// 상위 글
$doc = XeDocument::getModel($instanceId)->find($documentId);

// parentId 등록
$params['parentId'] = $doc->id;
$params['instanceId'] = '937c2ec7';
$params['title'] = '제목';
$params['content'] = '내용';
$inputs['userId'] = Auth::user()->getId();
$inputs['writer'] = Auth::user()->getDisplayName();

XeDocument::add($params);
```

답글 처리를 위해 `Document::setReply()`를 제공합니다. 어떤 문서의 하위로 글을 작성하려고 한다면 해당 모델에 `parentId`를 놓고 저장하면 됩니다. `Document::setReply()`의 실행은 `/app/Providers/DocumentServiceProvider.php` 의 `boot()`에서 `Illuminate\Database\Eloquent\Model` 의 `creating` 이벤트 리스너를 등록해서 자동으로 처리됩니다.

#### Division 지원

테이블 분리 기능을 처리하기 위해 Document 모델은 문서 등록, 수정, 삭제할 때 설정 값을 확인하고 추가적인 처리를 진행합니다. `Illuminate\Database\Eloquent\Model` 에서 제공하는 `performInsert()`, `performUpdate()`, `performDeleteOnModel()` 기능을 이용해 데이터베이스에 처리될 때 추가적인 코드를 처리합니다.

#### 관계

Document 모델은 User\(회원\)에 대한 관계만 제공합니다. 더 많은 릴레이션은 제공하지 않습니다. 더 많은 모델의 릴레이션을 사용하기 위해서는 게시판 플러그인의 Board 모델과 같이 Document 모델을 확장해서 사용하는것을 권장합니다.

