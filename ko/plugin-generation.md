# 플러그인 생성하기

처음 플러그인 개발을 시작할 때 부딪히는 난관이 플러그인에 필요한 디렉토리와 파일들을 직접 작성하는 것입니다. 플러그인 아이디를 `my_plugin`으로 정했다면, 우선 `plugins` 디렉토리에 `my_plugin`이라는 이름의 디렉토리를 만들고, 그 안에 `plugin.php`, `composer.json` 파일을 만드는 것부터 시작해야 합니다. 이러한 수고를 줄이기 위하여 XE는 플러그인 생성 커맨드를 제공합니다. 터미널에서 아래와 같이 커맨드를 실행하십시오.

```
$ php artisan make:plugin <name> <namespace> <title>
```

`name` 파라메터는 플러그인의 고유 id입니다. 플러그인의 디렉토리 이름으로도 사용됩니다.

`namespace` 파라메터에는 플러그인 클래스의 네임스페이스를 지정합니다. 지정한 네임스페이스는 플러그인 클래스 뿐만 아니라 플러그인 내에 존재하는 모든 PHP 클래스의 네임스페이스로도 사용됩니다. 이 네임스페이스는 다른 개발자가 작성한 클래스와 클래스명이 동일할 때 서로 구분하기 위해 사용됩니다. 다른 사람과 중복되지 않는 자신만의 고유한 네임스페이스를 지정하십시오. 가능하면 자신의 이름이나 소속회사명을 사용하시길 권장합니다. 

예를 들어, 본인의 이름이 'SungbumHong'이고 'blog' 플러그인이라면 `SungbumHong\XePlugins\Blog` 또는 `SungbumHong\Blog`를 네임스페이스로 사용하십시오. 또다른 플러그인 'cafe'가 있다면, cafe 플러그인에는 `SungbumHong\XePlugins\Cafe`를 네임스페이스로 사용할 수 있습니다.

실제 플러그인 생성 커맨드를 실행한 화면입니다.

```
$ php artisan make:plugin 'my_plugin' 'SungbumHong\MyPlugin' 'my plugin'
Generating autoload files
Plugin is created and activated successfully.
See ./plugins/my_plugin directory. And open http://localhost/plugin/my_plugin in your browser.
Input and modify your plugin information in ./plugins/my_plugin/composer.json file.
```

> 플러그인을 생성한 다음에는 반드시 `composer.json` 파일에 플러그인에 대한 정보(description, keywords, author, license, version 등)를 자세히 기입하시기 바랍니다.


플러그인 생성 커맨드는 아래와 같은 과정을 진행합니다.

- 입력받은 정보에 따라 `plugins` 디렉토리에 플러그인 디렉토리를 생성하고, 필요한 파일들을 추가합니다. 
- 그 다음 `composer update`를 실행하여 `vendor` 디렉토리를 생성합니다.
- 플러그인을 활성화(activate)시킵니다.



생성된 디렉토리는 아래와 같은 구조를 가집니다.

```
my_plugin/
├── assets/
│   └── style.css
├── src/
├── vendor/
├── views/
│   └── index.blade.php
├── composer.json
└── plugin.php
```

플러그인 생성 커맨드를 통해 생성된 플러그인은 별다른 기능을 포함하고 있지 않습니다. 다만, 간단한 웹페이지를 출력하는 코드가 들어 있습니다. `http://localhost/plugin/my_plugin`에 접속해보시기 바랍니다.

`views/index.blade.php`는 웹페이지를 출력할 때 사용되는 블레이드(blade) 템플릿 파일입니다.

`assets/style.css`는 출력하는 웹페이지에 추가되는 css파일입니다.