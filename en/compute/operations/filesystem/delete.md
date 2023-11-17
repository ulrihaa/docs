# Deleting file storage

1. [Detach file storage](detach-from-vm.md) from all the VMs it is attached to.
1. Delete file storage in {{ compute-name }}:

   {% list tabs %}

   - Management console

     1. In the [management console]({{ link-console-main }}), select the folder where your file store is located.
     1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
     1. In the left-hand panel, select ![image](../../../_assets/compute/storage.svg) **{{ ui-key.yacloud.compute.switch_file-storages }}**.
     1. In the appropriate file storage row, click ![image](../../../_assets/options-grey.svg) and select **{{ ui-key.yacloud.common.delete }}**.
     1. In the window that opens, click **{{ ui-key.yacloud.common.delete }}**.

   - {{ TF }}

      {% include [terraform-install](../../../_includes/terraform-install.md) %}

      1. Open the {{ TF }} configuration file and delete the fragment with the storage description:

         {% cut "Sample storage description in the {{ TF }} configuration" %}

         ```hcl
         ...
         resource "yandex_compute_filesystem" "default" {
           name  = "fs-name"
           type  = "network-ssd"
           zone  = "{{ region-id }}-a"
           size  = 150
         }
         ...
         ```

         {% endcut %}

      1. Apply the changes:

         {% include [terraform-validate-plan-apply](../../../_tutorials/terraform-validate-plan-apply.md) %}

      You can check the storage deletion using the [management console]({{ link-console-main }}) or this [CLI](../../../cli/quickstart.md) command:

      ```bash
      yc compute filesystem list
      ```

   - API

      Use the [delete](../../api-ref/Filesystem/delete.md) REST API method for the [Filesystem](../../api-ref/Filesystem/index.md) resource or the [FilesystemService/Delete](../../api-ref/grpc/filesystem_service.md#Delete) gRPC API call.

   {% endlist %}