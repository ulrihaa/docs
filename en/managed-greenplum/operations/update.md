# Changing {{ GP }} cluster settings

After creating a cluster, you can:

* [{#T}](#change-name-and-description)

* [{#T}](#change-public-access)

* [{#T}](#change-additional-settings)

* [{#T}](#change-gp-settings)

* [{#T}](#change-disk-size)

* [Configure {{ GP }} servers according to the {{ GP }} documentation](#change-gp-settings).

To move a cluster to a different availability zone, [restore it from a backup](cluster-backups.md#restore). While restoring the cluster, specify a new availability zone. You will thus move the cluster hosts.

## Change the cluster name and description {#change-name-and-description}

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
   1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** at the top of the page.
   1. Under **{{ ui-key.yacloud.mdb.forms.section_base }}**, enter a new name and description for the cluster.
   1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To change the name and description of a {{ GP }} cluster:

   1. View the current name (`name`) and description (`description`) of the cluster:

      ```bash
      {{ yc-mdb-gp }} cluster get <cluster_name_or_ID>
      ```

   1. View a description of the update cluster configuration CLI command:

      ```bash
      {{ yc-mdb-gp }} cluster update --help
      ```

   1. Enter a new name and description for the cluster:

      ```bash
      {{ yc-mdb-gp }} cluster update <cluster_name_or_ID> \
         --new-name <new_cluster_name> \
         --description <new_cluster_description>
      ```

- API

   To change a cluster's name and description, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) gRPC API call and provide the following in the request:

   * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).
   * A new name in the `name` parameter.
   * A new description in the `description` parameter.
   * List of cluster configuration fields to update in the `updateMask` parameter (in this case, `name` and `description`).

   {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Change the public access setting {#change-public-access}

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ mgp-name }}**.
   1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** at the top of the page.
   1. Under **{{ ui-key.yacloud.mdb.forms.section_network-settings }}**, enable or disable **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}**.
   1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To change the public access setting {{ GP }}:

   1. View a description of the update cluster configuration CLI command:

      ```bash
      {{ yc-mdb-gp }} cluster update --help
      ```

   1. Configure public access in the `--assign-public-ip` parameter:

      ```bash
      {{ yc-mdb-gp }} cluster update <cluster_name_or_ID> \
         --assign-public-ip=<public_access_to_cluster>
      ```

      Where `assign-public-ip` is public access to the cluster, true or false.

- API

   Use the [update](../api-ref/Cluster/update.md) API method and include the following in the request:

   * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).
   * Public access setting in the `config.assignPublicIp` parameter.
   * List of cluster configuration fields to update in the `updateMask` parameter (in this case, `name` and `description`).

   {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

{% note tip %}

If you enabled public access to the cluster but cannot access it from the internet:

* Check the [security group settings](./connect#configuring-security-groups).
* Wait a while. It may take some time to enable public access.

{% endnote %}

## Changing additional cluster settings {#change-additional-settings}

{% list tabs %}

- Management console

   1. Go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
   1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** in the top panel.
   1. Change additional cluster settings:

      * {% include [Backup time](../../_includes/mdb/console/backup-time.md) %}
      * **{{ ui-key.yacloud.mdb.forms.maintenance-window-type }}**: [Maintenance window](../concepts/maintenance.md) settings:

         {% include [Maintenance window](../../_includes/mdb/console/maintenance-window-description.md) %}

      * {% include [Datalens access](../../_includes/mdb/console/datalens-access.md) %}
      * {% include [DataTransfer access](../../_includes/mdb/console/datatransfer-access.md) %}
      * {% include [Deletion protection](../../_includes/mdb/console/deletion-protection.md) %}

         {% include [Deletion protection limits](../../_includes/mdb/deletion-protection-limits-db.md) %}

      * **{{ ui-key.yacloud.greenplum.section_cloud-storage }}**: Activates the {{ yandex-cloud }} [{{ YZ }} extension](https://github.com/yezzey-gp/yezzey/). This extension is used to export [AO and AOCO tables](../tutorials/yezzey.md) from disks within the {{ mgp-name }} cluster to cold storage in {{ objstorage-full-name }}. This way, the data will be stored in a service bucket in a compressed and encrypted form. This is a [more cost-efficient storage method](../../storage/pricing.md).

         You cannot disable this option after you save your cluster settings.

         
         {% note info %}

         This functionality is at the [Preview](../../overview/concepts/launch-stages.md) stage and is free of charge.

         {% endnote %}


      * **{{ ui-key.yacloud.mdb.forms.section_pooler }}**: Operation mode and parameters of the [connection pooler](../concepts/pooling.md):

         {% include [Pooling mode](../../_includes/mdb/mgp/pooling-mode.md) %}

   1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To change additional cluster settings:

   1. View a description of the update cluster CLI command:

      ```bash
      {{ yc-mdb-gp }} cluster update --help
      ```

   1. Run the following command with a list of settings to update:

      ```bash
      {{ yc-mdb-gp }} cluster update <cluster_name_or_ID> \
          --backup-window-start <backup_start_time> \
          --datalens-access=<access_from_DataLens> \
          --datatransfer-access=<access_from_Data_Transfer> \
          --maintenance-window type=<maintenance_type>,`
                              `day=<day_of_week>,`
                              `hour=<hour> \
          --assign-public-ip=<public_access_to_cluster> \
          --deletion-protection=<cluster_deletion_protection> \
      ```

   You can change the following settings:

   {% include [backup-window-start](../../_includes/mdb/cli/backup-window-start.md) %}

   * `--datalens-access`: Enables [{{ datalens-full-name }}](../../datalens/concepts/index.md) access. The default value is `false`.

   * `--datatransfer-access`: Enables [{{ data-transfer-full-name }}](../../data-transfer/) access. The default value is `false`.

   * `--maintenance-window`: Settings for the [maintenance window](../concepts/maintenance.md) (including those for disabled clusters), where `type` is the maintenance type:

      {% include [maintenance-window](../../_includes/mdb/cli/maintenance-window-description.md) %}

   * `--assign-public-ip`: Cluster accessibility from the internet.

   * {% include [Deletion protection](../../_includes/mdb/cli/deletion-protection.md) %}

      {% include [Deletion protection limits db](../../_includes/mdb/deletion-protection-limits-db.md) %}

   You can [retrieve the cluster name with a list of clusters in the folder](cluster-list.md#list-clusters).

- API

   To change additional cluster settings, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) gRPC API call and provide the following in the request:

   * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).
   * Public access settings in the `config.assignPublicIp` parameter.
   * Backup window settings in the `config.backupWindowStart` parameter.
   * Settings for access from [{{ datalens-full-name }}](../../datalens/concepts/index.md), in the `config.access.dataLens` parameter.
   * Settings for the [maintenance window](../concepts/maintenance.md) (including those for disabled clusters) in the `maintenanceWindow` parameter.
   * Cluster deletion protection settings in the `deletionProtection` parameter.

      {% include [Deletion protection limits](../../_includes/mdb/deletion-protection-limits-db.md) %}

   * List of cluster configuration fields to update in the `UpdateMask` parameter.

   {% include [note-api-updatemask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

## Changing {{ GP }} settings {#change-gp-settings}

You can change the DBMS settings of the hosts in your cluster.

{% list tabs %}

- Management console

   1. Go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
   1. Select the cluster and click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}** in the top panel.
   1. Change the [{{ GP }} settings](../concepts/settings-list.md) by clicking **{{ ui-key.yacloud.mdb.forms.button_configure-settings }}** under **{{ ui-key.yacloud.mdb.forms.section_settings }}**.
   1. Click **{{ ui-key.yacloud.component.mdb.settings.popup_settings-submit }}**.
   1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To change the [{{ GP }} DBMS settings](../concepts/settings-list.md):

   1. View the full list of settings specified for the cluster:

      ```bash
      {{ yc-mdb-gp }} cluster get <cluster_name_or_ID>
      ```

   1. View a description of the update cluster configuration CLI command:

      ```bash
      {{ yc-mdb-gp }} cluster update-config --help
      ```

   1. Set the required parameter values:

      All supported parameters are listed in the [request format for the update method](../api-ref/Cluster/update.md), in the `greenplumConfig_<Greenplum_version>` field. To specify a parameter name in the CLI call, convert the name from <q>lowerCamelCase</q> to <q>snake_case</q>. For example, the `maxConnections` parameter from an API call must be converted to `max_connections` for the CLI command:

      ```bash
      {{ yc-mdb-gp }} cluster update-config <cluster_name_or_ID> \
         --set <parameter_1_name>=<value1>,<parameter_2_name>=<value2>,...
      ```

      {{ mgp-short-name }} runs the update cluster settings operation.

- API

   To change {{ GP }} settings, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) gRPC API call and provide the following in the request:

   * New settings in the `configSpec.greenplumConfig_<version>` parameter.
   * List of cluster configuration fields to be updated in the `updateMask` parameter.

      {% include [note-api-updatemask](../../_includes/note-api-updatemask.md) %}

{% endlist %}

{% include [greenplum-trademark](../../_includes/mdb/mgp/trademark.md) %}

## Increasing storage size {#change-disk-size}

{% include [note-increase-disk-size](../../_includes/mdb/note-increase-disk-size.md) %}

{% list tabs %}

- Management console

   To increase the cluster storage size:

   1. In the [management console]({{ link-console-main }}), select the folder with the cluster you need.
   1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-greenplum }}**.
   1. Select the cluster you need.
   1. At the top of the page, click **{{ ui-key.yacloud.mdb.cluster.overview.button_action-edit }}**.
   1. Edit the settings in the **{{ ui-key.yacloud.mdb.forms.section_storage }}** section.
   1. Click **{{ ui-key.yacloud.common.save }}**.

- API

   To increase the cluster storage size, use the [update](../api-ref/Cluster/update.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Update](../api-ref/grpc/cluster_service.md#Update) gRPC API call and provide the following in the request:

   * Cluster ID in the `clusterID` parameter. To find out the cluster ID, [get a list of clusters in the folder](./cluster-list.md#list-clusters).
   * New master and segment host storage size in the `masterConfig.resources.diskSize` and `segmentConfig.resources.diskSize` parameters.
   * List of cluster configuration fields to update in the `UpdateMask` parameter.

   {% include [Note API updateMask](../../_includes/note-api-updatemask.md) %}

{% endlist %}
