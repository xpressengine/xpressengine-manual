# 에디터

XE에서는 에디터를 추가하여 사용할 수 있고, 또한 에디터에서 사용할 도구를 추가할 수 있습니다.

### 에디터 생성
#### 컴포넌트 추가
에디터가 사용되기 위해선 `AbstractEditor` 를 상속받은 컴포넌트가 생성되어야 합니다. 그리고 정의된 추상 메서드를 구현하세요.
* `getName`: js 스크립트에서 정의된 에디터의 이름입니다.
* `htmlable`: 해당 에디터가 wisywig 에디터 인지를 반환합니다.
* `compileBody`: 작성된 내용이 에디터를 통해 출력되기전 출력을 위한 처리동작을 수행합니다.

#### 스크립트 load
에디터는 frontend 에서 동작하므로 js 파일을 필요로 합니다. 이때 해당 파일을 불러오기 위한 방법으로 다음과 같은 방식을 권장합니다.

##### 이벤트
에디터는 표현되기 전에 처리하기 위한 이벤트 시점이 등록되어 있습니다. 이 이벤트 시점에 에디터에서 사용하고자 하는 스크립트 파일을 불러올 수 있습니다.
```
app('event')->listen('xe.editor.render', function ($editor) {
  app('xe.frontend')->js('path/to/editor.js')->load();
  app('xe.frontend')->js('path/to/editor.define.js')->before(['path/to/editor.js', 'assets/core/common/js/xe.editor.core.js'])->load();
});
```

##### override
에디터는 `AbstractEditor` 의 `render` 메서드를 통해 표현됩니다. 에디터는 이 메서드를 구현하여 원하는 동작을 추가할 수 있습니다.
```
public function render()
{
  app('xe.frontend')->js('path/to/editor.js')->load();
  app('xe.frontend')->js('path/to/editor.define.js')->before(['path/to/editor.js', 'assets/core/common/js/xe.editor.core.js'])->load();
  
  return parent::render();
}
```

> 에디터를 정의한 스크립트 파일은 반드시 XE의 `xe.editor.core.js` 를 필요로 합니다. 그러므로 XE core 의 스크립트가 해당 에디터 스크립트보다 먼저 불러질 수 있도록 하여야 합니다.


### 에디터 도구 생성
#### 컴포넌트 추가
에디터 도구는 `AbstractTool` 을 상속받아 구현되어야 합니다. 그리고 다음 추상 메서드를 구현해야 합니다.
* `initAssets`: 도구가 동작하는데 필요로하는 파일들을 불러옵니다. (js, css)
* `getIcon`: 에디터 도구영역에 추가되기 위해 필요한 아이콘 파일입니다. 해상도에 따라 다른게 사용되기 위해 배열로 `normal`, `large` 두개의 파일 경로가 제공되어야 합니다.
* `compile`: 등록된 내용중 해당 도구로 작성된 영역을 표현하기 위한 동작을 수행합니다.




> frontend 관련 내용 추가요망
> @blueng