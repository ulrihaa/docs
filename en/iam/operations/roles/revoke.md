# Revoke a role for a resource

If you want to prevent a [subject](../../concepts/access-control/index.md#subject) from accessing a resource, revoke the relevant roles for this resource and for resources that grant inherited access rights. For more information, see [{#T}](../../concepts/access-control/index.md).


## Revoking a role {#revoke-one-role}

{% list tabs %}

- Management console

   In the management console, you can only revoke a cloud or a folder role.

   * To revoke a role only in the folder:

      {% include [update-access-binding-user-console-folder](../../../_includes/resource-manager/update-access-binding-user-console-folder.md) %}

   * To revoke a role in the cloud:

      {% include [update-access-binding-user-console-cloud](../../../_includes/resource-manager/update-access-binding-user-console-cloud.md) %}

   * To revoke all the folder or cloud roles at once:

      1. On the management console [start page]({{ link-console-main }}), select a folder or a cloud.
      1. Go to the **{{ ui-key.yacloud.common.resource-acl.label_access-bindings }}** tab.
      1. Select a user from the list and click ![image](../../../_assets/options.svg) next to the username.
      1. If you want to revoke all of the user's roles in the cloud, click **{{ ui-key.yacloud.common.resource-acl.button_remove-bindings }}** and confirm the revocation.

- CLI

   {% include [cli-install](../../../_includes/cli-install.md) %}

   To revoke a role from a subject, delete the corresponding access binding for the appropriate resource:

   1. {% include [list-access-bindings-via-cli](../../../_includes/iam/list-access-bindings-via-cli.md) %}
   1. To delete an access binding, run:

      ```bash
      yc <service-name> <resource> remove-access-binding <resource-name>|<resource-id> \
          --role <role-id> \
          --subject <subject-type>:<subject-id>
      ```

      Where:
      * `<role-id>`: The ID of the role to revoke (such as `{{ roles-cloud-owner }}`).
      * `<subject-type>`: The [subject](../../concepts/access-control/index.md#subject) type to revoke a role from.
      * `<subject-id>`: The subject ID.

- API

   To revoke a resource role from a subject, delete the corresponding access binding:

   1. {% include [include](../../../_includes/iam/list-access-bindings-via-api.md) %}
   1. Create a request body, for example, in a `body.json` file. In the request body, specify which access binding to delete. For example, revoke the `editor` role from user `ajei8n54hmfhuk5nog0g`:

      **body.json:**
      ```json
      {
          "accessBindingDeltas": [{
              "action": "REMOVE",
              "accessBinding": {
                  "roleId": "editor",
                  "subject": {
                      "id": "ajei8n54hmfhuk5nog0g",
                      "type": "userAccount"
                      }
                  }
              }
          ]
      }
      ```


    1. Revoke the role by deleting the specified access binding:

        {% include [grant-role-folder-via-curl](../../../_includes/iam/grant-role-folder-via-curl.md) %}

- {{ TF }}

   {% include [terraform-install](../../../_includes/terraform-install.md) %}

   1. To revoke a resource role from a subject, find the resource description in the configuration file:


        ```
        resource "yandex_resourcemanager_cloud_iam_binding" "admin" {
            cloud_id    = "<cloud ID>"
            role        = "<role>"
            members     = [
            "serviceAccount:<service account ID>",
            "userAccount:<user ID>",
            ]
        }
        ```

    1. Delete the entry with information about the subject which rights are to be revoked from the `members` list of users.

       For more information about the parameters of the `yandex_resourcemanager_cloud_iam_binding` resource, see the [provider documentation]({{ tf-provider-resources-link }}/iam_service_account_iam_binding).

    1. Make sure the configuration files are valid.

        1. In the command line, go to the directory where you created the configuration file.
        1. Run the check using the command:

          ```
          terraform plan
          ```

       If the configuration is described correctly, the terminal displays a list of created resources and their parameters. If the configuration contains any errors, {{ TF }} will point them out.

    1. Deploy cloud resources.

        1. If the configuration contains no errors, run the command:

           ```
           terraform apply
           ```

        1. Confirm creating the resources: type `yes` in the terminal and press **Enter**.

        All the resources you need will then be created in the specified folder. You can check if the resource is there either from the [management console]({{ link-console-main }}) or using this [CLI](../../../cli/quickstart.md) command:

           ```
           yc resource-manager cloud list-access-bindings <cloud name>|<cloud ID>
           ```

{% endlist %}
