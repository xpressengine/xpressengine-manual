# 토글메뉴
토글메뉴는 형태에 따라 4가지 타입을 지원합니다.

- exec
- func
- link
- raw 

#### 토글메뉴 생성
추가하고자 하는 새로운 토글메뉴 클래스에서 `AbstractToggleMenu` 를 상속 받습니다. 그리고 명시된 추상메서드를 구현합니다.

* `getText`: 메뉴가 펼쳐졌을때 보여지게될 문자열입니다.
* `getType`: `exec`, `func`, `link`, `raw` 네가지 중 한가지를 반환해야 합니다.
* `getAction`: 해당 메뉴를 클릭했을때 실행 되어질 js 문자열입니다. 만약 타입이 `raw` 인 경우 html 이 반환되어야 합니다.
* `getScript`: 메뉴의 동작을 위해 특정 js 파일이 필요한 경우 해당 파일의 경로를 반환해 줍니다.
* `getIcon`: 메뉴가 표현될때 메뉴를 상징하는 아이콘을 표시해줍니다.`(현재 미지원)`

##### exec
`exec` 타입은 `getAction` 에 의해 반환된 문자열이 그 자체로 js 로 실행 가능한
형태를 가집니다. 특정 함수를 실행하는 경우 함수에서 필요로하는 인자는 해당 토글메뉴내에서 제공되어야 합니다.
```
public function getType()
{
  return 'exec';
}

public function getAction()
{
  return 'alert("hello")';
}
```

##### func
`func` 타입은 지정한 특정 함수를 실행시켜 줍니다. 이때 XE에서는 해당 함수에 토글메뉴에 지정된 대상과 대상에 속한 문서의 아이디를 인자로 전달해줍니다.
```
public function getType()
{
  return 'func';
}

public function getAction()
{
  return 'someMethod';
}

public function getScript()
{
  return asset('path/to/script/some.js');
}
```

```
# some.js
function someMethod(type, id)
{
  ...
}
```
> 토글메뉴에 지정된 대상이라 함은 토글메뉴에서 대상을 구분할 수 있는 값을 말합니다. 인스턴스를 가지는 대상은 인스턴스 아이디를 포함한 문자열이 됩니다. ex) `module/board@board/{instanceId}`, `comment/{instanceId}`


##### link
`link` 타입은 클릭시 다른페이지로 이동하는 메뉴입니다.
```
public function getType()
{
  return 'link';
}

public function getAction()
{
  return '/';
}
```

##### raw
`raw` 타입은 메뉴에 표현될 형태부터 실행될 방식까지 직접 지정하여 사용하는 방식입니다.
```
public function getType()
{
  return 'raw';
}

public function getAction()
{
  return '<a href="#" onclick="method('argument')">Raw메뉴</a>';
}
```
