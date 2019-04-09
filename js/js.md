# 프론트 엔드 JS 시작하기

XpressEngine 3\(이하 XE\)의 Front-End의 기능 및 동작은 `XE` 변수에 담겨 있으며, App 및 Event, AOP를 이용할 수 있습니다.

## window.XE

`window.XE`를 노출하고 있으며, XE에서 제공하는 기능이 담겨있습니다.

* Event를 구독, 트리거 할 수 있도록 EventEmitter를 확장하고 있습니다
* Request, Router, Lang 등 내장된 App을 사용할 수 있는 상태로 포함하고 있습니다.
  * 각 App은 EventEmitter를 확장하고 있으며, 개별적으로 이벤트를 관리합니다.
* `XE.app()` 메소드로 Request 등 등록된 App의 instance를 전달하는 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/promise)를 반환합니다.

## EventEmitter

EventEmitter는 이벤트에 대한 listener를 관리하고 트리거하는데 사용됩니다.

* `$$emit`, `$$on`, `$$once`, `$$off`, `$$offAll`
* `$$on`, `$$once`
  * callback 첫번째 argument는 발생된 이벤트의 이름
* before 옵션으로 순서를 조정할 수 있습니다.
  * 이름을 가진 listener만 대상으로 할 수 있습니다.

```javascript
XE.$$on('eventName', /*callback*/(eventName, arg1/*, arg2, ...*/) => {
    console.debug('emitted', eventName, arg1, arg2)
}, options)

XE.$$on('setup', (eventName, arg) => {
    console.debug('emitted', eventName, arg)
}, { name: 'low'})

XE.$$on('setup', (eventName, arg) => {
    console.debug('emitted', eventName, arg)
}, { name: 'common', before: 'low' } )
```

## XE.app\(\)

* XE Object에 포함되는 모듈은 App을 확장하였으며, Singleton임
* EventEmitter를 확장하고 있음
* XE Core에서 다루는 것만을 고려했으므로 외부에서 이를 직접 확장하여 사용하기는 어려울 수 있음

```javascript
// XE.app()은 Promise를 반환 함

// boot된 instance를 반환
XE.app('Request').then((appRequest) => {
    appRequest.get()
})

// instance를 반환 (아직 boot 되지 않았을 수 있음)
XE.app('Request', (appRequest) => {
    appRequest.get()
})
```

