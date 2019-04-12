# 휴지통

## 휴지통\(trash\)

Trash는 서비스 또는 서드파티에서 휴지통\(bin\)을 등록받아 처리합니다. BinInterface 를 따르는 구현체를 구현하고 TrashManager를 통해 등록하면 휴지통 비우는 요청이 있을 때 관련된 휴지통을 찾아 처리합니다.

Trash 서비스는 휴지통을 관리하며 휴지통에 포함된 아이템을 직접 컨트롤하지 않습니다. 커맨드를 통해 사용가능한 휴지통을 확인하고 휴지통 비우기 기능을 제공할 뿐입니다.

### 기본 사용법

XeTrash 파사드로 TrashManager를 사용합니다.

> 휴지통의 기능은 휴지통에 포함된 요약 정보와 그것을 비우는 역할이므로 코드 보다는 명령어 기준으로 설명합니다. 아래 나열된 사용법은 터미널에서 입력하는 명령어 사용 방법입니다.

#### 휴지통 등록

휴지통 구현체를 TrashManager를 통해 등록합니다. artisan 명령어로 휴지통을 제어하기 위해서 휴지통을 등록해야하 합니다.

```php
XeTrash::register(NAME_SPACE\CLASS_NAME::class);
```

#### 요약 정보 확인

```php
$bins = XeTrash::gets();
foreach ($bins as $bin) {
    echo $bin::summary();
}
```

#### 전체 휴지통 비우기

```php
XeTrash::clean();
```

### 명령어 사용

터미널에서 아래와 같이 명령어를 실행합니다.

#### 전체 휴지통 요약 정보

```text
$ php artisan trash
```

#### 게시판 휴지통 정보 보기

```text
$ php artisan trash board
```

#### 댓글, 게시판의 요약 정보

한개 이상의 휴지통 요약 정보를 확인하고자 할 경우에는 콤마\(,\)로 구분해서 입력합니다.

```text
$ php artisan trash board,comment
```

#### 전체 휴지통 비우기

```text
$ php artisan trash:clean
```

#### 게시판 휴지통 비우기

```text
$ php artisan trash:clean board
```

#### 댓글, 게시판 비우기

```text
$ php artisan trash:clean board,comment
```

