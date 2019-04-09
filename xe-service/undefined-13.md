# 이미지처리

## 이미지처리\(Image, media\)

XE에서는 사용자가 등록하는 이미지와 같은 미디어 파일들을 제어하기위한 `Media` 패키지가 있습니다. `Media` 는 `Intervention/image` 에서 제공하는 기능을 이용하여 설정에서 정의된 내용에 의해 섬네일을 어떤 형태로 생성할지 결정하고 사이즈별로 생성해줍니다. 또한 이미지외에도 오디오, 비디오 파일들을 간편하게 표현해주기 위한 인터페이스를 제공합니다.

### 설정

미디어의 설정은 `config/xe.php` 파일의 `media` 항목에서 지정합니다.  
섬네일 설정은 다음과 같이 작성되어있습니다.

```php
  'thumbnail' => [
    'disk' => 'local',
    'path' => 'public/thumbnails',
    'type' => 'fit',
    'dimensions' => [
      'S' => ['width' => 200, 'height' => 200,],
      'M' => ['width' => 400, 'height' => 400,],
      'L' => ['width' => 800, 'height' => 800,],
    ],
  ],
```

* `disk`: 생성된 섬네일 이미지가 저장되어질 파일저장소를 지정합니다. 파일저장소는 `config/filesystems.php` 에서 정의합니다.
* `path`: 섬네일 이미지가 위치할 경로를 지정합니다.
* `type`: 어떤 형태로 섬네일 이미지를 생성할지 지정합니다. 지정할 수 있는 타입은 다음과 같습니다.

  > * `fit`: 지정된 사이즈에 꽉 차고, 넘치는 영역은 삭제
  > * `letter`: 지정된 사이즈안에 이미지가 모두 포함되면서 기존 비율과 같은 비율을 가지는 형태 
  > * `widen`: 가로 사이즈만을 기준으로 생성
  > * `heighten`: 세로 사이즈만을 기준으로 생성
  > * `stretch`: 비율을 무시하고 지정된 사이즈에 꽉 차게 생성
  > * `spill`: 지정된 사이즈에 꽉 차고, 넘치는 영역은 삭제하지 않는 형태

* `dimensions`: 생성될 섬네일 이미지의 가로, 세로 사이즈를 지정합니다.

`Media` 에서는 비디오 파일에서 이미지를 추출하기 위한 확장기능을 제공합니다.

```php
  'videoExtensionDefault' => 'dummy',
  'videoExtensions' => [
    'ffmpeg' => [
      'ffmpeg.binaries' => '/usr/local/bin/ffmpeg',
      'ffprobe.binaries' => '/usr/local/bin/ffprobe',
      'timeout' => 3600,
      'ffmpeg.threads' => 4,
    ]
  ],
  'videoSnapshotFromSec' => 10
```

