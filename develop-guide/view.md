# 뷰

컨트롤러는 `Request`를 처리한 다음 다양한 형태의 `Response`를 반환합니다. `GET` 요청일 경우 대부분 html 형식으로 `Response`를 반환할 것이며, 때로는 json 형식으로 반환할 것입니다. `POST` 요청일 경우에는 대부분 리다이렉트\(redirect\) 형식으로 반환할 것입니다.

뷰는 컨트롤러가 html 형식의 `Response`를 만들 때 사용됩니다. 컨트롤러는 html 형식의 `Response`를 반환하기 위해 우선 필요한 데이터를 생성합니다. 그 다음, 생성된 데이터를 미리 정의된 템플릿에 적용하여 html 형식의 `Response`를 만들어야 합니다. 이때 뷰에 템플릿 정보와 데이터를 전달하면 뷰는 이를 html로 생성해줍니다.

뷰는 프리젠테이션 로직을 컨트롤러 및 어플리케이션 로직과 분리해주는 역할을 합니다.

## 뷰 사용의 제한 <a id="undefined"></a>

XE는 웹 브라우저로 html 형식의 응답을 보낼 때, 스킨과 테마를 적용한 후 보내야 합니다. 특별한 경우가 아니라면 컨트롤러에서 뷰를 직접 반환\(return\)하지 마십시오. 대신 [프리젠터](https://xpressengine.gitbook.io/xpressengine-manual/ko/undefined/presenter)를 사용하여 반환하십시오. 반드시 [프리젠터](https://xpressengine.gitbook.io/xpressengine-manual/ko/undefined/presenter)를 사용해야만 테마와 스킨이 적용되고 위젯 또한 정상적으로 출력됩니다.

단, 컨트롤러가 아닌 다른 구성요소이거나, 컨트롤러에서라도 반환하는 값이 아니라면, html 스트링을 생성할 때 뷰를 자유롭게 사용하십시오.

## 기본 사용법 <a id="undefined-1"></a>

간단한 뷰는 다음과 같습니다:

```markup
// plugins/my_plugin/views/greeting.php
<html>
    <body>
        <h1>Hello, <?php echo $name; ?></h1>
    </body>
</html>
```

뷰는 다음과 같이 브라우저로 보내집니다:

```text
Route::get('/', function()
{
    return view('my_plugin::views.greeting', ['name' => 'James']);
});
```

위와 같이 `view` 헬퍼함수에 전달하는 첫 번째 인자는 템플릿 파일의 이름이 됩니다. 두 번째 전달 인자는 템플릿에서 사용하기 위한 데이터의 배열입니다.

템플릿 파일을 지정할 때, `<플러그인아이디>::` 형태의 전치사\(prefix\)를 사용하면 플러그인 디렉토리를 기준으로 하는 상대경로로 파일 경로를 지정할 수 있습니다. 또, 점\(.\)을 사용하여 중첩된 서브 디렉토리에 있는 파일을 지정할 수도 있습니다.

### 뷰에 데이터 전달하기 <a id="undefined-2"></a>

```text
// Using conventional approach
$view = view('greeting')->with('name', 'Victoria');

// Using Magic Methods
$view = view('greeting')->withName('Victoria');
```

위 예제의 경우 뷰에서는 `$name` 변수에 `Victoria`라는 값을 확인할 수 있습니다.

필요한 경우에 `view` 헬퍼함수에 두 번째 인자로 데이터 배열을 전달할 수도 있습니다:

```text
$view = view('greetings', $data);
```

이러한 방식으로 정보를 전달할 때,`$data`는 키/값으로 구성된 배열이어야 합니다. 뷰 안에서 여러분은 `{{ $key }}`와 같이 각각의 키에 해당하는 값에 엑세스 할 수 있습니다. \(`$data['$key']`는 존재한다고 가정합니다. \)

### 파일 패스로부터 view 반환 <a id="view"></a>

필요하다면 절대경로를 기반으로 뷰를 생성할 수도 있습니다:

```text
return view()->file($pathToFile, $data);
```

