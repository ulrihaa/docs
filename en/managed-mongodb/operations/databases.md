# Database management in {{ mmg-name }}

You can add and remove databases, as well as view information about them.

## Getting a list of cluster databases {#list-db}

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mongodb }}**.
   1. Click the cluster name and select the **{{ ui-key.yacloud.mongodb.cluster.switch_databases }}** tab.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To get a list of databases in a cluster, run the command:

   ```bash
   {{ yc-mdb-mg }} database list
      --cluster-name <cluster_name>
   ```

   You can get the cluster name with a [list of clusters in the folder](cluster-list.md#list-clusters).

- API

   To get a list of cluster databases, use the [list](../api-ref/Database/list.md) REST API method for the [Database](../api-ref/Database/index.md) resource or the [DatabaseService/List](../api-ref/grpc/database_service.md#List) gRPC API call and provide the cluster ID in the `clusterId` request parameter.

   You can get the cluster ID with a [list of clusters in the folder](cluster-list.md#list-clusters).

{% endlist %}

## Creating a database {#add-db}

{% include [1000 DBs limit](../../_includes/mdb/1000dbnote.md) %}

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mongodb }}**.
   1. Click the cluster name.
   1. Select the **{{ ui-key.yacloud.mongodb.cluster.switch_databases }}** tab.
   1. Click **{{ ui-key.yacloud.mdb.cluster.databases.button_add }}**.
   1. Enter the database name and click **{{ ui-key.yacloud.mdb.dialogs.popup-add-db_button_add }}**.

      {% include [db-name-limits](../../_includes/mdb/mmg/note-info-db-name-limits.md) %}

   1. [Authorize](cluster-users.md#updateuser) the appropriate cluster users for access to the database created.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   Run the create database command and set the name of the new database:

   ```bash
   {{ yc-mdb-mg }} database create <DB_name>
     --cluster-name <cluster_name>
   ```

   {% include [db-name-limits](../../_includes/mdb/mmg/note-info-db-name-limits.md) %}

   You can get the cluster name with a [list of clusters in the folder](cluster-list.md#list-clusters).

   {{ mmg-short-name }} runs the create database operation.

   [Authorize](cluster-users.md#updateuser) the appropriate cluster users for access to the database created.

- {{ TF }}

   1. Open the current {{ TF }} configuration file with an infrastructure plan.

      For more information about creating this file, see [{#T}](cluster-create.md).

   1. Add a `database` block to the {{ mmg-name }} cluster description:

      ```hcl
      resource "yandex_mdb_mongodb_cluster" "<cluster_name>" {
        ...
        database {
          name = "<DB_name>"
        }
      }
      ```

      {% include [db-name-limits](../../_includes/mdb/mmg/note-info-db-name-limits.md) %}

   1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

   1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   For more information, see the [{{ TF }} provider documentation]({{ tf-provider-resources-link }}/mdb_mongodb_cluster).

   {% include [Terraform timeouts](../../_includes/mdb/mmg/terraform/timeouts.md) %}

- API

   To create a new database in a cluster, use the [create](../api-ref/Database/create.md) REST API method for the [Database](../api-ref/Database/index.md) resource or the [DatabaseService/Create](../api-ref/grpc/database_service.md#Create) gRPC API call and provide the following in the request:

   * ID of the cluster where you want to create a database, in the `clusterId` parameter. To retrieve the ID, [get a list of clusters in the folder](cluster-list.md#list-clusters).
   * Database settings in the `databaseSpec` parameter.

   To allow access to the created database, use the [update](../api-ref/User/update.md) REST API method for the [User](../api-ref/User/index.md) resource or the [UserService/Update](../api-ref/grpc/user_service.md#Update) gRPC API call.

{% endlist %}

## Deleting a database {#remove-db}

{% list tabs %}

- Management console

   1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mongodb }}**.
   1. Click the cluster name and open the **{{ ui-key.yacloud.mongodb.cluster.switch_databases }}** tab.
   1. Click the ![image](../../_assets/horizontal-ellipsis.svg) icon in the same row as the DB you need and select **{{ ui-key.yacloud.mdb.cluster.databases.button_action-remove }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To delete a database, run the command:

   ```bash
   {{ yc-mdb-mg }} database delete <DB_name>
      --cluster-name <cluster_name>
   ```

   You can get the cluster name with a [list of clusters in the folder](cluster-list.md#list-clusters).

- {{ TF }}

   1. Open the current {{ TF }} configuration file with an infrastructure plan.

      For more information about creating this file, see [{#T}](cluster-create.md).

   1. Delete the `database` block with the DB name from the {{ mmg-name }} cluster description.

   1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

   1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

   For more information, see the [{{ TF }} provider documentation]({{ tf-provider-resources-link }}/mdb_mongodb_cluster).

   {% include [Terraform timeouts](../../_includes/mdb/mmg/terraform/timeouts.md) %}

- API

   To delete a database, use the [delete](../api-ref/Database/delete.md) REST API method for the [Database](../api-ref/Database/index.md) resource or the [DatabaseService/Delete](../api-ref/grpc/database_service.md#Delete) gRPC API call and provide the following in the request:

   * Cluster ID in the `clusterId` parameter. To find out the cluster ID, [get a list of clusters in the folder](cluster-list.md).
   * Database name, in the `databaseName` parameter.

{% endlist %}

{% note warning %}

Before creating a new database with the same name, wait for the delete operation to complete, otherwise the database being deleted will be restored. Operation status can be obtained with a [list of cluster operations](cluster-list.md#list-operations).

{% endnote %}
