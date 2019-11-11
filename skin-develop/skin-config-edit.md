# 스킨 설정
## 스킨 설정 파일

### info.php

`info.php` 파일은 스킨의 구동에 필요한 여러가지 정보를 입력하는 파일입니다. 간단한 php 문법으로 쉽게 작성할 수 있습니다.

```php
<?php
return [
    'setting' => [
        'sample_text' => [
            '_type' => 'text',
            '_section' => '기본설정',
            'label' => '샘플 문구',
            'placeholder' => '샘플용 설정 필드입니다.',
            'description' => '샘플용 설정 필드입니다.',
        ],
    ],
    'support' => [
        'mobile' => true,
        'desktop' => true
    ]
];
```

`setting` 필드에는 스킨 설정 페이지에서 사용할 설정 항목에 대한 정보를 지정합니다.

`support` 필드에는 이 스킨이 데스크탑 버전과 모바일 버전을 지원하는지를 지정합니다.