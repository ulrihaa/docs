# Создать группу размещения

Создайте [группу размещения](../../concepts/placement-groups.md).

{% include [placement-groups-info.md](../../../_includes/compute/placement-groups-info.md) %}

{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) выберите [каталог](../../../resource-manager/concepts/resources-hierarchy.md#folder), в котором будет создана группа размещения.
  1. В списке сервисов выберите **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
  1. На панели слева выберите ![image](../../../_assets/compute/group-placement-pic.svg) **{{ ui-key.yacloud.compute.switch_placement-groups }}**.
  1. Перейдите на вкладку **{{ ui-key.yacloud.compute.placement-groups.label_tab-instances }}**.
  1. В правом верхнем углу нажмите кнопку **{{ ui-key.yacloud.compute.placement-groups.button_create }}** и выберите **{{ ui-key.yacloud.compute.placement-groups.button_create-instance-pg }}**.
  1. Введите имя группы размещения. Требования к нему:

      {% include [name-format](../../../_includes/name-format.md) %}

  1. (Опционально) Добавьте описание группы размещения.
  1. Нажмите кнопку **{{ ui-key.yacloud.compute.placement-groups.create.button_create }}**.

- CLI

  {% include [cli-install.md](../../../_includes/cli-install.md) %}

  {% include [default-catalogue.md](../../../_includes/default-catalogue.md) %}

  1. Посмотрите описание команды CLI для создания группы размещения:

     ```bash
     yc compute placement-group create --help
     ```

  1. Создайте группу размещения в каталоге по умолчанию с одной из стратегий размещения:

     {% include [pg-create](../../../_includes/compute/placement-groups-create.md) %}

  1. Проверьте, что группа размещения добавлена:

     ```bash
     yc compute placement-group list
     ```

     Результат:

     ```bash
     +----------------------+----------+----------+
     |          ID          |   NAME   | STRATEGY |
     +----------------------+----------+----------+
     | fd83bv4rnsna******** | my-group | SPREAD   |
     +----------------------+----------+----------+
     ```

- API

  Воспользуйтесь методом REST API [create](../../api-ref/PlacementGroup/create.md) для ресурса [PlacementGroup](../../api-ref/PlacementGroup/index.md) или вызовом gRPC API [PlacementGroupService/Create](../../api-ref/grpc/placement_group_service.md#Create).

- {{ TF }}

  {% include [terraform-definition](../../../_tutorials/terraform-definition.md) %}

  {% include [terraform-install](../../../_includes/terraform-install.md) %}

  1. Опишите в конфигурационном файле параметры группы размещения:

     * `name` — имя группы размещения. Формат имени:

        {% include [name-format](../../../_includes/name-format.md) %}

     * `folder_id` — идентификатор каталога, в котором создается группа размещения.
     * `description` — описание группы размещения.

     Пример структуры конфигурационного файла:

     ```hcl
     resource "yandex_compute_placement_group" "group1" {
       name        = "<имя группы размещения>"
       folder_id   = "<идентификатор каталога>"
       description = "<описание группы размещения>"
     }
     ```

     Более подробную информацию о параметрах ресурса `yandex_compute_placement_group` в {{ TF }} см. в [документации провайдера]({{ tf-provider-resources-link }}/compute_placement_group).

  1. В командной строке перейдите в папку, где расположен файл конфигурации {{ TF }}.

  1. Проверьте конфигурацию командой:

     ```
     terraform validate
     ```
     
     Если конфигурация является корректной, появится сообщение:
     
     ```
     Success! The configuration is valid.
     ```

  1. Выполните команду:

     ```
     terraform plan
     ```
  
     В терминале будет выведен список ресурсов с параметрами. На этом этапе изменения не будут внесены. Если в конфигурации есть ошибки, {{ TF }} на них укажет.

  1. Примените изменения конфигурации:

     ```
     terraform apply
     ```

  1. Подтвердите изменения: введите в терминал слово `yes` и нажмите **Enter**.

     После этого в указанном каталоге будут созданы все требуемые ресурсы. Проверить появление ресурсов и их настройки можно в [консоли управления]({{ link-console-main }}) или с помощью команды [CLI](../../../cli/quickstart.md):

     ```bash
     yc compute placement-group list
     ```

{% endlist %}

## Смотрите также {see-also}

* [Как добавить виртуальную машину в группу размещения](add-vm.md).
* [Как создать виртуальную машину в группе размещения](create-vm-in-pg.md).