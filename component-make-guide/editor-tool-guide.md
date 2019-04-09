# 에디터툴 제작 방법

## 에디터툴

에디터툴은 에디터상에서 제공되는 기능 도구들을 말합니다. 기본적으로 제공되는 도구 이외에 사용자가 에디터에서 사용가능한 도구들을 추가할 수 있습니다.

### 에디터 도구 생성

#### 컴포넌트 추가

에디터 도구는 `AbstractTool` 을 상속받아 구현되어야 합니다. 그리고 다음 추상 메서드를 구현해야 합니다.

* `initAssets`: 도구가 동작하는데 필요로하는 파일들을 불러옵니다. \(js, css\)
* `getIcon`: 에디터 도구영역에 추가되기 위해 필요한 아이콘 파일입니다.
* `compile`: 등록된 내용중 해당 도구로 작성된 영역을 표현하기 위한 동작을 수행합니다.

```php
class SomeTool extends AbstractTool
{
    public function initAssets()
    {
        XeFrontend::js(asset('path/to/sometool.js'))->load();
    }

    public function getIcon()
    {
        return asset('path/to/icon.gif');
    }

    public function compile($content)
    {
        $content = /* 내용 변환 코드 */;

        return $content;
    }
}
```

### 사용제한

때로는 특정 사용자만 추가된 에디터 도구를 사용하기를 원할 수 있습니다. 이럴땐 `enable` 메서드를 작성하여 현재 사용자가 해당 도구를 사용할 수 있는지에 대한 권한 검사를 수행해주어야 합니다.

```php
public function enable()
{
    return /* true or false */;
}
```

XE 의 기능을 이용하여 검사를 하고자 한다면 [권한](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/service-permission.md) 페이지를 확인해 주십시오.

### Frontend

#### XEeditor.defineTool

에디터의 툴 스크립트를 정의하기 위해서는 `XEeditor.defineTool` ~~\(XEeditor.tools.define은 deprecated 처리되었습니다.\)~~을 통해 아래의 형식과 같이 정의되어야 합니다. XEeditor객체에서는 Tool의 인터페이스를 제공하며 인스턴스를 관리합니다.

```javascript
XEeditor.defineTool{
    id : '',
    events: {
        iconClick: function() {},
        elementDoubleClick: function() {},
    }
});
```

**id \(string\)**

tool의 유니크값을 사용합니다. 다른 tool들과 구분을 위해 사용됩니다.

```javascript
//예시
id: 'editortool/navermap@navermap'
```

**events**

**iconClick \(function\)**

에디터 툴이 클릭됐을 경우 호출되는 이벤트입니다.

```javascript
//예시
iconClick: function() {
    window.open('/tool');
}
```

**elementDoubleClick \(function\)**

에디터 내의 생성된 요소가 더블클릭될 경우 호출되는 이벤트입니다.

```javascript
//예시
elementDoubleClick: function() {
    window.open('/tool_edit');
}
```

#### Tool사용

XEeditor.getEditor\('에디터명'\).create시 4번째 인자로 툴정보를 넣어주게 됩니다. 해당 툴정보들은 정의된 에디터의 `addTools` 인터페이스를 통해 등록됩니다.

```javascript
//예시
XEeditor.getEditor('에디터명').create('content', {}, {}, [{"id":"editortool\/navermap@navermap","icon":"http:\/\/domain\/plugins\/template_tool\/assets\/icon.gif","options":[],"enable":true}]
)
```

