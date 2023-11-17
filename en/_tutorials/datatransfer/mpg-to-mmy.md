You can set up data transfer from {{ mpg-name }} to {{ mmy-name }} databases using {{ data-transfer-name }}. To do this:

1. [Prepare the test data](#prepare-data).
1. [Prepare and activate the transfer](#prepare-transfer).
1. [Test the transfer](#verify-transfer).

If you no longer need the resources you created, [delete them](#clear-out).

## Getting started {#before-you-begin}

Prepare the infrastructure:

{% list tabs %}

- Manually

    1. [Create a {{ mpg-name }} source cluster](../../managed-postgresql/operations/cluster-create.md#create-cluster) in any [availability zone](../../overview/concepts/geo-scope.md) with publicly available hosts in any suitable configuration with the following settings:

        * **{{ ui-key.yacloud.mdb.forms.database_field_name }}**: `mpg_db`
        * **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}**: `mpg_user`
        * **{{ ui-key.yacloud.mdb.forms.database_field_user-password }}**: `<source_password>`

    1. [Grant](../../managed-postgresql/operations/grant.md#grant-privilege) the `mdb_replication` role to the `mpg_user`.

    1. In the same availability zone, [create a {{ mmy-name }} target cluster](../../managed-mysql/operations/cluster-create.md#create-cluster) with publicly available hosts in any suitable configuration and the following settings:

        * **{{ ui-key.yacloud.mdb.forms.database_field_name }}**: `mmy_db`
        * **{{ ui-key.yacloud.mdb.forms.database_field_user-login }}**: `mmy_user`
        * **{{ ui-key.yacloud.mdb.forms.database_field_user-password }}**: `<target_password>`

    1. Make sure that the cluster security groups have been set up correctly and allow connecting to them:

        * [{{ mpg-name }}](../../managed-postgresql/operations/connect.md#configuring-security-groups)
        * [{{ mmy-name }}](../../managed-mysql/operations/connect.md#configuring-security-groups)

- Using {{ TF }}

    1. If you do not have {{ TF }} yet, [install and configure it](../../tutorials/infrastructure-management/terraform-quickstart.md#install-terraform).
    1. Download the [file with provider settings](https://github.com/yandex-cloud/examples/tree/master/tutorials/terraform/provider.tf). Place it in a separate working directory and [specify the parameter values](../../tutorials/infrastructure-management/terraform-quickstart.md#configure-provider).
    1. Download the [postgresql-mysql.tf](https://github.com/yandex-cloud/examples/tree/master/tutorials/terraform/datatransfer/postgresql-mysql.tf) configuration file to the same working directory.

        This file describes:

        * [Networks](../../vpc/concepts/network.md#network) and [subnets](../../vpc/concepts/network.md#subnet) for hosting the clusters.
        * [Security groups](../../vpc/concepts/security-groups.md) for making cluster connections.
        * {{ mpg-name }} source cluster.
        * {{ mmy-name }} target cluster.
        * Source and target endpoints.
        * Transfer.

    1. In the `postgresql-mysql.tf` file, specify:

        * {{ MY }} and {{ PG }} versions.
        * {{ MY }} and {{ PG }} user passwords.

    1. Run the `terraform init` command in the directory with the configuration file. This command initializes the provider specified in the configuration files and enables you to use the provider resources and data sources.
    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

{% endlist %}

## Prepare the test data {#prepare-data}

1. [Connect to the {{ mpg-name }} source cluster database](../../managed-postgresql/operations/connect.md).

1. Add test data to the database. As an example, we will use a simple table with information transmitted by car sensors.

    Create a table:

    ```sql
    CREATE TABLE measurements (
        device_id varchar(200) NOT NULL,
        datetime timestamp NOT NULL,
        latitude real NOT NULL,
        longitude real NOT NULL,
        altitude real NOT NULL,
        speed real NOT NULL,
        battery_voltage real,
        cabin_temperature real NOT NULL,
        fuel_level real,
        PRIMARY KEY (device_id)
    );
    ```

    Populate the table with data:

    ```sql
    INSERT INTO measurements VALUES
    ('iv9a94th6rzt********', '2022-06-05 17:27:00', 55.70329032, 37.65472196,  427.5,    0, 23.5, 17, NULL),
    ('rhibbh3y08qm********', '2022-06-06 09:49:54', 55.71294467, 37.66542005, 429.13, 55.5, NULL, 18, 32);
    ```

## Prepare and activate the transfer {#prepare-transfer}

{% list tabs %}

- Manually

    1. [Create a source endpoint](../../data-transfer/operations/endpoint/source/postgresql.md) of the `{{ PG }}` type and specify the cluster connection parameters in it:

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnection.connection_type.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnectionType.mdb_cluster_id.title }}`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnectionType.mdb_cluster_id.title }}**: `<name_of_{{ PG }}_source_cluster>` from the drop-down list
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnection.database.title }}**: `mpg_db`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnection.user.title }}**: `mpg_user`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.postgres.console.form.postgres.PostgresConnection.password.title }}**: `<user_password>`

    1. [Create a target endpoint](../../data-transfer/operations/endpoint/target/mysql.md) of the `{{ MY }}` type and specify the cluster connection parameters in it:

        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnection.connection_type.title }}**: `{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnectionType.mdb_cluster_id.title }}`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnectionType.mdb_cluster_id.title }}**: `<name_of_{{ MY }}_target_cluster>` from the drop-down list
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnection.database.title }}**: `mmy_db`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnection.user.title }}**: `mmy_user`
        * **{{ ui-key.yc-data-transfer.data-transfer.console.form.mysql.console.form.mysql.MysqlConnection.password.title }}**: `<user_password>`

    1. [Create a transfer](../../data-transfer/operations/transfer.md#create) of the **_{{ ui-key.yc-data-transfer.data-transfer.console.form.transfer.console.form.transfer.TransferType.snapshot_and_increment.title }}_** type that will use the created endpoints.
    1. [Activate the transfer](../../data-transfer/operations/transfer.md#activate) and wait for its status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-RUNNING }}**.

- Using {{ TF }}

    1. In the `postgresql-mysql.tf` file, set the `transfer_enabled` parameter to `1`.

    1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

    1. The transfer is activated automatically. Wait for its status to change to **{{ ui-key.yacloud.data-transfer.label_connector-status-RUNNING }}**.

{% endlist %}

{% note info %}

If the transfer activation fails with the `Unable to push DDL` error:

1. [Connect to the target database](../../managed-mysql/operations/connect.md) and create in it an empty table named `measurements` similar to the source table.
1. Restart the transfer.

{% endnote %}

## Test the transfer {#verify-transfer}

To check if the transfer performs properly, test the copy and replication processes.

### Test the copy process {#verify-copy}

1. [Connect to the {{ mmy-name }} target cluster database](../../managed-mysql/operations/connect.md).
1. Make sure the source table has been transferred to the target database. For this, run the following query:

   ```sql
   SELECT * FROM measurements;
   ```

### Test the replication process {#verify-replication}

1. [Connect to the {{ mpg-name }} source cluster database](../../managed-postgresql/operations/connect.md).
1. Add data to the `measurements` table:

   ```sql
   INSERT INTO measurements VALUES
   ('iv7b74th678t********', '2020-06-08 17:45:00', 53.70987913, 36.62549834, 378.0, 20.5, 5.3, 20, NULL);
   ```

1. Make sure the new row has been added to the target database:

   1. [Connect to the {{ mmy-name }} target cluster database](../../managed-mysql/operations/connect.md).
   1. Run the following query:

      ```sql
      SELECT * FROM measurements;
      ```

## Delete the resources you created {#clear-out}

{% note info %}

Before deleting the created resources, [disable the transfer](../../data-transfer/operations/transfer.md#deactivate).

{% endnote %}

Some resources are not free of charge. To avoid paying for them, delete the resources you no longer need:

{% list tabs %}

- Manually

    * [Transfer](../../data-transfer/operations/transfer.md#delete)
    * [Endpoints](../../data-transfer/operations/endpoint/index.md#delete)
    * [{{ mmy-name }} cluster](../../managed-mysql/operations/cluster-delete.md)
    * [{{ mpg-name }} cluster](../../managed-postgresql/operations/cluster-delete.md)

- Using {{ TF }}

    If you created your resources using {{ TF }}:

    1. In the terminal window, go to the directory containing the infrastructure plan.
    1. Delete the `postgresql-mysql.tf` configuration file.
    1. Make sure the {{ TF }} configuration files are correct using this command:

      ```bash
      terraform validate
      ```

      If there are any errors in the configuration files, {{ TF }} will point them out.

    1. Confirm updating the resources.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      All the resources described in the `postgresql-mysql.tf` configuration file will be deleted.

{% endlist %}
