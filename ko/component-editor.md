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


### Frontend
#### XEeditor
프론트엔드에서는 기본적으로 에디터 Core가 있고 인터페이스 구현을 통해 에디터기능을 추가할 수 있습니다.
에디터 Core의 정의는 `xpressengine/assets/core/common/js/xe.editor.core.js`에서 확인할 수 있습니다. 

##### XEeditor.define(object)
xe.editor.core.js에서는 에디터의 인터페이스가 존재하며 에디터를 정의하기 위해 아래의 형식과 같이 필수적으로 구현해야하는 인터페이스들이 존재합니다.
xe.editor.core.js에서는 에디터의 인터페이스가 존재하며 에디터를 정의하기 위해 아래의 형식과 같이 필수적으로 구현해야하는 인터페이스들이 존재합니다