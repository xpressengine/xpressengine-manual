# 응답

## Response 사용의 제한

XE의 기본 프레임워크인 라라벨에서는 대부분의 라우트나 컨트롤러 액션에서 `Illuminate\Http\Response`의 인스턴스나 [뷰](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/docs/5.0/views/README.md)를 반환하도록 합니다.

하지만 XE는 웹 브라우저로 html 형식의 응답을 보낼 때, 스킨과 테마를 적용한 후 보내야 합니다. 특별한 경우가 아니라면 컨트롤러에서 `Illuminate\Http\Response` 인스턴스나 뷰를 직접 반환\(return\)하지 마십시오. 대신, [프리젠터]()를 사용하여 반환하십시오. 반드시 [프리젠터]()를 사용해야만 테마와 스킨이 적용되고 위젯 또한 정상적으로 출력됩니다.

## 리다이렉트

일반적으로 리다이렉트 Response는 `Illuminate\Http\RedirectResponse` 클래스의 인스턴스이며, 사용자를 다른 URL로 리다이렉트하는 데 필요한 적절한 헤더를 포함하고 있습니다.

### 리다이렉트 반환하기

`RedirectResponse` 인스턴스를 생성하는 데는 몇 가지 방법이 있습니다. 가장 간단한 방법은 `redirect` 헬퍼 함수를 사용하는 것입니다. 테스트를 진행할 때 리다이렉트 Response를 생성하는 모킹\(Mock\)은 일반적으로 잘 하지 않기 때문에, 대부분의 경우에 헬퍼 함수를 사용하게 됩니다.

```text
return redirect('user/login');
```

### 리다이렉트에 플래시 데이터와 함께 반환하기

새로운 URL로 리다이렉트 이동하고 [플래시 데이터를 세션에 저장](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/docs/5.0/session/README.md) 하는 것은 일반적으로 동시에 진행됩니다. 따라서 편의성을 높이기 위해 `RedirectResponse` 인스턴스를 생성하고 **동시에** 메소드 체인을 통해 플래시 데이터를 세션에 저장할 수 있습니다:

```text
return redirect('user/login')->with('message', 'Login Failed');
```

### 이전 URL로 리다이렉트

예를 들어, 폼 전송 후에, 사용자를 이전 URL로 리다이렉트 시키고자 하는 경우가 있을 수 있습니다. 이런 경우에는 `back` 메소드를 사용하면 됩니다:

```text
return redirect()->back();

return redirect()->back()->withInput();
```

### 이름이 지정된 라우트로 리다이렉트 하기

전달 인자 없이 `redirect` 헬퍼 함수를 호출할 때에는 `Illuminate\Routing\Redirector`의 인스턴스가 반환됩니다. 따라서 `Redirector` 인스턴스의 메소드를 사용할 수 있습니다. 예를 들어, 이름지 지정된 라우트로 이동하는 `RedirectResponse`를 생성하고자 한다면 `route` 메소드를 사용할 수 있습니다:

```text
return redirect()->route('login');
```

### 이름이 지정된 라우트로 파라미터와 함께 리다이렉트 하기

라우트에 전달해야 할 파라미터가 있다면 `route` 메소드의 두 번째 인자로 전달하면 됩니다.

```text
// For a route with the following URI: profile/{id}

return redirect()->route('profile', [1]);
```

### 이름지 지정된 라우트로 파라미터 이름과 함께 리다이렉트 하기

```text
// For a route with the following URI: profile/{user}

return redirect()->route('profile', ['user' => 1]);
```

### 컨트롤러 액션으로 리다이렉트 하기

이름이 지정된 라우트로 이동하는 `RedirectResponse` 인스턴스를 생성하는것과 비슷하게 [컨트롤러 액션](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/docs/5.0/controllers/README.md)으로 리다이렉션 할 수 있습니다.

```text
return redirect()->action('App\Http\Controllers\HomeController@index');
```

> **주의:** `URL:setRootControllerNamespace`를 통해서 컨트롤러의 루트 네임스페이스가 지정되었다면, 전체 네임 스페이스를 지정할 필요가 없습니다.

### 컨트롤러 액션으로 파라미터와 함께 리다이렉트 하기

```text
return redirect()->action('App\Http\Controllers\UserController@profile', [1]);
```

### 컨트롤러 액션으로 파라미터 이름과 함께 리다이렉트 하기

```text
return redirect()->action('App\Http\Controllers\UserController@profile', ['user' => 1]);
```

## 기타 Response

`response` 헬퍼 함수를 사용하여 편리하게 다른 타입의 response 인스턴스를 생성할 수도 있습니다. `response` 헬퍼함수를 인자없이 호출하게 되면 `Illuminate\Contracts\Routing\ResponseFactory` [contract](https://github.com/xpressengine/xpressengine-manual/tree/c7478cb51aab4433d992bac673751500bc61d523/docs/5.0/contracts/README.md)를 반환합니다. 이 contract는 response를 생성하기 위한 다양한 메소드를 제공합니다.

### JSON response 생성하기

`json` 메소드는 헤더의 `Content-Type`을 자동으로 `application/json`으로 지정합니다:

```text
return response()->json(['name' => 'Abigail', 'state' => 'CA']);
```

### JSONP Response 생성하기

```text
return response()->json(['name' => 'Abigail', 'state' => 'CA'])
                 ->setCallback($request->input('callback'));
```

### 파일 다운로드 Response 생성하기

```text
return response()->download($pathToFile);

return response()->download($pathToFile, $name, $headers);

return response()->download($pathToFile)->deleteFileAfterSend(true);
```

> **참고:** 파일 다운로드를 관리하는 Symfony의 HttpFoundation에서 다운로드 할 파일의 이름이 ASCII 파일 이름임을 필요로 하고 있습니다.

