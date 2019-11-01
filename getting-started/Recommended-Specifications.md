
# 권장사양

## 서버 요구사양

XE를 설치하기 위해서는 아래의 요구사항이 만족되어야 합니다.

| 서버 요구사양 |         |
|:--------|:--------:|
| 웹 서버 | Apache & Nginx 등 |
| PHP 버전 | PHP 7 이상(XE3.0.0-beta.24 부터)  |
| PHP 모듈 | PDO PHP Extension <br>cURL PHP Extension<br>FileInfo PHP Extension<br>GD PHP Extension<br>Mbstring PHP Extension<br>OpenSSL PHP Extension<br>Zip PHP Extension |
| 데이터베이스 | MariaDB 또는 MySQL 5.1 이상  |
| 디스크 | 300M 이상 또는 500M 이상  |
| 터미널(SSH) | 접속 가능 환경 권장  |



## 권장 호스팅 환경
현재 일부 호스팅사에서 XE3를 정상적으로 사용할 수 있는 환경이 있으니 참고 부탁드립니다.

| 호스팅사 | 호스팅 제품 |
|:--------|:--------:|
| Cafe24 | <a href="https://www.cafe24.com/?controller=product_page&type=basic&page=autoban" target="_blank">CAFE24 - 10G 광아우토반</a> |
| 닷홈 | <a href="https://www.dothome.co.kr/web/premium/#section1" target="_blank">무제한 웹 호스팅 Basic 이상(Basic 포함)</a> |


무제한 웹 호스팅의 FREE 플랜에서는 PHP 7.0 을 설치하더라도 ``allow_url_fopen`` 과 같은 옵션을 사용할 수 없기에, Basic 이상의 상품을 신청 후 기술지원 게시판을 통해 설정 변경 요청이 필요합니다.
