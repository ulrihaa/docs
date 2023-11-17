# Миграция хостов {{ MG }}-кластера в другую зону доступности

Хосты кластера {{ mmg-name }} располагаются в [зонах доступности](../../overview/concepts/geo-scope.md) {{ yandex-cloud }}. Хосты можно перенести из одной зоны в другую. Для этого:

1. [Создайте подсеть](../../vpc/operations/subnet-create.md) в зоне доступности, в которую вы переносите хосты.
1. Добавьте хост в кластер:

   {% list tabs %}

   - Консоль управления

      1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mongodb }}**.
      1. Нажмите на имя нужного кластера {{ mmg-name }} и перейдите на вкладку **{{ ui-key.yacloud.mdb.cluster.switch_hosts }}**.
      1. Нажмите кнопку ![image](../../_assets/plus-sign.svg) **{{ ui-key.yacloud.mdb.cluster.hosts.button_add-host }}**.
      1. Укажите параметры хоста:

         * Зону доступности, куда переносятся хосты.
         * Новую подсеть.
         * Выберите опцию **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}**, если хост должен быть доступен извне {{ yandex-cloud }}.

      1. Нажмите **{{ ui-key.yacloud.mdb.hosts.dialog.button_choose }}**.

   - CLI

      {% include [cli-install](../../_includes/cli-install.md) %}

      {% include [default-catalogue](../../_includes/default-catalogue.md) %}

      Выполните команду:

      ```bash
      {{ yc-mdb-mg }} host add \
         --cluster-name <имя_кластера> \
         --host type=<тип_хоста>,`
               `zone-id=<зона_доступности>,`
               `subnet-id=<ID_новой_подсети>,`
               `assign-public-ip=<публичный_доступ_к_хосту:_true_или_false>
      ```

      Особенности команды:

      * Имя кластера можно получить со [списком кластеров в каталоге](cluster-list.md#list-clusters).
      * Возможные значения параметра `type`: `mongod`, `mongos`, `mongocfg`, `mongoinfra`. Тип хоста зависит от [типа шардирования](../concepts/sharding.md#shard-management).
      * В параметре `zone-id` укажите зону, куда вы переносите хосты.

   - {{ TF }}

      1. В конфигурационный файл {{ TF }} с планом инфраструктуры добавьте манифест хоста:

         ```hcl
         resource "yandex_mdb_mongodb_cluster" "<имя_кластера>" {
           ...
           host {
             type             = "<тип_хоста>"
             zone_id          = "<зона_доступности>"
             subnet_id        = "<идентификатор_новой_подсети>"
             assign_public_ip = <публичный_доступ_к_хосту:_true_или_false>
             ...
           }
         }
         ```

         Возможные значения параметра `type`: `MONGOD`, `MONGOINFRA`, `MONGOS` или `MONGOCFG`. Тип хоста зависит от [типа шардирования](../concepts/sharding.md#shard-management).

         В параметре `zone` укажите зону, куда вы переносите хосты.

      1. Проверьте корректность настроек.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Подтвердите изменение ресурсов.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   - API

      Чтобы добавить хост в кластер, воспользуйтесь методом REST API [addHosts](../api-ref/Cluster/addHosts.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/AddHosts](../api-ref/grpc/cluster_service.md#AddHosts) и передайте в запросе:

      * Идентификатор кластера в параметре `clusterId`. Идентификатор можно получить со [списком кластеров в каталоге](cluster-list.md#list-clusters).
      * Настройки нового хоста в параметрах `hostSpecs`.

   {% endlist %}

1. Чтобы успешно выполнять подключение к базе данных после миграции, укажите FQDN нового хоста в вашем бэкенде или клиенте (например, в коде или графической IDE). Удалите FQDN прежнего хоста в первоначальной зоне.

   Чтобы узнать FQDN, получите список хостов в кластере:

   ```bash
   {{ yc-mdb-mg }} host list --cluster-name <имя_кластера>
   ```

   FQDN указан в выводе команды, в столбце `NAME`.

1. Удалите хосты в первоначальной зоне доступности:

   {% list tabs %}

   - Консоль управления

      1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mongodb }}**.
      1. Нажмите на имя нужного кластера {{ mmg-name }} и выберите вкладку **{{ ui-key.yacloud.mdb.cluster.switch_hosts }}**.
      1. Нажмите значок ![image](../../_assets/horizontal-ellipsis.svg) в строке нужного хоста, выберите пункт **{{ ui-key.yacloud.common.delete }}** и подтвердите удаление.

   - CLI

      Выполните команду для каждого хоста:

      ```bash
      {{ yc-mdb-mg }} host delete <FQDN_хоста> --cluster-name <имя_кластера>
      ```

   - {{ TF }}

      1. В конфигурационном файле {{ TF }} с планом инфраструктуры удалите из описания кластера блоки `host` с первоначальной зоной доступности.
      1. Проверьте корректность настроек.

         {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

      1. Введите слово `yes` и нажмите **Enter**.

         {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   - API

      Чтобы удалить хост, воспользуйтесь методом REST API [deleteHosts](../api-ref/Cluster/deleteHosts.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/DeleteHosts](../api-ref/grpc/cluster_service.md#DeleteHosts) и передайте в запросе:

      * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
      * FQDN-имя или массив имен удаляемых хостов в параметре `hostNames`.

   {% endlist %}

1. Дождитесь, когда кластер перейдет в состояние **Alive**. В консоли управления перейдите на страницу вашего каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mongodb }}**. Состояние кластера отображается в столбце **{{ ui-key.yacloud.mdb.clusters.column_availability }}**.

{% include [migration-in-data-transfer](../../_includes/data-transfer/migration-in-data-transfer.md) %}
