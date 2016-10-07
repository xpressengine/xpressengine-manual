# 휴지통(trash)

Trash는 서비스 또는 서드파티에서 휴지통(bin)을 등록받아 처리합니다.
BinInterface 를 따르는 구현체를 구현하고 TrashManager를 통해 register 에 등록하면 휴지통 비우는 요청이 있을 때 이를 처리합니다.