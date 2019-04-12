# 파일/스토리지

## 파일/스토리지\(file,storage\)

XE의 스토리지는 라라벨이 제공하는 파일 시스템을 기반으로 사용자의 파일을 관리할 수 있는 기능을 제공합니다. 또한 분산저장기능을 갖추어 필요에 따라 여러개의 파일 저장소에 파일들을 나누어 저장할 수 있습니다.

### 설정

스토리지와 관련된 설정은 `config/filesystems.php`에 있습니다. 기본으로는 `local`드라이버가 지정되어 소스코드가 존재하는 위치의 `storage` 디렉토리에 파일이 저장됩니다.

클라우드 스토리지를 파일 저장소로 사용하고자 하는 경우에는 별도의 패키지가 설치되어야 합니다. S3나 Rackspace를 사용하는 경우 아래의 패키지가 컴포저를 통해 설치되어야 합니다.

* Amazon S3: league/flysystem-aws-s3-v3 ~1.0
* Racspace: league/flysystem-rackspace ~1.0

#### 분산저장

분산저장 기능을 사용하기 위해서는 설정을 변경해야 합니다. 설정파일의 `division` 항목에 `enable` 값을 `true`로 설정하고, 파일을 저장할 파일 저장소명을 작성해야 합니다.

```php
'division' => [
  'enable' => true,
  'disk' => ['local', 's3', 'rackspace']
]
```

설정이 변경되면 스토리지 패키지는 파일이 업로드 될때마다 자동으로 각 파일 저장소로 분산하여 저장하게 됩니다.

> 분산저장의 대상이 되는 파일저장소는 분산저장을 설정하기 이전에 사용가능한 값으로 설정되어야 합니다.

### 사용

#### 업로드

파일을 서버로 업로드하고자 할 때는 간단하게 `upload` 메서드만 호출하면 됩니다.

```php
use XeStorage;
use Xpressengine\Http\Request;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
  public function uploadFile(Request $request)
  {
    XeStorage::upload($request->file('attached'), 'path/to/dir');
  }
}
```

저장하는 파일이 서버에서 특별한 이름을 가지게 하고 싶은 경우 이름을 지정할 수 있습니다.

```php
XeStorage::upload($request->file('attached'), 'path/to/dir', 'custom_file_name');
```

그리고 만약 특정 파일저장소에 파일을 저장하고자 한다면 해당 파일저장소를 지정할 수 있습니다.

```php
XeStorage::upload($request->file('attached'), 'path/to/dir', null, 's3');
```

단순 업로드를 통한 저장이 아닌 편집등을 거쳐 생성된 결과물을 파일로 저장하려는 경우 `create` 메서드를 사용하면 됩니다.

```php
$content = file_get_content('path/to/file');
$content = /* 편집처리 (이미지 섬네일 생성등과 같은) */;
XeStorage::create($content, 'path/to/dir', 'somefile');
```

#### 다운로드

파일 객체를 `download` 메서드를 통해 전달하세요.

```php
$file = XeStorage::find($id);
XeStorage::download($file);
```

### 관계와 재사용

XE 스토리지는 파일과 파일을 이용하는 대상과의 관계를 필요로 합니다. 그래서 업로드 후 관계를 맺어주는 작업을 수행해야 합니다.

```php
$file = XeStorage::upload($request->file('attached'), 'path/to/dir');
XeStorage::bind($targetId, $file);
```

그리고 이미 업로드되어진 파일은 다른 대상과도 관계를 맺을 수 있습니다.

```php
$file = XeStorage::find($id);
XeStorage::bind($otherTargetId, $file);
```

만약 파일을 사용하던 대상이 더 이상 해당 파일을 사용하지 않게 된 경우 관계를 끊을 수 있습니다.

```php
$file = XeStorage::find($id);
XeStorage::unBind($targetId, $file);
```

이때 어떤 대상과도 연결되어지지 않게 될 경우 해당 파일을 삭제할 수 있습니다.

```php
XeStorage::unBind($targetId, $file, true);
```

여러개의 파일을 동시에 처리하는 경우 `sync` 메서드를 사용하면 파일을 사용하는 대상과 파일들의 관계를 간편하게 정의할 수 있습니다.

```php
$fileIds = [];
foreach ($uploadedFiles as $uploadedFile) {
  $file = XeStorage::upload($request->file('attached'), 'path/to/dir');
  $fileIds[] = $file->id;
}
XeStorage::sync($targetId, $fileIds);
```

위와 같이 관계가 형성되면 대상의 아이디로 대상에 연결된 파일들을 조회하거나 관계를 끊을 수 있습니다.

```php
// 대상의 파일 목록 조회
$files = XeStorage::fetchByFileable($targetId);

// 대상의 삭제등의 처리시 파일들의 연결해제 처리
XeStorage::unBindAll($targetId);
```

