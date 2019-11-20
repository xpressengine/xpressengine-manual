# 에디터 제작 방법

## 에디터

XE에서는 에디터를 추가하여 사용할 수 있고, 또한 에디터에서 사용할 도구를 추가할 수 있습니다.

### 에디터 생성

#### 컴포넌트 추가

에디터가 사용되기 위해선 `AbstractEditor` 를 상속받은 컴포넌트가 생성되어야 합니다. 그리고 정의된 추상 메서드를 구현하세요.

* `getName`: js 스크립트에서 정의된 에디터의 이름입니다.
* `htmlable`: 해당 에디터가 wisywig 에디터 인지를 반환합니다.
* `compileBody`: 작성된 내용이 에디터를 통해 출력되기전 출력을 위한 처리동작을 수행합니다.

#### 스크립트 load

에디터는 frontend 에서 동작하므로 js 파일을 필요로 합니다. 이때 해당 파일을 불러오기 위한 방법으로 다음과 같은 방식을 권장합니다.

**이벤트**

에디터는 표현되기 전에 처리하기 위한 이벤트 시점이 등록되어 있습니다. 이 이벤트 시점에 에디터에서 사용하고자 하는 스크립트 파일을 불러올 수 있습니다.

```text
app('event')->listen('xe.editor.render', function ($editor) {
  app('xe.frontend')->js('path/to/editor.js')->load();
  app('xe.frontend')->js('path/to/editor.define.js')->before(['path/to/editor.js', 'assets/core/common/js/xe.editor.core.js'])->load();
});
```

**override**

에디터는 `AbstractEditor` 의 `render` 메서드를 통해 표현됩니다. 에디터는 이 메서드를 구현하여 원하는 동작을 추가할 수 있습니다.

```text
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

프론트엔드에서는 기본적으로 에디터 Core가 있고 인터페이스 구현을 통해 에디터기능을 정의할 수 있습니다. XEeditor에서는 구현되는 에디터들의 인터페이스를 정의하고 생성된 에디터 인스턴스들을 관리합니다. 에디터 Core의 코드는 `xpressengine/assets/core/common/js/xe.editor.core.js`에서 확인할 수 있습니다.

**XEeditor.define\(object\)**

xe.editor.core.js에서는 에디터의 인터페이스가 존재하며 에디터를 정의하기 위해 아래의 형식과 같이 XEeditor.define을 통해 아래의 형식들을 구현하여 에디터를 정의합니다.

```javascript
XEeditor.define({
  editorSettings: {
    name: "",
    configs: {},
  },
  interfaces: {
    initialize: function (selector, options) {},
    getContents: function () {},
    setContents: function (text) {},
    addContents: function (text) {},
    on: function (eventName, callback) {},
    reset: function () {},
  },
});
```

**editorSettings**

**name \(string\) 필수**

에디터의 명칭을 정의합니다. 추후 에디터 사용시 XEeditor.getEditor\('에디터명'\)으로 정의된 에디터를 가져올때 사용됩니다.

**configs\(object\)**

에디터 생성시 사용되는 공통 config들을 정의합니다.

**interfaces**

에디터 Core에서 정의된 인터페이스들로 일부 인터페이스들은 필수로 구현되어야 합니다.

**addProps\(object\)**

각 구현되는 인터페이스들의 함수 내부에서 사용가능하며 object로 데이터를 저장할 수 있습니다. 추가된 데이터는 this.props\['프로퍼티명'\]으로 사용 가능합니다.

**initialize\(selector, options\) 필수구현**

* selector \(string\)
* options  \(object\)

에디터 생성시 호출되며 selector와 configs에서 정의된 옵션을 인자로 받을 수 있습니다.

```javascript
//예시
initialize: function(selector, options) {
  var editor = CKEDITOR.replace(selector, options);

  //selector를 저장
  this.addProps({
    selector: selector
  });

  this.on("instanceReady", ...
}
```

**getContents 필수구현**

에디터의 컨텐츠를 리턴해주는 인터페이스로 필수 구현되어야 합니다.

```javascript
//예시
getContents: function() {
  return CKEDITOR.instances[this.props.selector].getData();
}
```

**setContents\(content\) 필수구현**

* content \(string\)

에디터의 컨텐츠를 입력하는 인터페이스로 필수 구현되어야 합니다.

```javascript
//예시
setContents: function(content) {
  CKEDITOR.instances[this.props.selector].setData(content);
}
```

**addContents\(content\) 필수구현**

* content \(string\)

에디터의 컨텐츠를 추가하는 인터페이스로 필수 구현되어야 합니다.

```javascript
//예시
addContents: function(content) {
  CKEDITOR.instances[this.props.selector].insertHtml(content);
}
```

**on\(eventName, callback\)**

* eventName \(string\)
* callback \(function\)

에디터의 이벤트를 정의하는 인터페이스입니다.

```javascript
//예시
on: function(eventName, callback) {
  CKEDITOR.instances[this.props.selector].on(eventName, callback);
}
```

**reset**

에디터를 초기화하는 인터페이스입니다.

```javascript
//예시
reset: function() {
  this.setContents("");
}
```

**addTools\(toolsMap, toolInfoList\)**

* toolsMap \(object\) 정의된 툴의 정보
* toolInfoList \(array\) 에디터가 create될때 4번째 인자로 받은 값 \(id, icon 정보 등\)

툴정보가 있을 경우 에디터 Core에서 등록된 툴정보들을 addTools인터페이스를 통해 전달합니다. 정의된 에디터에서는 addTools 인터페이스를 통해 넘겨 받은 tool정보로 툴들을 등록하는 코드를 구현해야 합니다.

```javascript
//예시
addTools: function(toolsMap, toolInfoList) {
  var editor = this.props.editor;
  var component = toolsMap[toolInfoList[0].id];
  var toolOption = component.props;

  toolOption.options.icon = toolInfoList[0].icon;

  editor.ui.add(editorOption.name, CKEDITOR.UI_BUTTON, toolOption);
  editor.addCommand(editorOption.options.command, function() {
    return {
      exec: function(editor) {
        component.events.iconClick(editor);
      }
    }
  })
}
```

#### 에디터 생성

**XEeditor.getEditor\('에디터명'\).create\(selector, options, editorOptions, toolInfoList\)**

* selector \(string\) - 생성되는 에디터의 selector
* options \(object\) - 에디터 생성시 필요한 옵션
* editorOptions \(object\) - 에디터 옵션
* toolInfoList \(array\) - 등록된 tool정보

정의된 에디터를 생성하기 위해서는 XEeditor.getEditor\('에디터명'\).create를 통해 생성할 수 있습니다.

