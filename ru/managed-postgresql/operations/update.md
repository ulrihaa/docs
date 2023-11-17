# Изменение настроек {{ PG }}-кластера

После создания кластера вы можете:

* [Изменить класс хостов](#change-resource-preset).

* [Настроить серверы {{ PG }}](#change-postgresql-config) согласно [документации {{ PG }}](https://www.postgresql.org/docs/current/runtime-config.html).

* [Изменить дополнительные настройки кластера](#change-additional-settings).

* [{#T}](#start-manual-failover).

* [Переместить кластер](#move-cluster) в другой каталог.


* [Изменить группы безопасности кластера](#change-sg-set).


Подробнее о других изменениях кластера:

* [{#T}](cluster-version-update.md).

* [{#T}](storage-space.md).

* [{#T}](host-migration.md).

## Изменить класс хостов {#change-resource-preset}

{% note info %}

Некоторые настройки {{ PG }} [зависят от выбранного класса хостов](../concepts/settings-list.md#settings-instance-dependent).

{% endnote %}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Выберите кластер и нажмите кнопку ![image](../../_assets/pencil.svg) **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_resource }}** выберите нужный класс для хостов {{ PG }}.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить [класс хостов](../concepts/instance-types.md) для кластера:

  1. Посмотрите описание команды CLI для изменения кластера:

      ```bash
      {{ yc-mdb-pg }} cluster update --help
      ```

  1. Запросите список доступных классов хостов (в колонке `ZONES` указаны зоны доступности, в которых можно выбрать соответствующий класс):

     
     ```bash
     {{ yc-mdb-pg }} resource-preset list
     ```

     ```text
     +-----------+--------------------------------+-------+----------+
     |    ID     |            ZONE IDS            | CORES |  MEMORY  |
     +-----------+--------------------------------+-------+----------+
     | s1.micro  | {{ region-id }}-a, {{ region-id }}-b,  |     2 | 8.0 GB   |
     |           | {{ region-id }}-c                  |       |          |
     | ...                                                           |
     +-----------+--------------------------------+-------+----------+
     ```


  1. Укажите нужный класс в команде изменения кластера:

      ```bash
      {{ yc-mdb-pg }} cluster update <имя_или_идентификатор_кластера> \
          --resource-preset <идентификатор_класса_хостов>
      ```

      {{ mpg-short-name }} запустит операцию изменения класса хостов для кластера.

- {{ TF }}

  1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

      О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

      Полный список доступных для изменения полей конфигурации кластера {{ mpg-name }} см. в [документации провайдера {{ TF }}]({{ tf-provider-mpg }}).

  1. Измените в описании кластера {{ mpg-name }} значение атрибута `resource_preset_id` в блоке `config.resources`:

      ```hcl
      resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
        ...
        config {
          resources {
            resource_preset_id = "<класс_хоста>"
            ...
          }
        }
      }
      ```

  1. Проверьте корректность настроек.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Подтвердите изменение ресурсов.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

- API

  Чтобы изменить класс хостов для кластера, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

  * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор кластера, [получите список кластеров в каталоге](./cluster-list.md#list-clusters).
  * Идентификатор класса хоста в параметре `configSpec.resources.resourcePresetId`. Список поддерживаемых значений запрашивайте методом [list](../api-ref/ResourcePreset/list.md) для ресурсов `ResourcePreset`.
  * Список настроек, которые необходимо изменить (в данном случае — `configSpec.resources.resourcePresetId`), в параметре `updateMask`.

  {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Изменить настройки {{ PG }} {#change-postgresql-config}

Вы можете изменить настройки СУБД для хостов вашего кластера.

{% note warning %}

* Вы не можете менять настройки {{ PG }} с помощью команд SQL.
* Некоторые настройки {{ PG }} [зависят от выбранного класса хостов или размера хранилища](../concepts/settings-list.md#settings-instance-dependent).

{% endnote %}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Выберите кластер и нажмите кнопку ![image](../../_assets/pencil.svg) **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. Измените [настройки {{ PG }}](../concepts/settings-list.md), нажав кнопку **{{ ui-key.yacloud.mdb.forms.button_configure-settings }}** в блоке **{{ ui-key.yacloud.mdb.forms.section_settings }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.component.mdb.settings.popup_settings-submit }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить [настройки сервера {{ PG }}](../concepts/settings-list.md):

  1. Посмотрите полный список настроек, установленных для кластера:

     ```bash
     {{ yc-mdb-pg }} cluster get <имя_или_идентификатор_кластера> --full
     ```

  1. Посмотрите описание команды CLI для изменения конфигурации кластера:

      ```bash
      {{ yc-mdb-pg }} cluster update-config --help
      ```

  1. Установите нужные значения параметров:

      Все поддерживаемые параметры перечислены в [формате запроса для метода update](../api-ref/Cluster/update.md), в поле `postgresqlConfig_<версия_{{ PG }}>`. Чтобы указать имя параметра в вызове CLI, преобразуйте его имя из вида <q>lowerCamelCase</q> в <q>snake_case</q>, например, параметр `maxPreparedTransactions` из запроса к API преобразуется в `max_prepared_transactions` для команды CLI:

      ```bash
      {{ yc-mdb-pg }} cluster update-config <имя_или_идентификатор_кластера> \
         --set <имя_параметра_1>=<значение_1>,<имя_параметра_2>=<значение_2>,...
      ```

      {{ mpg-short-name }} запустит операцию по изменению настроек кластера.

- {{ TF }}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

        Полный список доступных для изменения полей конфигурации кластера {{ mpg-name }} см. в [документации провайдера {{ TF }}]({{ tf-provider-mpg }}).

    1. Измените в описании кластера {{ mpg-short-name }} значения параметров в блоке `config.postgresql_config`. Если такого блока нет — создайте его.

        ```hcl
        resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
          ...
          config {
            ...
            postgresql_config = {
              max_connections                   = <максимальное_количество_соединений>
              enable_parallel_hash              = <true_или_false>
              vacuum_cleanup_index_scale_factor = <число_от_0_до_1>
              ...
            }
          }
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

- API

    Чтобы изменить настройки сервера {{ PG }}, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

    * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор кластера, [получите список кластеров в каталоге](./cluster-list.md#list-clusters).
    * Нужные значения настроек в параметре `configSpec.postgresqlConfig_<версия_{{ PG }}>`.
    * Список настроек, которые необходимо изменить, в параметре `updateMask`.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Изменить дополнительные настройки кластера {#change-additional-settings}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Выберите кластер и нажмите кнопку ![image](../../_assets/pencil.svg) **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. Измените дополнительные настройки кластера:

     {% include [mpg-extra-settings](../../_includes/mdb/mpg/extra-settings-web-console.md) %}

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить дополнительные настройки кластера:

    1. Посмотрите описание команды CLI для изменения кластера:

        ```bash
        {{ yc-mdb-pg }} cluster update --help
        ```

    1. Выполните команду, передав список настроек, которые хотите изменить:

        
        ```bash
        {{ yc-mdb-pg }} cluster update <имя_или_идентификатор_кластера> \
            --backup-window-start <время_начала_резервного_копирования> \
            --datalens-access=<true_или_false> \
            --maintenance-window type=<тип_технического_обслуживания>,`
                                `day=<день_недели>,`
                                `hour=<час_дня> \
            --websql-access=<true_или_false> \
            --deletion-protection=<защита_от_удаления> \
            --connection-pooling-mode=<режим_работы_менеджера_подключений> \
            --serverless-access=<true_или_false> \
            --yandexquery-access=<доступ_через_{{ yq-name }}> \
            --performance-diagnostics enabled=<true_или_false>,`
                                     `sessions-sampling-interval=<интервал_сбора_сессий>,`
                                     `statements-sampling-interval=<интервал_сбора_запросов>
        ```



    Вы можете изменить следующие настройки:

    {% include [backup-window-start](../../_includes/mdb/cli/backup-window-start.md) %}

    * `--datalens-access` — разрешает доступ из DataLens. Значение по умолчанию — `false`. Подробнее о настройке подключения см. в разделе [{#T}](datalens-connect.md).

    * `--maintenance-window` — настройки времени [технического обслуживания](../concepts/maintenance.md) (в т. ч. для выключенных кластеров), где `type` — тип технического обслуживания:

        {% include [maintenance-window](../../_includes/mdb/cli/maintenance-window-description.md) %}

    
    * `--websql-access` — разрешает [выполнять SQL запросы](web-sql-query.md) из консоли управления. Значение по умолчанию — `false`.
    
    
    * `--serverless-access` — разрешает доступ к кластеру из сервиса [{{ sf-full-name }}](../../functions/concepts/index.md). Значение по умолчанию — `false`. Подробнее о настройке доступа см. в документации [{{ sf-name }}](../../functions/operations/database-connection.md).

    * `--yandexquery-access` — разрешает доступ к кластеру из сервиса [{{ yq-full-name }}](../../query/concepts/index.md). Функциональность находится на стадии [Preview](../../overview/concepts/launch-stages.md) и предоставляется по запросу.



    * `--autofailover` – управляет настройкой автоматической смены мастера. Подробнее см. в разделе [{#T}](../concepts/replication.md#replication-auto). Значение по умолчанию — `true`.

    * `--connection-pooling-mode` — указывает [режим работы менеджера подключений](../concepts/pooling.md): `SESSION`, `TRANSACTION` или `STATEMENT`.

    * {% include [Deletion protection](../../_includes/mdb/cli/deletion-protection.md) %}

        {% include [Ограничения защиты от удаления кластера](../../_includes/mdb/deletion-protection-limits-db.md) %}

    * `--performance-diagnostics` — настройки [сбора статистики](./performance-diagnostics.md#activate-stats-collector):

        * `enabled` — значение `true` активирует сбор статистики. Значение по умолчанию — `false`.
        * `sessions-sampling-interval` — интервал сбора сессий, в секундах. Допустимые значения — от `1` до `86400`.
        * `statements-sampling-interval` — интервал сбора запросов, в секундах. Допустимые значения — от `60` до `86400`.

    Имя кластера можно [получить со списком кластеров в каталоге](cluster-list.md#list-clusters).

- {{ TF }}

  1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

      О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

      Полный список доступных для изменения полей конфигурации кластера {{ mpg-name }} см. в [документации провайдера {{ TF }}]({{ tf-provider-mpg }}).

  1. Чтобы изменить время начала резервного копирования, добавьте к описанию кластера {{ mpg-name }} блок `config.backup_window_start`.

      ```hcl
      resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
        ...
        config {
          backup_window_start {
            hours   = <час_начала_резервного_копирования>
            minutes = <минута_начала_резервного_копирования>
          }
          ...
        }
      }
      ```

  
  1. Чтобы разрешить доступ из {{ datalens-full-name }} и [выполнение SQL-запросов из консоли управления](web-sql-query.md), измените значения соответствующих полей в блоке `config.access`:

      ```hcl
      resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
        ...
        config {
          access {
            data_lens = <доступ_из_{{ datalens-name }}>
            web_sql   = <выполнение_SQL-запросов_из_консоли_управления>
            ...
        }
        ...
      }
      ```

      Где:

      * `data_lens` — доступ из {{ datalens-name }}: `true` или `false`.
      * `web_sql` — выполнение SQL-запросов из консоли управления: `true` или `false`.



  1. Чтобы изменить [режим работы менеджера подключений](../concepts/pooling.md), добавьте к описанию кластера {{ mpg-name }} блок `config.pooler_config`:

      ```hcl
      resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
        ...
        config {
          pooler_config {
            pool_discard = <параметр_Odyssey>
            pooling_mode = "<режим_работы>"
          }
          ...
        }
      }
      ```

      Где:

      * `pool_discard`  — параметр Odyssey `pool_discard`: `true` или `false`.
      * `pooling_mode` — режим работы: `SESSION`, `TRANSACTION` или `STATEMENT`.

  1. {% include [Maintenance window](../../_includes/mdb/mkf/terraform/maintenance-window.md) %}

  1. {% include [Performance diagnostics](../../_includes/mdb/mpg/terraform/performance-diagnostics.md) %}

  1. Чтобы включить защиту кластера от непреднамеренного удаления пользователем вашего облака, добавьте к описанию кластера поле `deletion_protection` со значением `true`:

      ```hcl
      resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
        ...
        deletion_protection = <защита_от_удаления>
      }
      ```

Где `deletion_protection` — защита от удаления кластера: `true` или `false`.

      {% include [Ограничения защиты от удаления](../../_includes/mdb/deletion-protection-limits-db.md) %}

  1. Проверьте корректность настроек.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Подтвердите изменение ресурсов.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

- API

    Чтобы изменить дополнительные настройки кластера, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

    * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор кластера, [получите список кластеров в каталоге](./cluster-list.md#list-clusters).
    * Настройки доступа из других сервисов  и к SQL-запросам из консоли управления  в параметре `configSpec.access`.
    * Настройки окна резервного копирования в параметре `configSpec.backupWindowStart`.
    * [Режим работы менеджера подключений](../concepts/pooling.md) в параметре `configSpec.poolerConfig.poolingMode`.
    * Настройки времени [технического обслуживания](../concepts/maintenance.md) (в т. ч. для выключенных кластеров) в параметре `maintenanceWindow`.
    * Настройки защиты от удаления кластера в параметре `deletionProtection`.

        {% include [Ограничения защиты от удаления](../../_includes/mdb/deletion-protection-limits-db.md) %}

    * Список изменяемых полей конфигурации кластера в параметре `updateMask`.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

        
    Чтобы разрешить доступ к кластеру из сервиса [{{ sf-full-name }}](../../functions/concepts/index.md), передайте значение `true` для параметра `configSpec.access.serverless`. Подробнее о настройке доступа см. в документации [{{ sf-name }}](../../functions/operations/database-connection.md).

    Чтобы разрешить доступ к кластеру из сервиса [{{ yq-full-name }}](../../query/index.yaml), передайте значение `true` для параметра `configSpec.access.yandexQuery`. Функциональность находится на стадии [Preview](../../overview/concepts/launch-stages.md) и предоставляется по запросу.


    Чтобы активировать [сбор статистики](./performance-diagnostics.md#activate-stats-collector):

    {% include [Performance diagnostic API](../../_includes/mdb/mpg/performance-diagnostics-api.md) %}

    {% include [datatransfer access](../../_includes/mdb/api/datatransfer-access-create.md) %}

{% endlist %}

## Вручную переключить хост-мастер {#start-manual-failover}

В отказоустойчивом кластере {{ mpg-name }} из нескольких хостов вы можете переключить роль мастера с текущего хоста-мастера на одну из реплик. После этой операции текущий хост-мастер станет хостом-репликой для нового мастера.

Особенности переключения мастера в {{ mpg-name }}:

* Нельзя сделать мастером реплику, для которой явно указан источник потока репликации.
* Если явно не указать имя хоста-реплики, мастер переключится на одну из кворумных реплик.

Подробнее см. в разделе [{#T}](../concepts/replication.md).

Чтобы переключить мастер:

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Нажмите на имя нужного кластера и выберите вкладку ![icon-hosts.svg](../../_assets/mdb/hosts.svg) **{{ ui-key.yacloud.postgresql.cluster.switch_hosts }}**.
  1. Нажмите кнопку ![icon-autofailover.svg](../../_assets/mdb/autofailover.svg) **{{ ui-key.yacloud.mdb.cluster.hosts.button_manual-failover }}**.
      * Чтобы переключить мастер на одну из кворумных реплик, оставьте опцию **{{ ui-key.yacloud.mdb.dialogs.popup-confirm-switch-master_auto }}** включенной.
      * Чтобы переключить мастер на конкретную реплику, выключите опцию **{{ ui-key.yacloud.mdb.dialogs.popup-confirm-switch-master_auto }}** и затем выберите нужную реплику из выпадающего списка.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.dialogs.popup-confirm-switch-master_button }}**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Выполните команду:

  ```bash
  {{ yc-mdb-pg }} cluster start-failover <имя_или_идентификатор_кластера> \
      --host <имя_хоста-реплики>
  ```

  Имя хоста-реплики можно запросить со [списком хостов в кластере](hosts.md#list), имя кластера — со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- {{ TF }}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

        Полный список доступных для изменения полей конфигурации кластера {{ mpg-name }} см. в [документации провайдера {{ TF }}]({{ tf-provider-mpg }}).

    1. Укажите имя хоста-реплики, на которую нужно переключиться, в параметре `host_master_name`.

        ```hcl
        resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
          ...
          host_master_name = "<имя_хоста-реплики>"
        }
        ```

        Где `host_master_name` — имя хоста-реплики: атрибут `name` соответствующего блока `host`.

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

- API

    Чтобы переключить хост-мастер, воспользуйтесь методом REST API [startFailover](../api-ref/Cluster/startFailover.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/StartFailover](../api-ref/grpc/cluster_service.md#StartFailover) и передайте в запросе:

    * Идентификатор кластера, в котором нужно переключить мастер, в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
    * (Опционально) Имя хоста-реплики, которую нужно сделать мастером, в параметре `hostName`. Чтобы узнать имя, [получите список хостов в кластере](hosts.md#list).

{% endlist %}

## Переместить кластер {#move-cluster}

{% list tabs %}

- Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
    1. Нажмите на значок ![image](../../_assets/horizontal-ellipsis.svg) справа в строке кластера, который вы хотите переместить.
    1. Выберите пункт **{{ ui-key.yacloud.mdb.clusters.button_action-move }}**.
    1. Выберите каталог, в который вы хотите переместить кластер.
    1. Нажмите кнопку **{{ ui-key.yacloud.mdb.dialogs.popup_button_move-cluster }}**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы переместить кластер:

    1. Посмотрите описание команды CLI для перемещения кластера:

        ```bash
        {{ yc-mdb-pg }} cluster move --help
        ```

    1. Укажите каталог назначения в команде перемещения кластера:

        ```bash
        {{ yc-mdb-pg }} cluster move <идентификатор_кластера> \
           --destination-folder-name=<имя_каталога_назначения>
        ```

        Идентификатор кластера можно получить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- API

    Чтобы переместить кластер, воспользуйтесь методом REST API [move](../api-ref/Cluster/move.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Move](../api-ref/grpc/cluster_service.md#Move) и передайте в запросе:

    * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
    * Идентификатор каталога назначения в параметре `destinationFolderId`.

{% endlist %}

После перемещения кластер продолжит использовать облачную сеть из исходного каталога. Если вы хотите разместить кластер в другой облачной сети, воспользуйтесь функцией [восстановления из резервной копии](./cluster-backups.md) и укажите необходимую сеть для копии кластера.

Если вы хотите переместить кластер в другую зону доступности, обратитесь к [инструкции](host-migration.md). В результате вы перенесете хосты кластера.


## Изменить группы безопасности {#change-sg-set}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-postgresql }}**.
  1. Выберите кластер и нажмите кнопку ![image](../../_assets/pencil.svg) **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_network }}** выберите группы безопасности для сетевого трафика кластера.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить список [групп безопасности](../concepts/network.md#security-groups) для кластера:

  1. Посмотрите описание команды CLI для изменения кластера:

      ```bash
      {{ yc-mdb-pg }} cluster update --help
      ```

  1. Укажите нужные группы безопасности в команде изменения кластера:

      ```bash
      {{ yc-mdb-pg }} cluster update <имя_или_идентификатор_кластера> \
          --security-group-ids <список_идентификаторов_групп_безопасности>
      ```

- {{ TF }}

  1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

      О том, как создать такой файл, см. в разделе [{#T}](cluster-create.md).

      Полный список доступных для изменения полей конфигурации кластера {{ mpg-name }} см. в [документации провайдера {{ TF }}]({{ tf-provider-mpg }}).

  1. Измените значение параметра `security_group_ids` в описании кластера:

      ```hcl
      resource "yandex_mdb_postgresql_cluster" "<имя_кластера>" {
        ...
        security_group_ids = [ <список_идентификаторов_групп_безопасности> ]
      }
      ```

  1. Проверьте корректность настроек.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

  1. Подтвердите изменение ресурсов.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      {% include [Terraform timeouts](../../_includes/mdb/mpg/terraform/timeouts.md) %}

- API

    Чтобы изменить список групп безопасности для кластера, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

    * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
    * Список идентификаторов групп безопасности в параметре `securityGroupIds`.
    * Список настроек, которые необходимо изменить (в данном случае — `securityGroupIds`), в параметре `updateMask`.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

{% note warning %}

Может потребоваться дополнительная [настройка групп безопасности](connect.md#configuring-security-groups) для подключения к кластеру.

{% endnote %}

