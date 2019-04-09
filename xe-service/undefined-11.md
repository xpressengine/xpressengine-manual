# 메일

## 메일\(email\)

### 설정

메일에 대한 설정 파일은 `config/mail.php`입니다. SMTP 호스트, 포트, 인증 및 라이브러리를 통해 송신되는 메세지들의 글로벌 `form` 주소를 설정할 수 있는 옵션들을 제공합니다. 원하는 경우 어떤 SMTP 서버라도 사용할 수 있습니다. 메일을 보낼 때 PHP의 `mail` 함수를 사용하려 한다면 설정 파일에서 `driver`를 `mail`로 변경하면 됩니다. 또한, `sendmail` 드라이버도 사용할 수 있습니다.

#### API 드라이버

또한, Mailgun과 Mandrill의 HTTP API 드라이버를 사용할 수 있습니다. HTTP API를 사용하려면 [라라벨 문서](http://xpressengine.github.io/laravel-korean-docs/docs/5.0/mail/)를 참고하십시오.

#### 로그 드라이버

`config/mail.php` 설정 파일에서 `driver` 옵션을 `log`로 설정한다면 실제로 이메일을 수신자에게 보내지 않고 로그 파일에 기록하게 됩니다. 이 설정은 주로 로컬에서 빠르게 디버깅을 해야하거나 내용을 확인하고자 할때 유용합니다.

### 기본 사용법

`Mail::send` 메소드를 통해서 이메일을 보낼 수 있습니다:

```php
Mail::send('emails.welcome', ['key' => 'value'], function($message)
{
    $message->to('foo@example.com', 'John Smith')->subject('Welcome!');
});
```

`send` 메소드의 첫 번째 인자로 이메일의 본문에 사용되는 뷰\(템플릿 파일\)의 이름을 전달합니다. 두 번째는 뷰로 전달되는 인자인데 대부분 배열의 형태로 구성되며 `$key`를 통해서 뷰에서 사용됩니다. 세 번째로 전달되는 클로저는 이메일 메세지에 대한 다양한 옵션을 지정하는 데 사용됩니다.

> **주의:** `$message`라는 이름의 변수가 항상 이메일 뷰에 전달되고, 인라인 첨부를 사용가능하게 합니다. 따라서 `message`라는 이름의 변수를 메일 뷰에서 사용하는 것은 피하는게 좋습니다.

만약 이메일 본문에서 사용할 템플릿 디자인이 준비되지 않았다면, XE에서 제공하는 템플릿 디자인을 사용할 수도 있습니다. XE에서 제공하는 템플릿 디자인은 `resources/views/emails/common.blade.php` 파일로 제공됩니다.

이메일 본문을 담고 있는 blade 파일에서 `emails.common`을 extends 하십시오.

```markup
<!-- plugins/my_plugin/views/emails/welcome.blade.php -->

@extends('emails.common')

@section('content')

이메일 내용...

@endsection
```

HTML 뷰에 추가로 플레인 텍스트 뷰를 지정할 수도 있습니다:

```php
Mail::send(['html.view', 'text.view'], $data, $callback);
```

또는 `html`또는 `text` 키를 사용하고 한 종류의 뷰를 지정할 수 있습니다:

```php
Mail::send(['text' => 'view'], $data, $callback);
```

이메일 메세지에 대해 참조나 첨부파일과 같은 다른 옵션들을 지정할 수도 있습니다:

```php
Mail::send('emails.welcome', $data, function($message)
{
    $message->from('us@example.com', 'Laravel');

    $message->to('foo@example.com')->cc('bar@example.com');

    $message->attach($pathToFile);
});
```

이메일에 첨부파일을 추가하고자 할때는 파일의 MIME 타입 또는 첨부파일이 표시되는 이름을 지정할 수 있습니다:

```php
$message->attach($pathToFile, ['as' => $display, 'mime' => $mime]);
```

이메일을 보내기 위한 뷰 대신에 간단한 문자열을 사용하고자 한다면 `raw` 메소드를 사용하면 됩니다:

```php
Mail::raw('Text to e-mail', function($message)
{
    $message->from('us@example.com', 'Laravel');

    $message->to('foo@example.com')->cc('bar@example.com');
});
```

> **주의:** `Mail::send` 클로저에 전달되는 메세지 인스턴스는 SwiftMailer 메세지 클래스를 확장하므로 이메일을 작성하는 데 필요한 클래스의 메소드들를 사용할 수 있습니다.

### 인라인 첨부

이메일에 인라인 이미지를 포함시키는 것은 번거로운 일입니다. XE는 이메일에 이미지를 첨부하고 최적의 CID를 얻을 수 있는 편리한 방법을 제공합니다.

**이메일 뷰에서 이미지를 첨부하는 방법**

```markup
<body>
    Here is an image:

    <img src="<?php echo $message->embed($pathToFile); ?>">
</body>
```

**이메일 뷰에서 Raw 데이터를 첨부하는 방법**

```markup
<body>
    Here is an image from raw data:

    <img src="<?php echo $message->embedData($data, $name); ?>">
</body>
```

`$message` 변수는 항상 `Mail` 클래스에 의해서 뷰에 전달된다는 것에 주의하십시오.

### 로컬 개발환경에서의 메일

이메일을 전송하는 플러그인을 개발하는 경우에 로컬 또는 개발 환경이라면 메세지 전송을 비활성화 하는것이 바람직할 것입니다. 이를 위해서 `config/mail.php` 설정 파일에 `pretend` 옵션을 `true`로 설정하거나 `Mail::pretend`메소드를 호출하면 됩니다. 메일러가 `pretend` 모드인 경우에는 이메일 메세지는 수신자에게 송신되는 대신에 어플리케이션의 로그 파일에 기록됩니다.

만약 실제로 이메일이 어떻게 보여지는지 확인하고자 한다면 [MailTrap](https://mailtrap.io)과 같은 서비스를 이용하는 것도 고려해보시기 바랍니다.

