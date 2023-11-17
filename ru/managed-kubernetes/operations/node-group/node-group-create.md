# Создание группы узлов

Чтобы создать [группу узлов](../../concepts/index.md#node-group), сначала [создайте кластер {{ managed-k8s-name }}](../kubernetes-cluster/kubernetes-cluster-create.md) и убедитесь, что в [облаке](../../../resource-manager/concepts/resources-hierarchy.md#cloud) достаточно [свободных ресурсов](../../concepts/limits.md).

## Создайте группу узлов {#node-group-create}

{% list tabs %}

- Консоль управления

  {% include [node-group-create](../../../_includes/managed-kubernetes/node-group-create.md) %}

- CLI

  {% include [cli-install](../../../_includes/cli-install.md) %}

  {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды CLI для создания группы узлов {{ managed-k8s-name }}:

     ```bash
     {{ yc-k8s }} node-group create --help
     ```

  1. Укажите параметры группы узлов {{ managed-k8s-name }} в команде создания (в примере приведены не все доступные параметры):

     ```bash
     {{ yc-k8s }} node-group create \
       --allowed-unsafe-sysctls=<имена_небезопасных_параметров_ядра,_через_запятую> \
       --cluster-name <имя_кластера> \
       --cores <количество_vCPU> \
       --core-fraction <гарантированная_доля_vCPU> \
       --daily-maintenance-window <настройки_окна_обновлений> \
       --disk-size <размер_хранилища_в_ГБ> \
       --disk-type <тип_хранилища:_network-nvme_или_network-hdd> \
       --fixed-size <фиксированное_количество_узлов_в_группе> \
       --location <настройки_размещения_хостов_кластера> \
       --memory <количество_ГБ_RAM> \
       --name <имя_группы_узлов> \
       --network-acceleration-type <standard_или_software-accelerated> \
       --network-interface security-group-ids=[<идентификаторы_групп_безопасности>],subnets=[<имена_подсетей>],ipv4-address=<nat_или_auto> \
       --platform-id <идентификатор_платформы> \
       --container-runtime <среда_запуска_контейнеров> \
       --preemptible \
       --public-ip \
       --template-labels <ресурсные_метки> \
       --version <версия_{{ k8s }}_на_узлах_группы> \
       --node-name <шаблон_имени_узлов> \
       --node-taints <метки_taint-политик>
     ```

     Где:
     * `--allowed-unsafe-sysctls` — разрешение на использование узлами группы {{ managed-k8s-name }} [небезопасных параметров ядра](../../concepts/index.md#node-group), через запятую.
     * `--cluster-name` — имя [кластера {{ managed-k8s-name }}](../../concepts/index.md#kubernetes-cluster), в котором будет создана группа узлов.
     * `--cores` — количество vCPU для узлов {{ managed-k8s-name }}.
     * `--core-fraction` — [гарантированная доля vCPU](../../../compute/concepts/performance-levels.md) для узлов {{ managed-k8s-name }}.
     * `--daily-maintenance-window` — настройки окна [обновлений](../../concepts/release-channels-and-updates.md#updates).
     * `--disk-size` — [размер диска](../../../compute/concepts/disk.md#maximum-disk-size) узла {{ managed-k8s-name }}.
     * `--disk-type` — [тип диска](../../../compute/concepts/disk.md#disks_types) узла {{ managed-k8s-name }}.
     * `--fixed-size` — количество узлов в группе узлов {{ managed-k8s-name }}.
     * `--location` — [зона доступности](../../../overview/concepts/geo-scope.md), [сеть](../../../vpc/concepts/network.md#network) и [подсеть](../../../vpc/concepts/network.md#subnet), в которых будут расположены узлы {{ managed-k8s-name }}. Можно указать несколько вариантов.

       Если в одной команде передать параметры `--location`, `--network-interface` и `--public-ip`, [возникнет ошибка](../../qa/troubleshooting.md#conflicting-flags). Расположение группы узлов {{ managed-k8s-name }} достаточно указать в `--location` или `--network-interface`.

       {% include [assign-public-ip-addresses](../../../_includes/managed-kubernetes/assign-public-ip-addresses.md) %}

     * `--memory` — количество памяти для узлов {{ managed-k8s-name }}.
     * `--name` — имя группы узлов {{ managed-k8s-name }}.
     * `--network-acceleration-type` — выбор типа [ускорения сети](../../../compute/concepts/software-accelerated-network.md):
       * `standard` — без ускорения.
       * `software-accelerated` — программно-ускоренная сеть.

       {% include [note-software-accelerated-network](../../../_includes/managed-kubernetes/note-software-accelerated-network.md) %}

     * `--network-interface` — настройки сети:

       {% include [network-interface](../../../_includes/managed-kubernetes/cli-network-interface.md) %}

     * `--platform-id` — [платформа](../../../compute/concepts/vm-platforms.md) для узлов {{ managed-k8s-name }}.
     * `--container-runtime` — [среда запуска контейнеров](../../concepts/index.md#config), `docker` или `containerd`.
     * `--preemptible` — флаг, который указывается, если виртуальные машины должны быть [прерываемыми](../../../compute/concepts/preemptible-vm.md).
     * `--public-ip` — флаг, который указывается, если группе узлов {{ managed-k8s-name }} требуется [публичный IP-адрес](../../../vpc/concepts/address.md#public-addresses).
     * `--template-labels` — [ресурсные метки {{ yandex-cloud }}](../../../resource-manager/concepts/labels.md) в формате `<имя_метки>=<значение_метки>` для ВМ, представляющих узлы группы {{ managed-k8s-name }}. Можно указать несколько меток через запятую.
     * `--version` — версия {{ k8s }} на узлах группы {{ managed-k8s-name }}.
     * `--node-name` — шаблон имени узлов {{ managed-k8s-name }}. Для уникальности имени шаблон должен содержать хотя бы одну переменную:

       {% include [node-name](../../../_includes/managed-kubernetes/node-name.md) %}

     * `--node-taints` — метки [taint-политик](../../concepts/index.md#taints-tolerations) {{ k8s }}. Можно указать несколько меток.

     {% include [user-data](../../../_includes/managed-kubernetes/user-data.md) %}

     Результат:

     ```text
     done (1m17s)
     id: catpl8c44kii********
     cluster_id: catcsqidoos7********
     ...
         start_time:
           hours: 22
         duration: 36000s
     ```

  1. Чтобы указать [группу размещения](../../../compute/concepts/placement-groups.md) для узлов {{ managed-k8s-name }}:
     1. Получите список групп размещения с помощью команды `yc compute placement-group list`.
     1. Передайте имя или идентификатор группы размещения в параметре `--placement group` при создании группы узлов {{ managed-k8s-name }}:

        ```bash
        {{ yc-k8s }} node-group create \
        ...
          --placement-group <имя_или_идентификатор_группы_размещения>
        ```

- {{ TF }}

  Чтобы создать [группу узлов {{ managed-k8s-name }}](../../concepts/index.md#node-group):
  1. В каталоге с [файлом описания кластера](../kubernetes-cluster/kubernetes-cluster-create.md#kubernetes-cluster-create) создайте конфигурационный файл, содержащий параметры новой группы узлов {{ managed-k8s-name }}:
     * Имя группы узлов {{ managed-k8s-name }}.
     * Идентификатор [кластера {{ managed-k8s-name }}](../../concepts/index.md#kubernetes-cluster) в параметре `cluster_id`.
     * [Платформу](../../../compute/concepts/vm-platforms.md) для узлов {{ managed-k8s-name }}.
     * Настройку [среды запуска контейнеров](../../concepts/index.md#config) в параметре `container_runtime`.
     * [Ресурсные метки {{ yandex-cloud }}](../../../resource-manager/concepts/labels.md) для ВМ, представляющих узлы группы {{ managed-k8s-name }}, в блоке `nodeTemplate.labels`.
     * Настройки масштабирования в блоке `scale_policy`.

     Пример структуры конфигурационного файла:

     ```hcl
     resource "yandex_kubernetes_node_group" "<имя_группы_узлов>" {
       cluster_id = yandex_kubernetes_cluster.<имя_кластера>.id
       name       = "<имя_группы_узлов>"
       ...
       instance_template {
         name       = "<шаблон_имени_узлов>"
         platform_id = "<платформа_для_узлов>"
         network_acceleration_type = "<тип_ускорения_сети>"
         container_runtime {
          type = "<среда_запуска_контейнеров>"
         }
         labels {
           "<имя_метки>"="<значение_метки>"
         }
         ...
       }
       ...
       scale_policy {
         <настройки_масштабирования_группы_узлов>
       }
     }
     ```

     Где:
     * `cluster_id` – идентификатор [кластера {{ managed-k8s-name }}](../../concepts/index.md#kubernetes-cluster).
     * `name` — имя группы узлов {{ managed-k8s-name }}.
     * `instance_template` — параметры узлов {{ managed-k8s-name }}:
       * `name` – шаблон имени узлов {{ managed-k8s-name }}. Для уникальности имени шаблон должен содержать хотя бы одну переменную:

         {% include [node-name](../../../_includes/managed-kubernetes/node-name.md) %}

       * `platform_id` – [платформа](../../../compute/concepts/vm-platforms.md) для узлов {{ managed-k8s-name }}.
       * `network_acceleration_type` — тип [ускорения сети](../../../compute/concepts/software-accelerated-network.md):
         * `standard` — без ускорения.
         * `software-accelerated` — программно-ускоренная сеть.

         {% include [note-software-accelerated-network](../../../_includes/managed-kubernetes/note-software-accelerated-network.md) %}

       * `container_runtime`:
         * `type` — [среда запуска контейнеров](../../concepts/index.md#config): `docker` или `containerd`.
       * `labels` — [ресурсные метки {{ yandex-cloud }}](../../../resource-manager/concepts/labels.md) для ВМ, представляющих узлы группы {{ managed-k8s-name }}. Можно указать несколько меток через запятую.
       * `scale_policy` – настройки масштабирования.

     {% note warning %}

     Файл с описанием группы узлов {{ managed-k8s-name }} должен находиться в одном каталоге с [файлом описания кластера](../kubernetes-cluster/kubernetes-cluster-create.md#kubernetes-cluster-create).

     {% endnote %}

     * Чтобы создать группу с фиксированным количеством узлов, добавьте блок `fixed_scale`:

       ```hcl
       resource "yandex_kubernetes_node_group" "<имя_группы_узлов>" {
         ...
         scale_policy {
           fixed_scale {
             size = <количество_узлов_в_группе>
           }
         }
       }
       ```

     * Чтобы создать группу узлов {{ managed-k8s-name }} с [автомасштабированием](../../concepts/node-group/cluster-autoscaler.md), добавьте блок `auto_scale`:

       ```hcl
       resource "yandex_kubernetes_node_group" "<имя_группы_узлов>" {
         ...
         scale_policy {
           auto_scale {
             min     = <минимальное_количество_узлов_в_группе_узлов>
             max     = <максимальное_количество_узлов_в_группе_узлов>
             initial = <начальное_количество_узлов_в_группе_узлов>
           }
         }
       }
       ```

     * Чтобы добавить [DNS-записи](../../../dns/concepts/resource-record.md):

       {% include [node-name](../../../_includes/managed-kubernetes/tf-node-name.md) %}

     Подробнее см. в [документации провайдера {{ TF }}]({{ tf-provider-k8s-nodegroup }}).
  1. Проверьте корректность конфигурационных файлов.

     {% include [terraform-create-cluster-step-2](../../../_includes/mdb/terraform-create-cluster-step-2.md) %}

  1. Создайте кластер {{ managed-k8s-name }}.

     {% include [terraform-create-cluster-step-3](../../../_includes/mdb/terraform-create-cluster-step-3.md) %}

- API

  Воспользуйтесь методом API [create](../../api-ref/NodeGroup/create.md) и передайте в запросе:
  * Идентификатор [кластера {{ managed-k8s-name }}](../../concepts/index.md#kubernetes-cluster) в параметре `clusterId`. Его можно получить со [списком кластеров {{ managed-k8s-name }} в каталоге](../kubernetes-cluster/kubernetes-cluster-list.md#list).
  * [Конфигурацию группы узлов {{ managed-k8s-name }}](../../concepts/index.md#config) в параметре `nodeTemplate`.
  * Тип [ускорения сети](../../../compute/concepts/software-accelerated-network.md) в параметре `nodeTemplate.networkSettings.type`.

    {% include [note-software-accelerated-network](../../../_includes/managed-kubernetes/note-software-accelerated-network.md) %}

  * [Среду запуска контейнеров](../../concepts/index.md#config) в параметре `nodeTemplate.containerRuntimeSettings.type`.
  * [Ресурсные метки {{ yandex-cloud }}](../../../resource-manager/concepts/labels.md) для ВМ, представляющих узлы группы {{ managed-k8s-name }}, в параметре `nodeTemplate.labels`.
  * [Настройки масштабирования](../../concepts/autoscale.md#ca) в параметре `scalePolicy`.
  * [Настройки размещения](../../../overview/concepts/geo-scope.md) группы узлов {{ managed-k8s-name }} в параметрах `allocationPolicy`.
  * Настройки окна [обновлений](../../concepts/release-channels-and-updates.md#updates) в параметрах `maintenancePolicy`.
  * Список изменяемых настроек в параметре `updateMask`.

  {% include [Note API updateMask](../../../_includes/note-api-updatemask.md) %}

  Чтобы узлы использовали [нереплицируемые диски](../../../compute/concepts/disk.md#disks_types), передайте значение `network-ssd-nonreplicated` для параметра `nodeTemplate.bootDiskSpec.diskTypeId`.

  Размер нереплицируемых дисков можно менять только с шагом 93 ГБ. Максимальный размер такого диска — 4 ТБ.

  {% include [Нереплицируемый диск не имеет резервирования](../../../_includes/managed-kubernetes/nrd-no-backup-note.md) %}

  Чтобы разрешить использование узлами группы {{ managed-k8s-name }} [небезопасных параметров ядра](../../concepts/index.md#node-group), передайте их имена в параметре `allowedUnsafeSysctls`.

  Чтобы задать метки [taint-политик](../../concepts/index.md#taints-tolerations), передайте их значения в параметре `nodeTaints`.

  Чтобы задать шаблон имени узлов {{ managed-k8s-name }}, передайте его в параметре `nodeTemplate.name`. Для уникальности имени шаблон должен содержать хотя бы одну переменную:

  {% include [node-name](../../../_includes/managed-kubernetes/node-name.md) %}

  Чтобы добавить [DNS-записи](../../../dns/concepts/resource-record.md), передайте их настройки в параметре `nodeTemplate.v4AddressSpec.dnsRecordSpecs`. В FQDN записи DNS можно использовать шаблон с переменными для имени узлов `nodeTemplate.name`.

{% endlist %}

{% note alert %}

После создания группы узлов {{ managed-k8s-name }} в {{ compute-full-name }} появится одна или несколько ВМ с автоматически сгенерированными именами. Не изменяйте имена ВМ, принадлежащих кластеру {{ managed-k8s-name }}. Это приведет к нарушению работы группы узлов и всего кластера {{ managed-k8s-name }}.

{% endnote %}