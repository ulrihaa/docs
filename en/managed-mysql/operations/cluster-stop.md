---
title: "Stopping and starting {{ MY }} clusters in {{ mmy-name }}"
description: "You can stop and restart a {{ MY }} database cluster, if you need to. You are not charged while your cluster is stopped: you continue to pay only for the storage size and backups."
---

# Stopping and starting {{ MY }} clusters

You can stop and restart a {{ MY }} DB cluster, if required. You are not charged while your cluster is stopped: you continue to pay only for the storage size and backups based on the [pricing policy](../pricing.md#prices-storage).

{% include [pricing-status-warning.md](../../_includes/mdb/pricing-status-warning.md) %}


## Stopping a cluster {#stop-cluster}

{% include [cluster-stop](../../_includes/mdb/cluster-stop.md) %}

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
   1. Select the cluster from the list, click ![options](../../_assets/horizontal-ellipsis.svg), and select **{{ ui-key.yacloud.mdb.clusters.button_action-stop }}**.
   1. Confirm that you want to stop the cluster and click **{{ ui-key.yacloud.mdb.cluster.stop-dialog.popup-confirm_button }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   1. See the description of the CLI cluster stop command:

      ```bash
      {{ yc-mdb-my }} cluster stop --help
      ```

   1. To get a cluster ID and name, request the list of clusters in the folder:

      ```bash
      {{ yc-mdb-my }} cluster list
      ```

   1. To stop a cluster, run the command:

      ```bash
      {{ yc-mdb-my }} cluster stop <cluster_name_or_ID>
      ```

- API

   To stop a cluster, use the [stop](../api-ref/Cluster/stop.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Stop](../api-ref/grpc/cluster_service.md#Stop) gRPC API call and provide the cluster ID in the `clusterId` request parameter.

   {% include [note-api-get-cluster-id](../../_includes/mdb/mmy/note-api-get-cluster-id.md) %}

{% endlist %}

## Starting a cluster {#start-cluster}

You can restart **STOPPED** clusters.

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mysql }}**.
   1. Select the stopped cluster from the list, click ![options](../../_assets/horizontal-ellipsis.svg), and select **{{ ui-key.yacloud.mdb.clusters.button_action-start }}**.
   1. Confirm that you want to start the cluster: click **{{ ui-key.yacloud.mdb.cluster.start-dialog.popup-confirm_button }}** in the dialog box that opens.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   1. See the description of the CLI cluster start command:

      ```bash
      {{ yc-mdb-my }} cluster start --help
      ```

   1. To get a cluster ID and name, request the list of clusters in the folder:

      ```bash
      {{ yc-mdb-my }} cluster list
      ```

   1. To start a cluster, run the command:

      ```bash
      {{ yc-mdb-my }} cluster start <cluster_name_or_ID>
      ```

- API

   To start a cluster, use the [start](../api-ref/Cluster/start.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Start](../api-ref/grpc/cluster_service.md#Start) gRPC API call and provide the cluster ID in the `clusterId` request parameter.

   To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).

{% endlist %}
