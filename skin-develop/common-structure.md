# 스킨 기본경로 및 구조

## 스킨 디렉토리 구조

생성된 스킨은 아래의 디렉토리 구조를 가집니다. `plugins/my_plugin/src/Skins/{skinName}` 디렉토리는 스킨의 모든 파일이 담겨 있는 '스킨 디렉토리'입니다.

```text
plugins/my_plugin/src/Skins
└── {skinName}/
    ├── assets/
    │   └── css/
    │       └── skin.css
    ├── views/
    │   └── index.blade.php
    ├── {skinName}Skin.php
    └── info.php
```

### assets 파일

스킨에 필요한 `.js`나 `.css` 파일 그리고 이미지 파일과 같은 asset 파일을 담는 디렉토리입니다.

### views 디렉토리

스킨을 출력할 때 사용하는 템플릿 파일들을 저장하는 디렉토리입니다. 템플릿 파일은 blade 템플릿 언어로 작성되어야 합니다. blade 템플릿 언어의 사용법은 [템플릿 문서](../develop-guide/template-blade.md)에 자세히 기술되어 있습니다.

### Skin.php 파일

`Skin.php` 파일\(위의 예에서는 `ProfileSkin.php` 파일\)은 스킨클래스 파일입니다. 스킨 생성 커맨드로 생성된 스킨의 클래스는 `\Xpressengine\Skin\GenericSkin` 클래스를 상속받고 있습니다. 또, `GenericSkin` 클래스는 스킨 컴포넌트의 추상클래스인 `\Xpressengine\Skin\AbstractSkin`를 상속받고 있습니다.

```text
\Xpressengine\Skin\AbstractSkin
└── \Xpressengine\Skin\GenericSkin
    └── Skin(Skin.php)
```

PHP 개발자가 아닌 스킨 제작자가 스킨 클래스를 직접 작성하는 것은 쉬운 일이 아닙니다. `GenericSkin` 클래스는 스킨 제작자들에게 규격화 된 스킨 구조를 제공함으로써 스킨 제작을 손쉽게 할 수 있도록 도와줍니다. `Skin.php` 파일은 처음 생성한 다음부터는 거의 직접 수정할 필요가 없습니다. `Skin.php`를 직접 수정하는 대신, `info.php`를 수정하십시오.

일반적으로 `Skin.php` 파일을 직접 수정하지 않아도 원하는 스킨을 제작하는 데에는 문제가 없습니다. 다만, 좀더 고수준의 스킨을 제작하고 싶거나, 특별한 기능을 스킨에 추가하고 싶다면 `Skin.php` 파일을 직접 수정하셔도 됩니다. `Skin.php` 파일을 수정하면 훨씬 자유롭고 편하게 스킨을 제작할 수 있습니다. 물론 그 전에 스킨 클래스의 각 메소드가 갖는 역할과 원리를 잘 이해하고 있어야 합니다.

### info.php 파일

`info.php` 파일은 스킨의 구동에 필요한 여러가지 정보를 입력하는 파일입니다. 간단한 php 문법으로 쉽게 작성할 수 있습니다.