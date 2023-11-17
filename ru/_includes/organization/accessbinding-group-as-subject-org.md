{% list tabs %}

- CLI

    {% include [cli-install](../cli-install.md) %}

    1. Назначьте [роль](../../iam/concepts/access-control/roles.md) для группы:

        ```bash
        yc organization-manager organization add-access-binding \
          --subject=group:<идентификатор_группы> \
          --role=<идентификатор_роли> \
          <идентификатор_организации>
        ```

    1. Проверьте, что запрошенные права были выданы:

        ```bash
        yc organization-manager organization list-access-bindings <идентификатор_организации>
        ```

        Ответ содержит список всех ролей, выданных пользователям и группам в организации:

        ```
        +------------------------------------------+--------------+----------------------+
        |                 ROLE ID                  | SUBJECT TYPE |      SUBJECT ID      |
        +------------------------------------------+--------------+----------------------+
        | organization-manager.admin               | userAccount  | ajev1p2345lj******** |
        | organization-manager.organizations.owner | userAccount  | ajev1p2345lj******** |
        | editor                                   | group        | ajev1p2345lj******** |
        | viewer                                   | group        | ajev1p2345lj******** |
        +------------------------------------------+--------------+----------------------+
        ```

- Terraform

    {% include [terraform-install](../terraform-install.md) %}

    1. Добавьте в конфигурационный файл параметры ресурса, укажите нужную роль и группу:

       ```
       resource "yandex_organizationmanager_organization_iam_member" "users-editors" {
         organization_id = "<идентификатор_облака>"
         role            = "<идентификатор_роли>"
         member          = "group:<идентификатор_группы>"
       }
       ```

       Где:

       * `organization_id` — [идентификатор облака](../../resource-manager/operations/cloud/get-id.md). Обязательный параметр.
       * `role` — назначаемая [роль](../../iam/concepts/access-control/roles.md). Обязательный параметр.
       * `member` — группа, которой назначается роль. Указывается в виде `group:<идентификатор_группы>`. Обязательный параметр.

       Более подробную информацию о параметрах ресурса `yandex_organizationmanager_organization_iam_member` см. в [документации провайдера]({{ tf-provider-resources-link }}/organizationmanager_organization_iam_member).


    1. Создайте ресурсы:

       {% include [terraform-validate-plan-apply](../../_tutorials/terraform-validate-plan-apply.md) %}

       После этого в указанном каталоге будут созданы все требуемые ресурсы. Проверить создание ресурса можно в [консоли управления]({{ link-console-main }}) или с помощью команды [CLI](../../cli/quickstart.md):

       ```bash
       yc resource-manager folder list-access-bindings <имя_или_идентификатор_папки>
       ```

{% endlist %}