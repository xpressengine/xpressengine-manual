# 스킨

스킨 컴포넌트는 스킨 생성 커맨드를 제공합니다. 스킨 생성 커맨드를 사용하면, 전문적인 PHP 지식이 없어도 스킨을 제작할 수 있도록 스킨을 구성하는 여러가지 파일을 자동으로 생성해 주고, 스킨을 XE에 등록해 해줍니다.

## 스킨 타겟

스킨은 항상 그 스킨이 사용되는 대상(타겟)이 있습니다. 예를 들어 게시판 스킨은 게시판이 대상이 되며, 회원 프로필 스킨은 회원 프로필 컨트롤러가 대상이 됩니다. 또 특정 위젯이 대상이 될 수도 있습니다. 게시판, 회원 프로필 컨트롤러, 위젯과 같이 스킨이 적용되는 대상을 '스킨 타겟'이라고 합니다. 모든 스킨은 스킨 타겟을 반드시 지정해야 합니다.

XE의 구성요소중 HTML을 출력하는 모든 구성요소는 스킨 타겟이 될 수 있습니다. 스킨 타겟은 HTML을 출력할 때, 스킨 시스템이 적용될 수 있도록 구현되어야 하며, 스킨 타겟 아이디를 지정해 주어야 합니다.

## 빈 스킨 생성

스킨 생성 커맨드를 사용하려면 우선 스킨이 소속될 플러그인이 마련되어 있어야 합니다. 아직 플러그인이 준비되지 않았다면, [플러그인 생성하기](plugin-generation.md) 문서를 참고하여 플러그인을 생성하길 바랍니다. 또, 스킨 타겟의 아이디를 미리 알고 있어야 합니다.

소속될 플러그인과 스킨 타겟 아이디가 준비되었다면 아래 커맨드를 사용하여 빈 스킨을 생성합니다.

```php
$ php artisan make:skin <path> <skin_target_id> <title>
```

`path`는 스킨이 위치할 디렉토리의 경로입니다. 플러그인의 디렉토리 이름을 포함한 경로를 입력해줍니다.

`skin_target_id`는 스킨 타겟 아이디를 지정해주십시오.

`title`에는 스킨의 제목을 지정해 주십시오. 지정한 스킨 제목은 스킨 선택시 스킨의 이름으로 표시됩니다.

만약 `my_plugin` 플러그인에 스킨을 넣고, 스킨 이름을 `나만의 프로필 스킨`으로 지정하고 싶다면, 아래와 같이 커맨드를 실행하시면 됩니다. 커맨드를 실행하면 생성되는 스킨의 개략적인 정보를 터미널에서 볼 수 있습니다.

```
$ php artisan make:skin my_plugin/skins/profile member/profile "나만의 프로필 스킨"

[New skin info]
  plugin:        my_plugin
  path:          my_plugin/skins/profile
  class file:    my_plugin/skins/profile/ProfileSkin.php
  class name:    SungbumHong\MyPlugin\ProfileSkin
  id:            member/profile/skin/my_plugin@profileskin
  title:         나만의 프로필 스킨
  description:   The Skin supported by My_plugin plugin.

 Do you want to add skin? [yes|no]:
 > yes

Generating autoload files
Skin is created successfully.
```



## 스킨 컴포넌트 아이디

스킨도 [컴포넌트](components.md)이므로 컴포넌트 아이디를 가지고 있어야 합니다. 스킨의 컴포넌트 아이디는 아래 규칙을 따라야 합니다.

```
<skin_target_id>/skin/<plugin_id>@<pure_id>
```

`skin_target_id`는 



## 스킨 등록



## 템플릿 파일



## 스킨 설정

