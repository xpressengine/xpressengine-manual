# Request

XE에 비동기 요청을 요청하고 처리하기 위해 사용합니다. 요청에 대한 결과는 Promise를 반환합니다.

공통으로 다음과 같은 arguments를 지정할 수 있습니다.

* url \(string. required\)
  * 요청보낼 URL 값
* options \(object. optional\)
  * 서버로 전송할 data 및 parameter, header

지원하는 Method 목록은 아래와 같습니다. HEAD를 제외하고 `XE.get()`, `XE.post()`와 같이 `XE` 객체에서 사용할 수도 있습니다.

* GET: `XE.Request.get()`
* POST: `XE.Request.post()`
* DELETE: `XE.Request.delete()`
* PUT: `XE.Request.put()`
* HEAD: `XE.Request.head()`

## GET 요청 예시 <a id="get"></a>

```text
XE.get('/path/to/target', { data: { id: 123 } }).then(function (response) {  console.log(response);  // => response = { data, status, statusText }});
```

[  
](https://xpressengine.gitbook.io/xpressengine-front-end/docs/pagemodal)