기본으로 지정된 `dummy` 는 이미지추출 작업을 하지 않는 가상의 확장기능입니다. 비디오에서 이미지를 추출하도록 하고 싶다면 `ffmpeg` 로 변경하고 필요한 항목을 설정해야 합니다.  
`ffmpeg` 를 사용하기 위해선 서버에 [FFmpeg](https://ffmpeg.org/) 가 설치되어있어야 합니다. 그리고 컴포저를 통해 다음 패키지가 설치되어야 합니다.

* php-ffmpeg/php-ffmpeg ~0.5

> 비디오 확장기능은 현재 `ffmpeg` 만 지원합니다.

### 섬네일 생성

섬네일을 생성하기 위해서는 우선 `File` 객채를 미디어 객체로 변환해야 합니다. `is`메서드를 통해 특정파일이 미디어파일인지 확인하고, 미디어파일인 경우 섬네일을 생성하도록 합니다.

```php
use XeStorage;
use XeMedia;
use Xpressengine\Http\Request;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
  public function uploadFile(Request $request)
  {
    $file = XeStorage::upload($request->file('attached'), 'path/to/dir');
    if (XeMedia::is($file)) {
      $media = XeMedia::make($file);
      $thumbnails = XeMedia::createThumbnails($media);
    }
  }
}
```

특정페이지는 섬네일 이미지 생성시 별도의 형태로 하고자 하는 경우 직접 타입을 지정할 수 있습니다.

```php
$thumbnails = XeMedia::createThumbnails($media, 'widen');
```

기존에 생성된 섬네일 이미지는 이미지 핸들러를 통해 얻을 수 있습니다.

```php
$thumbnails = XeMedia::images()->getThumbnails($media);

// 특정 섬네일을 얻는 경우
$thumbnail = XeMedia::images()->getThumbnail($media, 'spill', 'S');
```

#### 편집기능 특별하게 사용하기

XE 에서 제공하는 이미지 편집 기능중에 `crop` 기능이 있습니다. `crop` 은 가로, 세로 사이즈뿐만 아니라 좌표값이 필요한 기능이므로 기본적인 섬네일 생성방식에서 제외되어 있습니다. 이 기능을 사용하기 위해서는 아래와 같이 조금 특별하게 코드가 작성되어야 합니다.

```php
use XeStorage;
use Xpressengine\Media\Commands\CropCommand;
use Xpressengine\Media\Coordinators\Position;
use Xpressengine\Media\Coordinators\Dimension;
use Xpressengine\Media\Thumbnailer;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
  public function editImage($id)
  {
    $image = XeMedia::images()->find($id);

    $cropCmd = new CropCommand();
    $cropCmd->setPosition(new Position(50, 100));    // 시작 좌표 설정
    $cropCmd->setDimension(new Dimension(300, 200)); // 시작 좌표로 부터 가로, 세로 사이즈 지정

    $thumbnailer = new Thumbnailer();
    $content = $thumbnailer->setOrigin($image->getContent())->addCommand($cropCmd)->generate();

    XeStorage::create($content, 'path/to/dir', 'crop_file_name');
  }
```

`Thumbnailer` 는 처리할 명령을 순서대로 입력받을 수 있습니다. 만약 `crop` 한 이미지를 `letter` 타입으로 크기를 변환하려고 한다면 다음과 같이 할 수 있습니다.

```php
$letterCmd = new LetterCommand();
$letterCmd->setDimension(new Dimension(100, 100));

$content = $thumbnailer->setOrigin($image->getContent())->addCommand($cropCmd)->addCommand($letterCmd)->generate();
```

### 타입별 접근

XE 에서는 3가지 미디어 타입을 분리하여 기능을 제공하고 있습니다. 각 타입의 객체를 획득하거나 기능을 사용하기 위해서는 해당 타입의 핸들러를 획득해야 합니다. 핸들러를 획득하기 위해서는 각 타입의 타입 키워드를 단수 또는 복수형으로 호출하는 방식을 이용해야 합니다.

```php
// image handler
XeMedia::image();
XeMedia::images();

// video handler
XeMedia::video();
XeMedia::videos();

// audio handler
XeMedia::audio();
XeMedia::audios();
```

`extend` 메서드를 이용하여 추가된 미디어 핸들러도 같은 방식으로 사용할 수 있습니다.

### 미디어 표현

미디어의 객체는 간편하게 화면에 표현할 수 있도록 인터페이스를 제공합니다. 이것은 사전에 정의된 html 태그를 반환합니다.

```markup
<div>
  {{ $image->render() }}
</div>
<div>
  {{ $audio->render() }}
</div>
<div>
  {{ $video->render() }}
</div>
```

기본으로 제공하는 html 태그를 사용하지 않길 원할수도 있습니다. 이땐 단순히 파일의 url 을 반환받을 수 있으므로 별도의 태그를 직접 작성하여 해결할 수 있습니다.

```markup
<object data="{{ $video->url() }}" width="300" height="200">
  <param name="autoplay" value="true">
  <param name="showcontrols" value="true">
</object>
```

