{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder the VM belongs to.
   1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
   1. In the line with the appropriate VM, click ![image](../../_assets/options.svg), and then click **{{ ui-key.yacloud.common.delete }}**.

      To delete multiple VMs, select the VMs you need to delete in the list and click **{{ ui-key.yacloud.compute.instances.button_gr-action-delete }}** at the bottom of the screen.

   1. In the window that opens, click **{{ ui-key.yacloud.compute.instances.popup-confirm_button_delete }}**.

- CLI

   {% include [cli-install](../cli-install.md) %}

   {% include [default-catalogue](../default-catalogue.md) %}

   1. View a description of the CLI instance delete command below:

      ```bash
      yc compute instance delete --help
      ```

   1. Get a list of VMs in the default folder:

      {% include [compute-instance-list](../../compute/_includes_service/compute-instance-list.md) %}

   1. Select the VM `ID` or `NAME` (for example, `first-instance`).
   1. Delete the VM instance:

      ```bash
      yc compute instance delete first-instance
      ```

- API

   Use the [delete](../../compute/api-ref/Instance/delete.md) REST API method for the [Instance](../../compute/api-ref/Instance/) resource or the [InstanceService/Delete](../../compute/api-ref/grpc/instance_service.md#Delete) gRPC API call.

- {{ TF }}

   {% include [terraform-install](../terraform-install.md) %}

   If you created an instance using {{ TF }}, you can delete it:
   1. In the command line, go to the directory with the {{ TF }} configuration file.
   1. Delete the resources using this command:

      ```bash
      terraform destroy
      ```

      {% note alert %}

      {{ TF }} deletes all the resources that you created in the current configuration, such as clusters, networks, subnets, and instances.

      {% endnote %}

   1. Type `yes` and press **Enter**.

{% endlist %}
