# 뷰(View)

## 기본 사용법

컨트롤러는 `Request`를 처리한 다음 다양한 형태의 `Response`를 반환합니다. `GET` 요청일 경우 대부분 html 형식으로 `Response`를 반환할 것이며, 때로는 json 형식으로 반환할 것입니다. `POST` 요청일 경우에는 대부분 리다이렉트(redirect) 형식으로 반환할 것입니다. 

뷰는 컨트롤러가 html 형식의 `Response`를 만들 때 사용됩니다. 컨트롤러는 html 형식의 `Response`를 반환하기 위해 우선 필요한 데이터를 생성합니다. 그 다음, 생성된 데이터를 미리 정의된 템플릿에 적용하여 html 형식의 `Response`를 만들어야 합니다. 이때 뷰에 템플릿 정보와 데이터를 전달하면 뷰는 이를 html로 생성해줍니다.

뷰는 프리젠테이션 로직을 컨트롤러 및 어플리케이션 로직과 분리해주는 역할을 합니다.

간단한 뷰는 다음과 같습니다:


```html
// plugins/my_plugin/views/greeting.php
<html>
    <body>
        <h1>Hello, <?php echo $name; ?></h1>
    </body>
</html>
```

뷰는 다음과 같이 브라우저로 보내집니다:

```html
Route::get('/', function()
{
    return view('my_plugin::views.greeting', ['name' => 'James']);
});
```

위와 같이 `view` 헬퍼함수에 전달하는 첫 번째 인자는 템플릿 파일의 이름이 됩니다. `<플러그인 아이디>::`를 사용하십시오.

두 번째 전달 인자는 템플릿에서 사용하기 위한 데이터의 배열입니다.

당연하게도 뷰는 `resources/views` 디렉토리의 중첩된 서브 디렉토리를 구성할 수 있습니다. 예를 들어, 뷰파일이 `resources/views/admin/profile.php`처럼 저장되었다면 다음처럼 호출해야 합니다:

    return view('admin.profile', $data);

#### 뷰에 데이터 전달하기

    // Using conventional approach
    $view = view('greeting')->with('name', 'Victoria');

    // Using Magic Methods
    $view = view('greeting')->withName('Victoria');

위 예제의 경우 뷰에서는 `$name` 변수에 `Victoria`라는 값을 확인할 수 있습니다.

필요한 경우에 `view` 헬퍼함수에 두 번째 인자로 데이터 배열을 전달할 수도 있습니다:

	$view = view('greetings', $data);

이러한 방식으로 정보를 전달할 때,`$data`는 키/값으로 구성된 배열이어야 합니다. 뷰 안에서 여러분은 `{{ $key }}`와 같이 각각의 키에 해당하는 값에 엑세스 할 수 있습니다. (`$data['$key']`는 존재한다고 가정합니다. )

#### 모든 뷰에서 데이터 공유하기

때때로 어플리케이션에서 표시하는 모든 뷰에서 데이터를 공유할 필요가 있을 수도 있습니다. 이 경우 몇 가지의 옵션이 있습니다. `view` 헬퍼 함수를 사용하거나 `Illuminate\Contracts\View\Factory` [contract](/docs/5.0/contracts)를 이용하는 법, 또는 와일드 카드의 [view composer](#view-composers)를 통하는 방법입니다.

`view` 헬퍼함수를 이용하는 예제입니다.

    view()->share('data', [1, 2, 3]);

`View` 파사드를 사용할 수도 있습니다.

    View::share('data', [1, 2, 3]);

일반적으로 `share` 메소드는 서비스 프로바이더의 `boot` 메소드 안에서 호출합니다.  `AppServiceProvider`에서 편하게 추가할수도 있고, 다른 별도의 서비스 프로바이더를 생성하고 구성할 수도 있습니다.

> ** 참고:** `view` 헬퍼함수를 전달 인자 없이 호출하는 경우에는 반환값은 `Illuminate\Contracts\View\Factory` contract의 구현체가 됩니다.

#### 뷰가 존재하는지 판단하기

뷰 파일이 존재하는 판단해야될 필요가 있다면 `exists` 메소드를 사용하면 됩니다:

    if (view()->exists('emails.customer'))
    {
        //
    }

#### 파일 패스로부터 view 반환

필요하다면 절대경로를 기반으로 뷰를 생성할 수도 있습니다:

    return view()->file($pathToFile, $data);
