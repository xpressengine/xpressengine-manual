# 프리젠터(Presenter)
웹 브라우저로 응답을 보낼 때 라라벨의 [뷰](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/docs/5.0/views)를 대신해서 프리젠터를 사용합니다.

프리젠터는 일반적인 Html 응답뿐만 아니라 API 요청에 대한 Json 응답을 하나의 메소드로 처리할 수 있습니다. 요청 정보에 포함된 응답 포멧을 참고해서 Html 이 아닐경우 해당 포멧에 맞는 반환을 합니다. 
XE 설계 과정에서 하나의 라우팅을 담당하는 컨트롤러를 이용해서 Html 과 API 모두를 처리하고자 하는 요구사항이 발생했으며 이런 기능을 통해서 유지보수의 비용을 줄일수 있기를 기대했습니다. 

프리젠터는 응답 포멧에 따라 렌더러를 선택해서 사용하며 XE 프리젠터 패키지에는 HtmlRenderer, JsonRenderer 두개의 렌더러가 포함되어 있습니다. 만약 API 요청의 응답을 Json이 아닌 XML 형식으로 받고싶다면 플러그인으로 렌더러를 추가하고 요청에서 반환 포멧을 xml로 하면 됩니다.

Html 응답을 처리할 경우 HtmlRenderer는 테마, 스킨을 처리합니다. HtmlRenderer는 테마 핸들러를 사용해서 처리하는 응답의 테마를 설정합니다. 스킨은 `XePresenter::setSkinTargetId()`으로 외부에서 타겟 컴포넌트 아이디를 입력받아 스킨 핸들러를 사용해 설정된 스킨을 사용합니다.

프리젠터는 XePresenter 파사드를 제공합니다.

## 기본적인 프리젠터 사용

#### Html 응답
XE에서 모든 Html 응답은 테마와 스킨을 사용해 처리되도록 하고 있습니다. 
프리젠터에서 스킨을 선택할 수 있도록 응답할 때 어떤 종류의 스킨을 사용해야 하는지 설정하고 사용해야 합니다.

`XePresenter::make()`는 html 형식만 지원합니다.
```php
XePresenter::setSkinTargetId('member/profile');
...
return XePresenter::make('index', compact('user', 'grant'));
```
> `app/Html/Controllers/ProfileController.php` 예시 코드 입니다.


스킨없이 뷰 블레이드 파일을 바로 사용할 수 있습니다. 
```php
return XePresenter::make('seo.setting');
```
> `app/Html/Controllers/SeoController.php`에서 스킨을 사용하지 않고 Html을 반환하는 코드입니다.

#### API 응답
XE는 json 형식을 지원하며 JsonRenderer가 사용됩니다. 

```php
return XePresenter::makeApi(['list' => $list]);
```
> `app/Html/Controllers/DynamicFieldController.php` 예시 코드 입니다.

#### 모든 형식 지원
Html, API 모든 형식을 지원하기 위해서 `XePresenter::all()`을 사용합니다.
```php
return XePresenter::makeAll('index', $this->listDataImporter($request));
```
> Board 플러그인의 `src/Controllers/UserController.php` 예시 코드 입니다.
> XE의 API를 이용한 개발 케이스가 많지 않아 API 지원에 대한 부분은 계속 개선해야 합니다.


## HtmlRenderer
프리젠터에서 Html 응답처리할 `XePresenter::make()` 할 경우 기본으로 `/resources/views/`를 참고합니다.
이것은 `HtmlRenderer::renderSkin()`에서 스킨의 타겟 아이디가 지정되지 않았을 경우 [뷰](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/docs/5.0/views)를 직접 사용하기 때문입니다.

정상적인 사용 과정으로 스킨의 타겟 아이디를 프리젠터에게 전달한 경우 HtmlRenderer는 사용될 스킨을 찾아 Renderable 인터페이스의 `render()`를 실행시키며 스킨 컴포넌트는 [뷰](https://xpressengine.gitbooks.io/xpressengine-manual/content/ko/docs/5.0/views)를 사용해서 블레이드 파일을 처리합니다.

#### 전체 프레임 구성 파일
`resources/views/common/base.blade.php`으로 전체 프레임을 구성하며 `$content`에 테마를 전달받아 출력합니다.

`HtmlRenderer::render()`는 SEO, 스킨, 테마 순서로 처리되고 마지막에 `self::$commonHtmlWrapper`으로 감싸서 반환합니다. 
`self::$commonHtmlWrapper`는 `app/Providers/PresenterServiceProvider.php`에서 `config/xe.php`의 `HtmlWrapper`로 설정합니다. 

`base.blade.php`는 프론트엔드에 등록된 js, css등 여러 요소들을 어떤 위치에 출력할지 결정하고 있습니다.
