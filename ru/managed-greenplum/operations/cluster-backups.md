# Управление резервными копиями в {{ mgp-name }}

Вы можете просматривать имеющиеся [резервные копии](../concepts/backup.md) и восстанавливать из них кластеры.

## Получить список резервных копий {#list-backups}

{% list tabs %}

- Консоль управления

    Чтобы получить список резервных копий кластера:
    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку ![image](../../_assets/mdb/backup.svg) **{{ ui-key.yacloud.mdb.cluster.switch_backups }}**.

    Чтобы получить список всех резервных копий в каталоге:
    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. На панели слева выберите ![image](../../_assets/mdb/backup.svg) **{{ ui-key.yacloud.mdb.cluster.switch_backups }}**.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы получить список резервных копий кластера {{ GP }}, выполните команду:

    ```bash
    {{ yc-mdb-gp }} cluster list-backups <имя_или_идентификатор_кластера>
    ```

    Идентификатор и имя кластера можно [получить со списком кластеров в каталоге](cluster-list.md#list-clusters).

    Результат:

    ```text
    +--------------------------+---------------------+----------------------+---------------------+
    |            ID            |     CREATED AT      |  SOURCE CLUSTER ID   |     STARTED AT      |
    +--------------------------+---------------------+----------------------+---------------------+
    | c9qgo11pud7k********:... | 2020-08-10 12:00:00 | c9qgo11pud7k******** | 2020-08-10 11:55:17 |
    | ...                                                                                         |
    +--------------------------+---------------------+----------------------+---------------------+
    ```

- API

    Чтобы получить список резервных копий кластера, воспользуйтесь методом REST API [listBackups](../api-ref/Cluster/listBackups.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/ListBackups](../api-ref/grpc/cluster_service.md#ListBackups) и передайте в запросе идентификатор кластера в параметре `clusterId`.

    Чтобы получить список резервных копий всех кластеров {{ mgp-name }} в каталоге, воспользуйтесь методом REST API [list](../api-ref/Backup/list.md) для ресурса [Backup](../api-ref/Backup/index.md) или вызовом gRPC API [BackupService/List](../api-ref/grpc/backup_service.md#List) и передайте в запросе идентификатор каталога в параметре `folderId`.

    Идентификатор кластера можно получить со [списком кластеров в каталоге](cluster-list.md#list-clusters).

{% endlist %}

## Получить информацию о резервной копии {#get-backup}

{% list tabs %}

- Консоль управления

    Чтобы получить информацию о резервной копии существующего кластера:
    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку ![image](../../_assets/mdb/backup.svg) **{{ ui-key.yacloud.mdb.cluster.switch_backups }}**.

    Чтобы получить информацию о резервной копии удаленного ранее кластера:
    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. На панели слева выберите ![image](../../_assets/mdb/backup.svg) **{{ ui-key.yacloud.mdb.cluster.switch_backups }}**.

- API

    Чтобы получить информацию о резервной копии, воспользуйтесь методом REST API [get](../api-ref/Backup/get.md) для ресурса [Backup](../api-ref/Backup/index.md) или вызовом gRPC API [BackupService/Get](../api-ref/grpc/backup_service.md#Get) и передайте в запросе идентификатор резервной копии в параметре `backupId`.

    Чтобы узнать идентификатор, [получите список резервных копий](#list-backups).

{% endlist %}

## Восстановить кластер из резервной копии {#restore}

Технология Point-in-Time Recovery (PITR) позволяет вернуть состояние кластера на любую из точек восстановления, созданных позже сохраненной резервной копии. Подробнее см. в разделе [{#T}](../concepts/backup.md).

Восстанавливая кластер из резервной копии, вы создаете новый кластер с данными из резервной копии. Если в каталоге не хватает [ресурсов](../concepts/limits.md) для создания такого кластера, восстановиться из резервной копии не получится.

Для нового кластера необходимо задать все параметры, обязательные при создании.

Если вы хотите перенести хосты кластера {{ mgp-name }} в другую зону доступности, восстановите кластер из резервной копии. Такая возможность может понадобиться, например, если ваши хосты размещаются в зоне доступности `{{ region-id }}-c`, которая [выводится из эксплуатации](/blog/posts/2023/08/new-availability-zone). Во время восстановления из резервной копии укажите новую зону доступности. Если ваш кластер выступает в роли [эндпоинта {{ data-transfer-full-name }}](../../data-transfer/concepts/index.md#endpoint), перед восстановлением из резервной копии обратите внимание на [особенности миграции в {{ data-transfer-name }}](hosts/host-migration.md#data-transfer).

{% note warning %}

Существуют ограничения на параметры нового кластера при восстановлении из резервной копии:

{% include [limits](../../_includes/mdb/mgp/restore-limits.md) %}

{% endnote %}

Если в качестве времени восстановления выбран текущий момент, состояние нового кластера будет соответствовать последней доступной точке восстановления.

{% list tabs %}

- Консоль управления

    Чтобы восстановить из резервной копии существующий кластер:

    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. Нажмите на имя нужного кластера и выберите вкладку ![image](../../_assets/mdb/backup.svg) **{{ ui-key.yacloud.mdb.cluster.switch_backups }}**.
    1. Нажмите на значок ![image](../../_assets/horizontal-ellipsis.svg) для нужной резервной копии, затем нажмите **{{ ui-key.yacloud.mdb.forms.button_restore }}**.
    1. Задайте настройки нового кластера. В списке **{{ ui-key.yacloud.mdb.forms.base_field_folder }}** можно выбрать каталог для нового кластера.
    1. В настройке **{{ ui-key.yacloud.mdb.forms.field_date }}** задайте момент времени, на который нужно восстановить состояние кластера. Введите значение вручную или выберите из выпадающего календаря. Будет использована ближайшая к этому времени точка восстановления.

       Если оставить настройку без изменений, кластер будет приведен в состояние, сохраненное в резервной копии. Точки восстановления использованы не будут.

    1. В настройке **{{ ui-key.yacloud.greenplum.field_hosts-count }}** укажите количество хостов-сегментов.
    1. В настройке **{{ ui-key.yacloud.greenplum.field_segments-in-host }}** укажите количество [сегментов](../concepts/index.md) на хост.
    1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

    Чтобы восстановить из резервной копии удаленный ранее кластер:
    1. Перейдите на [страницу каталога]({{ link-console-main }}) и выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
    1. На панели слева выберите ![image](../../_assets/mdb/backup.svg) **{{ ui-key.yacloud.mdb.cluster.switch_backups }}**.
    1. Найдите нужную резервную копию по времени создания и идентификатору кластера. В колонке **{{ ui-key.yacloud.mdb.cluster.backups.column_name }}** содержатся идентификаторы в формате `<идентификатор_кластера>:<идентификатор_резервной_копии>`.
    1. Нажмите на значок ![image](../../_assets/horizontal-ellipsis.svg) для нужной резервной копии, затем нажмите **{{ ui-key.yacloud.mdb.forms.button_restore }}**.
    1. Задайте настройки нового кластера. В списке **{{ ui-key.yacloud.mdb.forms.base_field_folder }}** можно выбрать каталог для нового кластера.
    1. В настройке **{{ ui-key.yacloud.mdb.forms.field_date }}** задайте момент времени, на который нужно восстановить состояние кластера. Введите значение вручную или выберите из выпадающего календаря. Будет использована ближайшая к этому времени точка восстановления.

       Если оставить настройку без изменений, кластер будет приведен в состояние, сохраненное в резервной копии. Точки восстановления использованы не будут.

    1. В настройке **{{ ui-key.yacloud.greenplum.field_hosts-count }}** укажите количество хостов-сегментов.
    1. В настройке **{{ ui-key.yacloud.greenplum.field_segments-in-host }}** укажите количество [сегментов](../concepts/index.md) на хост.
    1. Нажмите кнопку **{{ ui-key.yacloud.common.create }}**.

    {{ mgp-name }} запустит операцию создания кластера из резервной копии.

- CLI

    {% include [cli-install](../../_includes/cli-install.md) %}

    {% include [default-catalogue](../../_includes/default-catalogue.md) %}

    Чтобы восстановить кластер из резервной копии:

    1. Посмотрите описание команды CLI для восстановления кластера {{ GP }}:

        ```bash
        {{ yc-mdb-gp }} cluster restore --help
        ```

    1. Запросите создание кластера из резервной копии:

        
        ```bash
        {{ yc-mdb-gp }} cluster restore \
           --backup-id=<идентификатор_резервной_копии> \
           --time=<момент_времени> \
           --name=<имя_кластера> \
           --environment=<окружение> \
           --network-name={{ network-name }} \
           --master-resource-preset=<класс_хоста> \
           --master-disk-size=<объем_хранилища_ГБ> \
           --master-disk-type=<тип_диска> \
           --segment-resource-preset=<класс_хоста> \
           --segment-disk-size=<объем_хранилища_ГБ> \
           --segment-disk-type=<тип_диска> \
           --segment-host-count <количество_хостов-сегментов> \
           --segment-in-host <количество_сегментов_на_хост> \
           --zone-id=<зона_доступности> \
           --subnet-id=<идентификатор_подсети> \
           --assign-public-ip=<публичный_доступ_к_кластеру>
        ```


        Где:

        * `--backup-id` — идентификатор [резервной копии](../concepts/backup.md).
        * `--time` — момент времени, на который нужно восстановить состояние кластера {{ GP }}, в формате `yyyy-mm-ddThh:mm:ssZ`. По умолчанию кластер будет восстановлен в состояние, сохраненное в резервной копии.
        * `--name` — имя кластера.
        * `--environment` — окружение:

            * `PRESTABLE` — для тестирования. Prestable-окружение аналогично Production-окружению и на него также распространяется SLA, но при этом на нем раньше появляются новые функциональные возможности, улучшения и исправления ошибок. В Prestable-окружении вы можете протестировать совместимость новых версий с вашим приложением.
            * `PRODUCTION` — для стабильных версий ваших приложений.

        * `--network-name` — [имя сети](../../vpc/concepts/network.md#network).
        * `--master-resource-preset` — [класс хостов-мастеров](../concepts/instance-types.md#available-flavors).
        * `--master-disk-size` — размер хранилища хостов-мастеров в гигабайтах.
        * `--master-disk-type` — [тип диска](../concepts/storage.md) хостов-мастеров.
        * `--segment-resource-preset` — [класс хостов-сегментов](../concepts/instance-types.md#available-flavors).
        * `--segment-disk-size` — размер хранилища хостов-сегментов в гигабайтах.
        * `--segment-disk-type` — [тип диска](../concepts/storage.md) хостов-сегментов.
        * `--segment-host-count` — количество хостов-сегментов.
        * `--segment-in-host` — количество [сегментов](../concepts/index.md) на хост.
        * `--zone-id` — [зона доступности](../../overview/concepts/geo-scope.md).

        
        * `--subnet-id` — [идентификатор подсети](../../vpc/concepts/network.md#subnet). Необходимо указывать, если в выбранной зоне доступности создано две или больше подсетей.
        * `--assign-public-ip` — флаг, который указывается, если кластеру требуется доступ из интернета.


- API

    Чтобы восстановить кластер из резервной копии, воспользуйтесь методом REST API [restore](../api-ref/Cluster/restore.md) для ресурса [Cluster](../api-ref/Cluster/index.md) или вызовом gRPC API [ClusterService/Restore](../api-ref/grpc/cluster_service.md#Restore) и передайте в запросе:

    * Идентификатор требуемой резервной копии в параметре `backupId`. Чтобы узнать идентификатор, [получите список резервных копий в кластере](#list-backups).
    * Момент времени, на который должен быть восстановлен кластер, в параметре `time`. По умолчанию кластер будет восстановлен в состояние, сохраненное в резервной копии.
    * Количество хостов-сегментов в параметре `segmentHostCount`.
    * Количество [сегментов](../concepts/index.md) на хост в параметре `segmentInHost`.
    * Имя нового кластера, который будет содержать восстановленные из резервной копии данные, в параметре `name`. Имя кластера должно быть уникальным в рамках каталога.

    По умолчанию кластер будет восстановлен в тот же каталог, где находится резервная копия. Чтобы восстановить кластер в другом каталоге, укажите идентификатор этого каталога в параметре `folderId`.

{% endlist %}

{% include [backup-warning](../../_includes/mdb/backups/backup-create-warning.md) %}

{% include [greenplum-trademark](../../_includes/mdb/mgp/trademark.md) %}
