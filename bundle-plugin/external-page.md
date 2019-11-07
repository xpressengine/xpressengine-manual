# 외부페이지 사용방법
XE에서 제공하는 게시판, 위젯, 심플 페이지를 사용하지 않고 직접 작성한 HTML 또는 PHP파일을 불러와 내 사이트에 보여줄 수 있습니다.

## 플러그인 설치
XE 코어에서는 기본적으로 외부페이지 플러그인이 설치되거나, 번들로 제공되지 않습니다.
가장 먼저 외부 페이지 익스텐션을 설치합니다. [익스텐션 설치는 이곳을 눌러 설치 방법](core-setupindex/extension-install-update)을 알아볼 수 있습니다.

설치 이후에는 <b>꼭 익스텐션을 활성화</b> 해줘야 합니다.


## 사용 방법
`관리자 > 사이트 맵> 사이트 메뉴 편집`에서 `아이템 추가` 기능으로 아아템을 추가해서 사용합니다.<br>
아이템 추가는 아래 순서로 가능합니다.
<center><img src="https://www.xpressengine.io/plugins/gitbookMarkDownParser/assets/html/dotgitbook/assets/item_add.gif" alt="아이템 생성방법(XE공통)" /></center>

1. 사이트맵 추가에서 `아이템 추가`를 클릭합니다.
2. `External Page Module`를 클릭하고 다음을 클릭합니다.
3. 추가할 아이템의 URL와 이름, 테마를 설정 합니다.
4. 하단의 `External Page Setting` 항목에 xe가 설치된 경로를 기준으로 절대 경로를 입력합니다.<br>
(예 : /extern_page/page_template/subpage.php)
5. 완료!

## 페이지를 삭제하는 방법

<center><img src="../.gitbook/assets/item_delete.gif"></center>

관리자 &gt; 사이트 맵&gt; 사이트 메뉴 편집에서 `아이템 제거` 기능으로 아이템을 삭제할 수 있습니다. 아이템 삭제는 아래 순서로 가능합니다.

* 삭제할 페이지 이름 클릭
* 상단의 아이템 삭제하기 클릭
* 최종 삭제 할 내용을 확인하고 삭제 클릭
* 깔끔히 삭제되었습니다.
