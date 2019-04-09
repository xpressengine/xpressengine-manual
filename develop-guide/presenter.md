# 프레젠터

* 3.0.0-beta6 변경 사항

  > 화면 출력을 담당하는 renderer 의 명칭을 presentable 로 변경하고 이 인터페이스를 따르는 구현체의 이름을 변경함

* 3.0.0-beta7 변경 사항

  > 프리젠터의 본래 목적인 html, api 형식의 유연한 처리를 위해서 XePresenter::make\(\) 을 변경합니다. 이전에는 make\(\) 가 html에 대한 지원만 처리하였지만 이후 부터 make 를 사용할 경우 html, api 모두를 지원하도록 변경합니다.

XE에서는 웹 브라우저로 응답을 보낼 때 [뷰](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/docs/5.0/views)를 대신해서 프리젠터를 사용합니다.

프리젠터는 일반적인 HTML 응답뿐만 아니라 API 요청에 대한 Json 응답을 하나의 메소드로 처리할 수 있습니다. 프리젠터는 요청\(Request\) 정보에 포함된 응답 포멧을 검사하고, 데이터를 해당 포멧에 맞게 반환합니다.

XE 설계 과정에서, 하나의 컨트롤러를 이용해서 HTML 형식과 API 형식\(Json\)의 응답을 모두를 처리하여 유지보수의 비용을 줄이고자 하는 요구사항이 있었습니다.

프리젠터는 응답 포멧에 따라 presetable 한 구현체를 선택합니다. 기본으로 `HtmlPresenter`, `JsonPresenter` 두개의 구현체를 포함하고 있습니다. 만약 API 요청의 응답을 Json이 아닌 XML 형식으로 받고싶다면, 플러그인을 통해 presentable 한 XML 구현체를 추가하고 요청에서 반환 포멧을 xml로 하면 됩니다.

프리젠터는 XePresenter 파사드를 제공합니다.

## HTML 형식으로 응답하기 <a id="html"></a>

Html 응답을 처리할 경우, `HtmlPresenter`는 테마, 스킨을 처리합니다. `HtmlPresenter`는 테마 핸들러에게 테마 컴포넌트를 받아 처리합니다. 스킨은 `XePresenter::setSkinTargetId()`으로 외부에서 스킨 타겟 아이디를 입력받아 스킨 핸들러를 사용해 설정된 스킨을 사용합니다.

XE에서 HTML 형식으로 응답할 때에는, 컨트롤러가 생성한 데이터를 스킨을 사용해 HTML로 변환합니다. 또, 컴파일된 HTML에 테마를 적용하여 반환합니다. 프리젠터에서 스킨을 선택할 수 있도록 응답할 때 어떤 종류의 스킨을 사용해야 하는지 설정하고 사용해야 합니다.

> HTML 형식만 지원하려고 싶을 경우 `XePresenter::makeHtml()`을 사용합니다.

```php
// in app/Html/Controllers/ProfileController.php

// 스킨 타겟 지정
XePresenter::setSkinTargetId('member/profile');

...

// HTML 형식으로 반환, 'index' 뷰를 사용
return XePresenter::makeHtml('index', compact('user', 'grant'));
```

스킨을 사용하지 않고, 템플릿 파일의 뷰 이름을 바로 지정할 수도 있습니다.

```php
// in app/Html/Controllers/SeoController.php

// 뷰 이름을 직접 지정
return XePresenter::makeHtml('seo.setting');

```

### 스킨 타겟 아이디 <a id="undefined-1"></a>

스킨은 특정 컴포넌트나 집단 밑에 하나의 그룹을 형성합니다.

코어에서 제공하는 프로필 컨트롤러는 `member/profile` 아이디를 스킨 타겟 아이디로 사용하고 있습니다. 플러그인으로 프로필 스킨을 만드려고 할 때 제작되는 스킨 컴포넌트는 `member/profile`를 대상 아이디로 컴포넌트 아이디를 생성해야 하며 스킨 패키지는 이를 기준으로 스킨을 설정할 수 있는 관리자를 제공합니다.

다른 예로 Board 플러그의 Board 모듈 컴포너트는 `module/board@board` 스킨 타겟 아이디를 사용합니다.

스킨을 사용하는 컨트롤러는 `XePresenter::setSkinTargetId()`에 지정된 스킨 타겟 아이디를 설정하여 `HtmlRenderer::render()`가 처리될 때 설정된 스킨을 찾아 처리할 수 있도록 합니다.

### HtmlPresenter <a id="htmlpresenter"></a>

프리젠터에서 Html 응답처리할 `XePresenter::make()` 할 경우 기본으로 `/resources/views/`를 참고합니다. 이것은 `HtmlPresenter::renderSkin()`에서 스킨의 타겟 아이디가 지정되지 않았을 경우 [뷰](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/docs/5.0/views)를 직접 사용하기 때문입니다.

정상적인 사용 과정으로 스킨의 타겟 아이디를 프리젠터에게 전달한 경우 HtmlPresenter는 사용될 스킨을 찾아 Renderable 인터페이스의 `render()`를 실행시키며 스킨 컴포넌트는 [뷰](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/docs/5.0/views)를 사용해서 블레이드 파일을 처리합니다.

#### 전체 프레임 구성 파일 <a id="undefined-2"></a>

`resources/views/common/base.blade.php`으로 전체 프레임을 구성하며 `$content`에 테마를 전달받아 출력합니다.

`HtmlPresenter::render()`는 SEO, 스킨, 테마 순서로 처리되고 마지막에 `self::$commonHtmlWrapper`으로 감싸서 반환합니다. `self::$commonHtmlWrapper`는 `app/Providers/PresenterServiceProvider.php`에서 `config/xe.php`의 `HtmlWrapper`로 설정합니다.

`base.blade.php`는 프론트엔드에 등록된 js, css등 여러 요소들을 어떤 위치에 출력할지 결정하고 있습니다.

## API 형식으로 응답하기 <a id="api"></a>

XE는 json 형식을 지원하며 `JsonPresenter`가 사용됩니다.

```php
// in app/Html/Controllers/DynamicFieldController.php

  ...

  // $list를 json 형식으로 변환하여 반환
  return XePresenter::makeApi(['list' => $list]);
```

## 모든 형식 지원 <a id="undefined"></a>

HTML, API 모든 형식을 지원하기 위해서 `XePresenter::make()`를 사용합니다.

```php
// in src/Controllers/UserController.php

  ...

  // 응답 형식에 따라 $data를 json 형식으로 반환하거나, 
  // $data를 index 템플릿(스킨)에 적용한 후, HTML 형식으로 반환
  return XePresenter::make('index', $data);
```

> XE의 API를 이용한 개발 케이스가 많지 않아 API 지원에 대한 부분은 계속 개선해야 합니다.

