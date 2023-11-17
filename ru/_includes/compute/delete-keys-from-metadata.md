{% list tabs %}

- Консоль управления

  1. В [консоли управления]({{ link-console-main }}) выберите каталог, которому принадлежит ВМ.
  1. Выберите сервис **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
  1. Нажмите на имя нужной ВМ.
  1. В правом верхнем углу страницы нажмите ![image](../../_assets/pencil.svg) **{{ ui-key.yacloud.compute.instance.overview.button_action-edit }}**.
  1. Раскройте меню **{{ ui-key.yacloud.common.metadata }}** и удалите ключи, нажав ![image](../../_assets/cross.svg).

- CLI

  {% include [cli-install](../cli-install.md) %}

  {% include [default-catalogue](../default-catalogue.md) %}

  1. Посмотрите описание команды CLI для удаления метаданных:

      ```bash
      yc compute instance remove-metadata --help
      ```

  1. Удалите ключи:

      ```bash
      yc compute instance remove-metadata <идентификатор_ВМ> --keys <имя_SSH-ключа>
      ```

- API

  Чтобы удалить SSH-ключи из метаданных ВМ, воспользуйтесь методом REST API [updateMetadata](../../compute/api-ref/Instance/updateMetadata.md) для ресурса [Instance](../../compute/api-ref/Instance/) или вызовом gRPC API [InstanceService/UpdateMetadata](../../compute/api-ref/grpc/instance_service.md#UpdateMetadata).

  В запросе передайте параметр `delete` с SSH-ключом.

  **Пример запроса для REST API**

  ```bash
  curl -X POST -H "Authorization: Bearer <IAM-токен>" \
    -d '{"delete":["<имя_SSH-ключа>"]}' https://compute.api.cloud.yandex.net/compute/v1/instances/<идентификатор_ВМ>/updateMetadata
  ```

{% endlist %}
