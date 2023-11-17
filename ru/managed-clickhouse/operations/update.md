# Изменение настроек кластера {{ CH }}

После создания кластера вы можете:


* [Изменить настройки сервисного аккаунта](#change-service-account).


* [{#T}](#change-resource-preset).

* [{#T}](#change-disk-size).

* [{#T}](#SQL-management).

* [Настроить серверы](#change-clickhouse-config) {{ CH }} согласно [документации {{ CH }}]({{ ch.docs }}/operations/server-configuration-parameters/settings).

* [Изменить дополнительные настройки кластера](#change-additional-settings).

* [Переместить кластер](#move-cluster) в другой каталог.


* [Изменить группы безопасности кластера](#change-sg-set).


* [Изменить настройки гибридного хранилища](#change-hybrid-storage).

Если вы хотите переместить кластер в другую зону доступности, обратитесь к [инструкции](host-migration.md). В результате вы перенесете хосты кластера.


## Изменить настройки сервисного аккаунта {#change-service-account}

{% list tabs %}

- Консоль управления

    1. В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
    1. Выберите кластер и нажмите кнопку **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
    1. В разделе **{{ ui-key.yacloud.mdb.forms.section_service-settings }}** выберите нужный сервисный аккаунт из списка или [создайте новый](../../iam/operations/sa/create.md). Подробнее о настройке сервисного аккаунта см. в разделе [{#T}](s3-access.md).

       {% include [mdb-service-account-update](../../_includes/mdb/service-account-update.md) %}

{% endlist %}


## Изменить класс хостов {#change-resource-preset}

{% note info %}

В кластерах с поддержкой {{ CK }} хосты {{ ZK }} не используются. Подробнее см. в разделе [Репликация](../concepts/replication.md).

{% endnote %}

Класс хостов влияет на количество оперативной памяти, доступной для использования {{ CH }}. Подробнее см. в разделе [Управление памятью](../concepts/memory-management.md).

Минимальное количество ядер для одного хоста {{ ZK }} зависит от суммарного количества ядер хостов {{ CH }}. Подробнее см. в разделе [Репликация](../concepts/replication.md#zk).

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Выберите кластер и нажмите кнопку **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. Чтобы изменить класс хостов {{ CH }}, в блоке **{{ ui-key.yacloud.mdb.forms.new_section_resource }}** выберите платформу, тип виртуальной машины и нужный класс хоста.
  1. Чтобы изменить класс хостов {{ ZK }}, в блоке **{{ ui-key.yacloud.mdb.forms.section_zookeeper-resource }}** выберите платформу, тип виртуальной машины и нужный класс хоста {{ ZK }}.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить [класс хостов](../concepts/instance-types.md) для кластера:

  1. Посмотрите описание команды CLI для изменения кластера:

     ```bash
     {{ yc-mdb-ch }} cluster update --help
     ```

  1. Запросите список доступных классов хостов (в колонке `ZONES` указаны зоны доступности, в которых можно выбрать соответствующий класс):

     
     ```bash
     {{ yc-mdb-ch }} resource-preset list

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
     {{ yc-mdb-ch }} cluster update <имя_или_ID_кластера> \
        --clickhouse-resource-preset=<ID_класса>
     ```

     {{ mch-short-name }} запустит операцию изменения класса хостов для кластера.

  1. Чтобы изменить класс хостов {{ ZK }}, передайте нужное значение в параметре `--zookeeper-resource-preset`.

- {{ TF }}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).

    1. Измените в описании кластера {{ mch-name }} значение параметра `resource_preset_id` в блоках `clickhouse.resources` и `zookeeper.resources` для хостов {{ CH }} и {{ ZK }} соответственно:

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя_кластера>" {
          ...
          clickhouse {
            resources {
              resource_preset_id = "<класс_хостов_{{ CH }}>"
              ...
            }
          }
          zookeeper {
            resources {
              resource_preset_id = "<класс_хостов_{{ ZK }}>"
              ...
            }
          }
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-resources-link }}/mdb_clickhouse_cluster).

    {% include [Terraform timeouts](../../_includes/mdb/mch/terraform/timeouts.md) %}

- API

  Чтобы изменить класс хостов, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

  * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](./cluster-list.md#list-clusters).
  * Нужные значения в параметре `configSpec.clickhouse.resources.resourcePresetId` (для ZooKeeper — `configSpec.zookeeper.resources.resourcePresetId`).

      Список поддерживаемых значений запрашивайте методом [list](../api-ref/ResourcePreset/list.md) для ресурсов `ResourcePreset`.

  * Список настроек, которые необходимо изменить, в параметре `updateMask`.

  {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Увеличить размер хранилища {#change-disk-size}

{% note info %}

В кластерах с поддержкой {{ CK }} хосты {{ ZK }} не используются. Подробнее см. в разделе [Репликация](../concepts/replication.md).

{% endnote %}

{% include [note-increase-disk-size](../../_includes/mdb/note-increase-disk-size.md) %}

{% list tabs %}

- Консоль управления

  Чтобы увеличить размер хранилища для кластера:
  
  1. В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Выберите кластер и нажмите кнопку **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_disk }}** укажите необходимое значение.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы увеличить размер хранилища для кластера:

  1. Посмотрите описание команды CLI для изменения кластера:

     ```bash
     {{ yc-mdb-ch }} cluster update --help
     ```

  1. Укажите нужный размер хранилища в команде изменения кластера (должен быть не меньше, чем значение `disk_size` в свойствах кластера):

     ```bash
     {{ yc-mdb-ch }} cluster update <идентификатор или имя кластера> \
       --clickhouse-disk-size <размер хранилища в ГБ>
     ```

  1. Чтобы увеличить размер хранилища хостов {{ ZK }}, передайте нужное значение в параметре `--zookeeper-disk-size`.

- {{ TF }}

  Чтобы увеличить размер хранилища:

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).

    1. Измените в описании кластера {{ mch-name }} значение параметра `disk_size` в блоках `clickhouse.resources` и `zookeeper.resources` для хостов {{ CH }} и {{ ZK }} соответственно:

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя_кластера>" {
          ...
          clickhouse {
            resources {
              disk_size = <размер_хранилища_в_ГБ>
              ...
            }
          }
          zookeeper {
            resources {
              disk_size = <размер_хранилища_в_ГБ>
              ...
            }
          }
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-resources-link }}/mdb_clickhouse_cluster).

    {% include [Terraform timeouts](../../_includes/mdb/mch/terraform/timeouts.md) %}

- API

  Чтобы увеличить размер хранилища, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

  * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](./cluster-list.md#list-clusters).
  * Нужный размер хранилища хостов {{ CH }} в параметре `configSpec.clickhouse.resources.diskSize`.
  * Нужный размер хранилища хостов {{ ZK }} в параметре `configSpec.zookeeper.resources.diskSize`.
  * Список полей конфигурации кластера, которые необходимо изменить, в параметре `updateMask`.

  {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Включить управление пользователями и базами данных через SQL {#SQL-management}

Сервис {{ mch-name }} позволяет включить управление [пользователями](./cluster-users.md#sql-user-management) и [базами данных](./databases.md#sql-database-management) кластера через SQL.

{% note alert %}

Управление пользователями и базами данных через другие интерфейсы станет недоступно.

Включенные однажды настройки управления пользователями или базами данных через SQL невозможно выключить.

{% endnote %}

{% list tabs %}

- Консоль управления

  1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Выберите кластер и нажмите кнопку **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. Для [управления пользователями через SQL](./cluster-users.md#sql-user-management), в блоке **{{ ui-key.yacloud.mdb.forms.section_settings }}** включите настройку **{{ ui-key.yacloud.mdb.forms.database_field_sql-user-management }}** и укажите пароль пользователя `admin`.
  1. Для [управления базами данных через SQL](./databases.md#sql-database-management), в блоке **{{ ui-key.yacloud.mdb.forms.section_settings }}** включите настройки **{{ ui-key.yacloud.mdb.forms.database_field_sql-user-management }}** и **{{ ui-key.yacloud.mdb.forms.database_field_sql-database-management }}**, укажите пароль пользователя `admin`.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    1. Чтобы включить [режим управления пользователями через SQL](./cluster-users.md#sql-user-management):

        * задайте значение `true` для параметра `--enable-sql-user-management`;
        * задайте пароль для пользователя `admin` в параметре `--admin-password`.

        ```bash
        {{ yc-mdb-ch }} cluster update <имя_или_ID_кластера> \
           ...
           --enable-sql-user-management true \
           --admin-password "<пароль_пользователя_admin>"
        ```

    1. Чтобы включить [режим управления базами данных через SQL](./databases.md#sql-database-management):

        * задайте значение `true` для параметров `--enable-sql-user-management` и `--enable-sql-database-management`;
        * задайте пароль для пользователя `admin` в параметре `--admin-password`.

        ```bash
        {{ yc-mdb-ch }} cluster update <имя_или_ID_кластера> \
           ...
           --enable-sql-user-management true \
           --enable-sql-database-management true \
           --admin-password "<пароль_пользователя_admin>"
        ```

- {{ TF }}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).

    1. {% include [Enable SQL user management with Terraform](../../_includes/mdb/mch/terraform/sql-management-users.md) %}

    1. {% include [Enable SQL database management with Terraform](../../_includes/mdb/mch/terraform/sql-management-databases.md) %}

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-mch }}).

    {% include [Terraform timeouts](../../_includes/mdb/mch/terraform/timeouts.md) %}

- API

    Чтобы включить управление пользователями и базами данных через SQL, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе нужные значения в параметре `configSpec.clickhouse.config`:

    * `sqlUserManagement` — задайте значение `true` для включения режима [управления пользователями через SQL](cluster-users.md#sql-user-management).
    * `sqlDatabaseManagement` — задайте значение `true` для включения режима [управления базами данных через SQL](databases.md#sql-database-management). Необходимо, чтобы был включен режим управления пользователями через SQL.
    * `adminPassword` — задайте пароль пользователя `admin`, с помощью которого осуществляется управление.

{% endlist %}

## Изменить настройки {{ CH }} {#change-clickhouse-config}

{% note info %}

Изменить значение настройки [Max server memory usage]({{ ch.docs }}/operations/server-configuration-parameters/settings/#max_server_memory_usage) можно только [изменив класс хостов {{ CH }}](#change-resource-preset).

Подробнее см. в разделе [Управление памятью](../concepts/memory-management.md).

{% endnote %}

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Выберите кластер и нажмите кнопку **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. Измените [настройки {{ CH }}](../concepts/settings-list.md#dbms-cluster-settings), нажав на кнопку **{{ ui-key.yacloud.mdb.forms.button_configure-settings }}** в блоке **{{ ui-key.yacloud.mdb.forms.section_settings }}**.
  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы изменить [настройки сервера {{ CH }}](../concepts/settings-list.md):

    1. Посмотрите полный список настроек, установленных для кластера:

        ```bash
        {{ yc-mdb-ch }} cluster get <имя_или_ID_кластера> --full
        ```

    1. Посмотрите описание команды CLI для изменения конфигурации кластера:

        ```bash
        {{ yc-mdb-ch }} cluster update-config --help
        ```

    1. Установите нужные значения параметров:

        ```bash
        {{ yc-mdb-ch }} cluster update-config <имя_или_ID_кластера> \
           --set <имя_параметра1>=<значение1>,...
        ```

        {{ mch-short-name }} запустит операцию по изменению настроек кластера.

        Все поддерживаемые параметры перечислены в [описании настроек {{ CH }}](../concepts/settings-list.md).

- {{ TF }}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).

    1. Измените в описании кластера {{ mch-name }} значения параметров в блоке `clickhouse.config`:

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя_кластера>" {
          ...
          clickhouse {
            ...

            config {
              # Общие настройки СУБД
              ...

              merge_tree {
                # Настройки движка MergeTree
                ...
              }

              kafka {
                # Общие настройки получения данных из Apache Kafka
                ...
              }

              kafka_topic {
                # Настройки отдельного топика Apache Kafka
                ...
              }

              rabbit_mq {
                # Настройки получения данных из {{ RMQ }}
                username = "<имя_пользователя>"
                password = "<пароль>"
              }

              compression {
                # Настройки сжатия данных
                method              = "<метод_сжатия:_LZ4_или_ZSTD>"
                min_part_size       = <минимальный_размер_куска_данных_таблицы_в_байтах>
                min_part_size_ratio = <отношение_размера_наименьшего_куска_таблицы_к_полному_размеру_таблицы>
              }

              graphite_rollup {
                # Настройки движка GraphiteMergeTree для прореживания и агрегирования/усреднения
                # (rollup) данных Graphite.
                ...
              }
            }
          ...
          }
        ...
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-resources-link }}/mdb_clickhouse_cluster).

    {% include [Terraform timeouts](../../_includes/mdb/mch/terraform/timeouts.md) %}

- API

  Чтобы изменить настройки {{ CH }}, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

  * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](./cluster-list.md#list-clusters).
  * Нужные значения в параметре `configSpec.clickhouse.config`.

      Все поддерживаемые настройки описаны в разделе [Настройки {{ CH }}](../concepts/settings-list.md#dbms-cluster-settings) и в [справочнике API](../api-ref/Cluster/update.md).

  * Список настроек, которые необходимо изменить, в параметре `updateMask`.

  {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Изменить дополнительные настройки кластера {#change-additional-settings}

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Выберите кластер и нажмите кнопку **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_service-settings }}** измените дополнительные настройки кластера:

     {% include [mch-extra-settings](../../_includes/mdb/mch/extra-settings-web-console.md) %}

  1. Нажмите кнопку **{{ ui-key.yacloud.mdb.forms.button_edit }}**.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить дополнительные настройки кластера:

    1. Посмотрите описание команды CLI для изменения кластера:

        ```bash
        {{ yc-mdb-ch }} cluster update --help
        ```

    1. Выполните команду, передав список настроек, которые хотите изменить:

        
        ```bash
        {{ yc-mdb-ch }} cluster update <имя_или_ID_кластера> \
           --backup-window-start <время_начала_резервного_копирования> \
           --datalens-access=<true_или_false> \
           --datatransfer-access=<true_или_false> \
           --deletion-protection=<защита_от_удаления_кластера:_true_или_false> \
           --maintenance-window type=<тип_технического_обслуживания:_anytime_или_weekly>,`
                               `day=<день_недели_для_типа_weekly>,`
                               `hour=<час_дня_для_типа_weekly> \
           --metrika-access=<true_или_false> \
           --serverless-access=<true_или_false> \
           --yandexquery-access=<доступ_через_Yandex_Query:_true_или_false> \
           --websql-access=<true_или_false>
        ```



    Вы можете изменить следующие настройки:

    {% include [backup-window-start](../../_includes/mdb/cli/backup-window-start.md) %}

    
    * `--datalens-access` — разрешает доступ из {{ datalens-name }}. Значение по умолчанию — `false`. Подробнее о настройке подключения см. в разделе [Подключение из {{ datalens-name }}](datalens-connect.md).

    * {% include [datatransfer access](../../_includes/mdb/cli/datatransfer-access-update.md) %}


    * {% include [Защита от удаления кластера](../../_includes/mdb/cli/deletion-protection.md) %}

        {% include [Ограничения защиты от удаления](../../_includes/mdb/deletion-protection-limits-db.md) %}

    * `--maintenance-window` — настройки времени [технического обслуживания](../concepts/maintenance.md) (в т. ч. для выключенных кластеров), где `type` — тип технического обслуживания:
  
        {% include [maintenance-window](../../_includes/mdb/cli/maintenance-window-description.md) %}

    
    
    * `--metrika-access` — разрешает [импорт данных из AppMetrika в кластер](https://appmetrica.yandex.ru/docs/common/cloud/about.html). Значение по умолчанию — `false`.

    * `--websql-access` — разрешает [выполнять SQL запросы](web-sql-query.md) из консоли управления. Значение по умолчанию — `false`.

    * `--serverless-access` — разрешает доступ к кластеру из сервиса [{{ sf-full-name }}](../../functions/concepts/index.md). Значение по умолчанию — `false`. Подробнее о настройке доступа см. в документации [{{ sf-name }}](../../functions/operations/database-connection.md).

    * `--yandexquery-access=true` — разрешает доступ к кластеру из сервиса [{{ yq-full-name }}](../../query/concepts/index.md). Функциональность находится на стадии [Preview](../../overview/concepts/launch-stages.md). Значение по умолчанию — `false`.



    Идентификатор и имя кластера можно [получить со списком кластеров в каталоге](cluster-list.md#list-clusters).

- {{ TF }}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).

    1. Чтобы изменить время начала резервного копирования, добавьте к описанию кластера {{ mch-name }} блок `backup_window_start`.

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя_кластера>" {
          ...
          backup_window_start {
            hours   = <час_начала_резервного_копирования>
            minutes = <минута_начала_резервного_копирования>
          }
          ...
        }
        ```

    
    1. Чтобы разрешить доступ из других сервисов и [выполнение SQL-запросов из консоли управления](web-sql-query.md), измените значения соответствующих полей в блоке `access`:

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя_кластера>" {
          ...
          access {
            data_lens  = <доступ_из_{{ datalens-name }}:_true_или_false>
            metrika    = <доступ_из_Метрики_и_AppMetrika:_true_или_false>
            serverless = <доступ_из_Cloud_Functions:_true_или_false>
            web_sql    = <выполнение_SQL-запросов_из_консоли_управления:_true_или_false>
            yandex_query = <доступ_из_Yandex_Query:_true_или_false>
          }
          ...
        }
        ```



    1. {% include [Maintenance window](../../_includes/mdb/mch/terraform/maintenance-window.md) %}

    1. Чтобы включить защиту кластера от непреднамеренного удаления пользователем вашего облака, добавьте к описанию кластера поле `deletion_protection` со значением `true`:

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя_кластера>" {
          ...
          deletion_protection = <защита_от_удаления_кластера:_true_или_false>
        }
        ```

        {% include [Ограничения защиты от удаления](../../_includes/mdb/deletion-protection-limits-db.md) %}

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-resources-link }}/mdb_clickhouse_cluster).

    {% include [Terraform timeouts](../../_includes/mdb/mch/terraform/timeouts.md) %}

- API

    Чтобы изменить дополнительные настройки кластера, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

    * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](./cluster-list.md#list-clusters).
    * Настройки доступа из других сервисов  и к SQL-запросам из консоли управления  в параметре `configSpec.access`.
    * Настройки окна резервного копирования в параметре `configSpec.backupWindowStart`.
    * Настройки времени [технического обслуживания](../concepts/maintenance.md) (в т. ч. для выключенных кластеров) в параметре `maintenanceWindow`.
    * Настройки защиты от удаления кластера в параметре `deletionProtection`.

        {% include [Ограничения защиты от удаления кластера](../../_includes/mdb/deletion-protection-limits-db.md) %}

    * Список изменяемых полей конфигурации кластера в параметре `updateMask`.

    {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

    
    
    Чтобы разрешить доступ к кластеру из сервиса [{{ sf-full-name }}](../../functions/concepts/index.md), передайте значение `true` для параметра `configSpec.access.serverless`. Подробнее о настройке доступа см. в документации [{{ sf-name }}](../../functions/operations/database-connection.md).

    Чтобы разрешить доступ к кластеру из сервиса [{{ yq-full-name }}](../../query/concepts/index.md), передайте значение `true` для параметра `configSpec.access.yandexQuery`. Функциональность находится на стадии [Preview](../../overview/concepts/launch-stages.md).



    {% include [datatransfer access](../../_includes/mdb/api/datatransfer-access-create.md) %}

{% endlist %}

## Переместить кластер {#move-cluster}

{% list tabs %}

- Консоль управления

    1. Перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
    1. Нажмите на значок ![image](../../_assets/horizontal-ellipsis.svg) справа в строке кластера, который вы хотите переместить.
    1. Выберите пункт **{{ ui-key.yacloud.common.move }}**.
    1. Выберите каталог, в который вы хотите переместить кластер.
    1. Нажмите кнопку **{{ ui-key.yacloud.mdb.dialogs.popup_button_move-cluster }}**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы переместить кластер:

    1. Посмотрите описание команды CLI для перемещения кластера:

        ```bash
        {{ yc-mdb-ch }} cluster move --help
        ```

    1. Укажите каталог назначения в команде перемещения кластера:

        ```bash
        {{ yc-mdb-ch }} cluster move <имя_или_ID_кластера> \
           --destination-folder-name=<имя_каталога_назначения>
        ```

        Идентификатор кластера можно получить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

- API

    Чтобы переместить кластер, воспользуйтесь методом REST API [move](../api-ref/Cluster/move.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Move](../api-ref/grpc/cluster_service.md#Move) и передайте в запросе:

    * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
    * Идентификатор каталога назначения в параметре `destinationFolderId`.

{% endlist %}


## Изменить группы безопасности {#change-sg-set}

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) перейдите на страницу каталога и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-clickhouse }}**.
  1. Выберите кластер и нажмите кнопку **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** на панели сверху.
  1. В блоке **{{ ui-key.yacloud.mdb.forms.section_network-settings }}** выберите группы безопасности для сетевого трафика кластера.

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить список [групп безопасности](../concepts/network.md#security-groups) для кластера:

  1. Посмотрите описание команды CLI для изменения кластера:

      ```bash
      {{ yc-mdb-ch }} cluster update --help
      ```

  1. Укажите нужные группы безопасности в команде изменения кластера:

      ```bash
      {{ yc-mdb-ch }} cluster update <имя_кластера> \
         --security-group-ids <список_идентификаторов_групп_безопасности>
      ```

- {{ TF }}

    1. Откройте актуальный конфигурационный файл {{ TF }} с планом инфраструктуры.

        О том, как создать такой файл, см. в разделе [Создание кластера](cluster-create.md).

    1. Измените значение параметра `security_group_ids` в описании кластера:

        ```hcl
        resource "yandex_mdb_clickhouse_cluster" "<имя_кластера>" {
          ...
          security_group_ids = [ <список_идентификаторов_групп_безопасности_кластера> ]
        }
        ```

    1. Проверьте корректность настроек.

        {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

    1. Подтвердите изменение ресурсов.

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-resources-link }}/mdb_clickhouse_cluster).

    {% include [Terraform timeouts](../../_includes/mdb/mch/terraform/timeouts.md) %}

- API

  Чтобы изменить группы безопасности, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

  * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  * Список идентификаторов групп безопасности в параметре `securityGroupIds`.
  * Список настроек, которые необходимо изменить, в параметре `updateMask`.

  {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

{% note warning %}

Может потребоваться дополнительная [настройка групп безопасности](connect.md#configuring-security-groups) для подключения к кластеру.

{% endnote %}


## Изменить настройки гибридного хранилища {#change-hybrid-storage}

{% list tabs %}

- CLI

  {% include [cli-install](../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../_includes/default-catalogue.md) %}

  Чтобы изменить [настройки гибридного хранилища](../concepts/storage.md#hybrid-storage-settings):

  1. Посмотрите описание команды CLI для изменения кластера:

      ```bash
      {{ yc-mdb-ch }} cluster update --help
      ```

  1. Если гибридное хранилище в кластере выключено, включите его:

      ```bash
      {{ yc-mdb-ch }} cluster update <имя_или_ID_кластера> \
          --cloud-storage=true
      ```

      {% include [Hybrid Storage cannot be switched off](../../_includes/mdb/mch/hybrid-storage-cannot-be-switched-off.md) %}

  1. Передайте список настроек, которые хотите изменить:

      ```bash
      {{ yc-mdb-ch }} cluster update <имя_или_ID_кластера> \
          --cloud-storage-data-cache=<true_или_false> \
          --cloud-storage-data-cache-max-size=<объем_памяти _в_байтах> \
          --cloud-storage-move-factor=<доля_свободного_места> \
          --cloud-storage-prefer-not-to-merge=<true_или_false>
      ```

      Вы можете изменить следующие настройки:

      {% include [Hybrid Storage settings CLI](../../_includes/mdb/mch/hybrid-storage-settings-cli.md) %}

- API

  Чтобы изменить настройки гибридного хранилища, воспользуйтесь методом REST API [update](../api-ref/Cluster/update.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) и передайте в запросе:

  * Идентификатор кластера в параметре `clusterId`. Чтобы узнать идентификатор, [получите список кластеров в каталоге](cluster-list.md#list-clusters).
  * Значение `true` в параметре `configSpec.cloudStorage.enabled`, если гибридное хранилище еще не включено.

    {% include [Hybrid Storage cannot be switched off](../../_includes/mdb/mch/hybrid-storage-cannot-be-switched-off.md) %}

  * [Настройки гибридного хранилища](../concepts/storage.md#hybrid-storage-settings) в параметрах `configSpec.cloudStorage`:

      {% include [Hybrid Storage settings API](../../_includes/mdb/mch/hybrid-storage-settings-api.md) %}

  * Список настроек, которые необходимо изменить, в параметре `updateMask`.

  {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}
