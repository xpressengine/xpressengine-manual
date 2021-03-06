# 모바일

## 모바일\(mobile\)

플러그인을 개발할 때, 현재 접근한 기기가 데스크탑인지 모바일 기기인지 알아야 할 때가 있습니다. XE는 자체적으로 접근한 기기의 타입을 판단하고, 이를 사용할 수 있도록 인터페이스를 제공합니다.

### 기기 타입 강제 지정

XE는 우선 브라우저의 user-agent 정보를 바탕으로 현재 접근한 기기의 타입이 데스크탑인지 모바일인지 검사합니다.

만약 사용자가 접근한 기기의 타입을 강제로 지정하고 싶다면, 요청 URL의 쿼리스트링을 사용해 지정할 수 있습니다. 요청하는 URL에 `?_m=1`을 추가하면 XE가 현재 접속한 기기를 모바일 기기로 판단하도록 강제 지정할 수 있습니다. 반대로 `?_m=0`을 추가하면 데스크탑 기기로 판단하도록 강제 지정됩니다. 강제로 지정한 정보는 쿠키에 추가되어 차후 요청시에도 당분간 유지됩니다.

코드상에서 기기 타입을 지정하고 싶다면 직접 쿠키를 저장하면 됩니다. 쿠키 이름을 `mobile`에 값을 '0' 또는 '1'로 저장하십시오.

```php
// 현재 접속 기기를 120분간 모바일 기기로 강제 지정,
\Cookie::queue('mobile', '1', 120);
```

### 현재 접근한 기기의 타입 검사

XE는 현재 접근한 기기의 타입은 `Request` 인스턴스를 통해 알 수 있습니다.

```php
$isMobile = request()->isMobile();
```

`isMobile` 메소드를 사용하면, 현재 설정된 기기의 타입이 모바일인지 검사할 수 있습니다. 만약 접근한 기기 타입이 강제로 지정돼 있다면, 강제로 지정된 타입을 반환합니다.

강제로 지정된 타입과 관계없이 순수하게 브라우저의 user-agent 정보만으로 판단된 기기 타입을 알고싶다면 대신 `isMobileByAgent` 메소드를 사용하십시오. \(또는 `isMobile(true)`을 사용하십시오. 같은 응답을 합니다.\)

```php
$isMobile = request()->isMobileByAgent();
```

