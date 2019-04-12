# 카테고리

## 카테고리\(category\)

웹 애플리케이션을 구성하다보면 컨텐츠를 분류하고자 할때가 있습니다. 이때 필요한게 카테고리 기능입니다.

### 등록

카테고리를 사용하기 위해선 먼저 카테고리의 그룹을 생성해야 합니다. 그리고 해당 카테고리 그룹에 아이템들을 등록시킵니다.

```php
// 신규 카테고리 그룹 생성
$category = XeCategory::createCate();
// 카테고리 그룹에 아이템 추가
$item = XeCategory::createItem($category, ['word' => 'foo']);
```

만약 특정 카테고리 아이템의 자식에 해당하는 아이템을 등록하고자 한다면, 전달하는 값에 `parentId` 항목에 부모에 해당하는 아이디값을 포함하면 됩니다.

```php
$item = XeCategory::createItem($category, ['word' => 'foo', 'parentId' => '{parent id}']);
```

### 트리

카테고리는 트리구조를 가지고 있습니다. 그래서 특정 카테고리 아이템의 부모 또는 자식에 해당하는 아이템들을 조회할 수 있습니다.

```php
$item = XeCategory::items()->find($id);
// 자식에 해당하는 아이템
$children = $item->getChildren();
// 부모 아이템
$parent = $item->getParent();
```

또한 트리형태로 아이템들을 얻을 수 있습니다.

```php
$category = XeCagetory::cates()->find($id);
$tree = $category->getTree();     // 카테고리 전체 트리

$item = XeCategory::items()->find($id);
$tree = $item->getDescendantTree();  // 특정 아이템의 자손들로 이루어진 트리
```

