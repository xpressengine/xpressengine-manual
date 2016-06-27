# 플러그인 생성하기

여러분이 처음으로 플러그인을 생성하려면, `plugins` 디렉토리에 `my_plugin`이라는 이름의 디렉토리를 만들고, 그 안에 `plugin.php`, `composer.json` 파일을 만드는 것부터 시작해야 합니다. XE3는 여러분의 수고를 덜어드리기 위하여 플러그인 생성 명령을 제공합니다.

```
$ php artisan make:plugin <plugin_id> <plugin_namespace> <plugin_title>
```
