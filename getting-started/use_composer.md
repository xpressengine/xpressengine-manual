
# Composer 사용 방법
XpressEngine에서는 플러그인들의 의존성 관리 및 버전 관리를 위하여 composer를 사용하고 있습니다.
composer를 설치할 수 있는 환경과 하지 못하는 환경에서 사용할 수 있는 방법을 제공하는 문서입니다.

## Composer 확인하기

XE3를 사용하기 위해서는 composer(컴포저)가 설치 되어 있어야 합니다.
터미널(SSH)에서 아래의 명령어를 실행하여 컴포저가 설치되어 있는지 확인합니다.

```$ composer```

그 이후 composer와 관련된 명령어와 설명이 나열된 화면이 표시된다면 설치된 것입니다.
별다른 서버의 설정이 필요 없으며, 바로 설치하여 사용하실 수 있습니다.

## Composer 설치하기
`Curl` 또는 `sudo`를 실행할 수 있는 환경이거나, 가상 서버 등의 환경이라면 composer를 설치하여 손쉽게 Composer를 사용할 수 있습니다.

1. composer 설치
`$ curl -sS https://getcomposer.org/installer | sudo php`
2. 전역 변수로 사용하기 위한 디렉토리 이동
`$ mv composer.phar /usr/local/bin/`
3. composer를 입력하여 사용하기 위한 심볼릭 링크 생성
`$ sudo  ln  -s /usr/local/bin/composer.phar /usr/local/bin/composer`
4. 설치확인
`$ composer`
명령어를 입력 후, composer와 관련된 설명과 명령어가 나열된다면 정상적으로 사용할 수 있습니다.


## 설치할 수 없는 상황이라면

모든 서버가 내 서버라면 얼마나 좋을까요,
모든 CMS가 요구하는 패키지를 설치하고, 자유롭게 내 사이트를 만들고 일할 수만 있다면 말이에요.

아쉽게도 서버 관리자 아니거나, 웹호스팅 제공사에서 컴포저를 제공하지 않아서 설치가 안 될까 걱정이신가요? 그런 걱정을 덜기 위하여 모든 배포본에 컴포저를 빌드하여 제공하고 있습니다.

다만, 이 기능들을 사용하기 위해서는 터미널(SSH)가 필요하다는 사실!

1. 공식 홈페이지에서 XE3를 내려받습니다. [클릭해서 내려받기](http://start.xpressengine.io/download/latest.zip)
2. 내 서버에 압축을 풀어 올려 놓거나, zip파일 올려놓기
3. XE3를 설치하기.
4. 관리자 사이트로 로그인 한 후  관리자 사이트에서 설정 > 기본설정을 클릭 합니다.
5.  Composer 홈 디렉토리 항목에 XE3를 설치한 디렉토리를 입력합니다.
6. 너. 내 XE 사이트가 되어라!

> 압축 파일을 그대로 올릴 경우 터미널에서 웹 디렉토리로 이동 후 unzip ./latest.zip을 사용하여 압축을 풀어 준 후 내 웹사이트로 접속하여 설치하여야 합니다.

## 사용하기
### 설치 또는 사용 가능 한 환경에서는
컴포저가 설치 되어있어 사용가능 하거나, 설치한 경우에는 터미널에서 아래의 명령어와 같이 실행 가능 합니다. 아래의 명령어는 개인 플러그인(private 플러그인)을 설치할 때 사용하는 명령어 이며, 플러그인 폴더에 위치 하였을 때 사용할 수 있습니다.
` $ composer dump`

### XE3 빌트인 컴포저를 사용할 때에는
컴포저가 설치되어 있지 않거나, 설치할 수 없는 경우에는 XE3코어와 함께 빌트인 된 컴포저를 사용해야 합니다. 아래의 명령어를 통해 사용할 수 있으며, 개인 플러그인을 설치 할때 사용하는 명령어를 통해 예시를 표현합니다.
`$ /home/phantom/composer.phar dump`



만약 매뉴얼의 방법대로 진행하였으나 작동되지 않는 경우 [XE3 헬프센터]([https://www.xpressengine.io/help](https://www.xpressengine.io/help)) 를 확인하거나 [QnA]([https://www.xpressengine.io/qna](https://www.xpressengine.io/qna))에 문의해주세요.
