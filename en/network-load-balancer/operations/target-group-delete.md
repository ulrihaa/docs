---
title: "Deleting a target group in the load balancer"
description: "Before deleting a target group, detach it from the network load balancer. You cannot restore a target group after it is deleted. Open the Load Balancer section in the folder you want to delete the target group from. In the menu that opens, click Delete."
---

# Delete a {{ network-load-balancer-name }} target group

{% note alert %}

Before deleting a target group, detach it from the network load balancer. You cannot restore a target group after it is deleted.

{% endnote %}

{% list tabs %}

- Management console

   To delete a [target group](../concepts/target-resources.md):
   1. In the [management console]({{ link-console-main }}), select the folder to delete a target group from.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_load-balancer }}**.
   1. In the left-hand panel, select ![image](../../_assets/trgroups.svg) **{{ ui-key.yacloud.load-balancer.target-group.label_list }}**.
   1. Click ![image](../../_assets/horizontal-ellipsis.svg) in the line of the target group to delete.
   1. In the menu that opens, select **{{ ui-key.yacloud.common.delete }}**.
   1. In the window that opens, click **{{ ui-key.yacloud.common.delete }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   1. See the description of the CLI's delete target group command:

      ```bash
      yc load-balancer target-group delete --help
      ```

   1. Delete the target group from the default folder:

      ```bash
      yc load-balancer target-group delete <target group ID or name>
      ```

      You can get the target group ID and name with a [list of target groups in the folder](target-group-list.md#list).

- {{ TF }}

   {% include [terraform-definition](../../_tutorials/terraform-definition.md) %}

   {% include [terraform-install](../../_includes/terraform-install.md) %}

   To delete a target group created with {{ TF }}:
   1. Open the {{ TF }} configuration file and delete the fragment with the target group description.

      ```hcl
      resource "yandex_lb_target_group" "foo" {
        name      = "<target group name>"
        target {
          subnet_id = "<subnet ID>"
          address   = "<internal IP address of resource>"
        }
        target {
          subnet_id = "<subnet ID>"
          address   = "<internal IP address of resource 2>"
        }
      }
      ```

   1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

   1. Delete the network load balancer.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

- API

   To delete a target group, use the [delete](../api-ref/TargetGroup/delete.md) REST API method for the [TargetGroup](../api-ref/TargetGroup/index.md) resource or the [TargetGroupService/Delete](../api-ref/grpc/target_group_service.md#Delete) gRPC API call.

   You can get the target group ID with a [list of target groups in the folder](target-group-list.md#list).

{% endlist %}