# 캡챠 설정
회원가입을 하거나, 수정 하는 등 코어에서 제공하는 네이버/구글 캡챠를 사용할 수 있습니다.


## 환경 설정

### 파일 생성
현재 API키를 등록하는 방법은 관리자 페이지에서 제공하고 있지 않습니다.
``config/production/captcha.php`` 파일을 추가해야합니다.

```php
<?php
/**
 * config/production/captcha.php
 *
 * PHP version 7
 *
 * @category    Config
 * @author      XE Developers <developers@xpressengine.com>
 * @copyright   2019 Copyright XEHub Corp. <https://www.xehub.io>
 * @license     http://www.gnu.org/licenses/lgpl-3.0-standalone.html LGPL
 * @link        https://xpressengine.io
 */

return [
    // 캡챠를 google로 사용하는 경우
    'driver' => env('CAPTCHA_DRIVER', 'google'),
    
    // 캡챠를 naver로 사용하는 경우
    'driver' => env('CAPTCHA_DRIVER', 'naver.com'),
    
    
    'apis' => [
    // 구글 API정보
        'google' => [
            'siteKey' => env('google_site_key', null),
            'secret' => env('google_secret_key', null)
        ],
    // 네이버 API정보
        'naver' => [
            'clientId' => env('naver_client_key', null),
            'secret' => env('naver_secret_key', null)
        ],
        
    ]
];
```


### 관리자 설정

관리자 페이지로 로그인하여, `회원 > 설정 > 기본설정` 으로 이동합니다.

"로그인 시 CAPTCHA 사용" 항목에 사용 버튼을 클릭후 저장하면 끝!

