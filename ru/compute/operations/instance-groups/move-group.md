---
title: "Перенос группы виртуальных машин в другую зону доступности"
description: "Из статьи вы узнаете, как можно перенести группу ВМ {{ compute-name }} из одной зоны доступности в другую."
---

# Перенести группу виртуальных машин в другую зону доступности


{% note info %}

{% include [zone-c-deprecation](../../../_includes/vpc/zone-c-deprecation.md) %}

{% endnote %}


Если группа ВМ подключена к балансировщику нагрузки, воспользуйтесь следующими инструкциями:
* [{#T}](move-group-with-nlb.md)
* [{#T}](move-group-with-alb.md)

Чтобы перенести группу ВМ в другую зону доступности:
1. [Создайте](../../../vpc/operations/subnet-create.md) подсеть в зоне доступности, в которую вы хотите перенести группу ВМ.
1. Добавьте ВМ группы в новую зону доступности:

    {% include [ig-create-in-another-zone](../../../_includes/compute/ig-create-in-another-zone.md) %}

1. Удалите ВМ группы из старой зоны доступности:

    {% include [ig-delete-in-zone.md](../../../_includes/compute/ig-delete-in-zone.md) %}

### См. также {#see-also}

* [{#T}](move-group-with-nlb.md)
* [{#T}](move-group-with-alb.md)