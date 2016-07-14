# 라이프 사이클

여러분이 XE를 사용하거나 XE의 플러그인을 개발하려는 개발자라면 XE의 사용법, 플러그인 제작법 그리고 XE에서 제공하는 여러가지 서비스들의 사용법만 숙지하는 것 만으로도 충분할 많은 것을 할 수 있습니다. 하지만 여러분이 좀더 고도의 기능을 필요로 하는 플러그인을 만들거나 XE를 제대로 사용하고 싶다면, XE가 어떤 방식과 어떤 구조로 요청을 처리하는지에 대한 전체적인 흐름을 알고 있어야 합니다. 전체적인 흐름을 알고 있지 않다면 결국 한계에 도달할 것이고, 전체적인 흐름을 알기 위해 노력하는 시점이 올 것입니다.

XE의 라이프 사이클은 크게 두 가지 경우로 나눌 수 있습니다. 사용자들이 웹브라우저를 통해 http 요청을 전송할 때 이를 처리하여 응답하는 일반적인 경우와, 사이트 관리자가 ssh와 같은 콘솔에 접근하여 php 명령을 실행시킬 경우가 있습니다. 이 문서에서는 더 일반적으로 생각할 수 있는 http 요청을 처리하는 경우에 대하여 살펴보겠습니다.


아래 다이어그램은 XE의 요청 처리 흐름을 개략적으로 보여줍니다.

![xe3 life](assets/lifecycle/xe3lifecycle.png)

## index.php

사용자의 웹브라우저로부터 http 전송 요청이 들어올 경우, XE는 항상 `index.php`파일을 실행시킵니다. `index.php` 파일이 그리 많은 코드를 가지고 있는 것은 아닙니다.

#### autoload 파일 로드
가장 먼저 `index.php`는 composer를 통해 생성된 autoload 파일을 로드합니다. autoload 파일을 로드함으로써 XE는 php 파일을 include하지 않고 자동으로 로드할 수 있게됩니다.

#### 서비스 컨테이너 생성

그 다음으로 

- autoload
- service container load
- kernel load
- generate request
- run kernel

## Kernel
- bootstrapping
  - load service providers
- load middlewares

## Middlewares

- various middlewares

## Router
- find controller
- call controller

## Controller
- resolve request
- use many services

## Presenter
- make response
- apply skin, wrap theme
