# Creating a {{ MG }} cluster

A {{ MG }} cluster consists of one or more database hosts you can configure replication between. Replication is enabled by default in any cluster consisting of more than one host (the primary host accepts write requests and asynchronously duplicates changes in the secondary hosts).


{% note info %}


* The number of hosts you can create together with a {{ MG }} cluster depends on the selected [disk type](../concepts/storage.md#storage-type-selection) and [host class](../concepts/instance-types.md#available-flavors).
* Available disk types [depend](../concepts/storage.md) on the selected [host class](../concepts/instance-types.md).


{% endnote %}



{% list tabs %}

- Management console

   To create a cluster:

   1. In the [management console]({{ link-console-main }}), select the folder where you want to create a DB cluster.

   1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-mongodb }}**.

   1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.

   1. Under **{{ ui-key.yacloud.mdb.forms.section_base }}**:

      * Enter a name in the **{{ ui-key.yacloud.mdb.forms.base_field_name }}** field. The cluster name must be unique within the folder.
      * (Optional) Enter a cluster **{{ ui-key.yacloud.mdb.forms.base_field_description }}**.
      * Select the environment where you want to create the cluster (you cannot change the environment once the cluster is created):

         * `PRODUCTION`: For stable versions of your apps.
         * `PRESTABLE`: For testing purposes. The prestable environment is similar to the production environment and also covered by the SLA but it is the first to receive new functionalities, improvements, and bug fixes. In the prestable environment, you can test compatibility of new versions with your application.

      * Specify the DBMS version.

   1. {% include [mmg-settings-host-class](../../_includes/mdb/mmg/settings-host-class.md) %}

   1. Under **{{ ui-key.yacloud.mdb.forms.section_disk }}**:

      * Select the [disk type](../concepts/storage.md).

         
         {% include [storages-step-settings](../../_includes/mdb/settings-storages.md) %}


      * Select the storage size to be used for data and backups. For more information about how backups take up storage space, see [{#T}](../concepts/backup.md).

   1. Under **{{ ui-key.yacloud.mdb.forms.section_database }}**, specify the DB attributes:

      * DB name

         {% include [db-name-limits](../../_includes/mdb/mmg/note-info-db-name-limits.md) %}

      * Username
      * User password; minimum 8 characters long

   
   1. Under **{{ ui-key.yacloud.mdb.forms.section_network }}**, select:

      * Cloud network for the cluster.
      * Security groups for the cluster network traffic. You may also need to [set up security groups](connect/index.md#configuring-security-groups) to connect to the cluster.


   1. Under **{{ ui-key.yacloud.mdb.forms.section_host }}**, add the DB hosts created with the cluster:

      
      * Click **{{ ui-key.yacloud.mdb.forms.button_add-host }}**.
      * Select the [availability zone](../../overview/concepts/geo-scope.md).
      * Select the [subnet](../../vpc/concepts/network.md#subnet) in the specified availability zone. If there is no subnet, create one.
      * If the host must be available outside {{ yandex-cloud }}, enable **{{ ui-key.yacloud.mdb.hosts.dialog.field_public_ip }}**. You cannot change this setting after you create a host.


      
      To ensure fault tolerance, you need at least 3 hosts for `local-ssd` and `network-ssd-nonreplicated` disk types. For more information, see [Storage](../concepts/storage.md).


      By default, hosts are created in different availability zones. For details, see the [host management](hosts.md).

   1. Configure additional cluster settings, if required:

      {% include [mmg-extra-settings](../../_includes/mdb/mmg-extra-settings.md) %}

   1. Configure the [DBMS settings](../concepts/settings-list.md#dbms-cluster-settings), if required.

      {% include [mmg-settings-dependence](../../_includes/mdb/mmg/note-info-settings-dependence.md) %}

   1. Click **{{ ui-key.yacloud.mdb.clusters.button_create }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To create a cluster:

   
   1. Check whether the folder has any subnets for the cluster hosts:

      ```bash
      yc vpc subnet list
      ```

      If there are no subnets in the folder, [create the required subnets](../../vpc/operations/subnet-create.md) in {{ vpc-short-name }}.


   1. View a description of the create cluster CLI command:

      ```bash
      {{ yc-mdb-mg }} cluster create --help
      ```

   1. Specify the cluster parameters in the create command (the example shows only some of the parameters):

      
      ```bash
      {{ yc-mdb-mg }} cluster create \
        --name <cluster_name> \
        --environment=<environment> \
        --network-name <network_name> \
        --host zone-id=<availability_zone>,`
              `subnet-id=<subnet_ID>,`
              `assign-public-ip=<public_access> \
        --mongod-resource-preset <host_class> \
        --user name=<username>,password=<user_password> \
        --database name=<DB_name> \
        --mongod-disk-type <disk_type> \
        --mongod-disk-size <storage_size_GB> \
        --performance-diagnostics=<enable_diagnostics> \
        --deletion-protection=<deletion_protection>
      ```

      You need to specify `subnet-id` if the selected availability zone has two or more subnets.


      Where:

      * `--environment`: `prestable` or `production`.

      
      * `--host`: Host parameters:
         * `zone-id`: [Availability zone](../../overview/concepts/geo-scope.md).
         * `subnet-id`: [Subnet ID](../../vpc/concepts/network.md#subnet). It must be specified if the selected availability zone includes two or more subnets.
         * `assign-public-ip`: Internet access to the host via a public IP, `true` or `false`.


      * `--performance-diagnostics`: Enable performance diagnostics for the cluster, `true` or `false`.
      * `--deletion-protection`: Cluster deletion protection, `true` or `false`.

      {% include [db-name-limits](../../_includes/mdb/mmg/note-info-db-name-limits.md) %}

      {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-db.md) %}

      {% note info %}

      When creating a cluster, the `anytime` [maintenance](../concepts/maintenance.md) mode is set by default. You can set a specific maintenance period when [updating the cluster settings](update.md#change-additional-settings).

      {% endnote %}

- {{ TF }}

   {% include [terraform-definition](../../_tutorials/terraform-definition.md) %}

   {% include [terraform-install](../../_includes/terraform-install.md) %}

   To create a cluster:

   1. In the configuration file, describe the parameters of the resources you want to create:

      * Database cluster: Description of the cluster and its hosts.

      * {% include [Terraform network description](../../_includes/mdb/terraform/network.md) %}

      * {% include [Terraform subnet description](../../_includes/mdb/terraform/subnet.md) %}

      Here is an example of the configuration file structure:

      
      
      ```hcl
      resource "yandex_mdb_mongodb_cluster" "<cluster_name>" {
        name                = "<cluster_name>"
        environment         = "<environment>"
        network_id          = "<network_ID>"
        security_group_ids  = [ "<list_of_security_group_IDs>" ]
        deletion_protection = <cluster_deletion_protection>

        cluster_config {
          version = "<{{ MG }}_version>"
        }

        database {
          name = "<DB_name>"
        }

        user {
          name     = "<username>"
          password = "<user_password>"
          permission {
            database_name = "<DB_name>"
            roles         = [ "<list_of_user_roles>" ]
          }
        }

        resources_mongod {
          resource_preset_id = "<host_class>"
          disk_type_id       = "<disk_type>"
          disk_size          = <storage_size_GB>
        }

        host {
          zone_id          = "<availability_zone>"
          subnet_id        = "<subnet_ID>"
          assign_public_ip = <public_access>
        }
      }

      resource "yandex_vpc_network" "<network_name>" { name = "<network_name>" }

      resource "yandex_vpc_subnet" "<subnet_name>" {
        name           = "<subnet_name>"
        zone           = "<availability_zone>"
        network_id     = "<network_ID>"
        v4_cidr_blocks = ["<range>"]
      }
      ```




      Where:

      * `environment`: Environment, `PRESTABLE` or `PRODUCTION`.

      
      * `host`: Host parameters:
         * `zone_id`: Availability zone.
         * `subnet_id`: ID of a subnet in the selected availability zone.
         * `assign_public_ip`: Public access to the host, `true` or `false`.


      * `deletion_protection`: Cluster deletion protection, `true` or `false`.
      * `version`: {{ MG }} version, {{ versions.tf.str }}.

      {% include [db-name-limits](../../_includes/mdb/mmg/note-info-db-name-limits.md) %}

      {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-db.md) %}

      {% include [Maintenance window](../../_includes/mdb/mmg/terraform/maintenance-window.md) %}

      For more information on resources that you can create with {{ TF }}, see the [provider documentation]({{ tf-provider-mmg }}).

   1. Make sure the settings are correct.

      {% include [terraform-validate](../../_includes/mdb/terraform/validate.md) %}

   1. Create a cluster.

      {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

      After this, all required resources will be created in the specified folder, and the [host FQDNs](../concepts/network.md#hostname) will be displayed in the terminal. You can check the new resources and their configuration using the [management console]({{ link-console-main }}).

      {% include [Terraform timeouts](../../_includes/mdb/mmg/terraform/timeouts.md) %}

- API

   To create a cluster, use the [create](../api-ref/Cluster/create.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Create](../api-ref/grpc/cluster_service.md#Create) gRPC API call and provide the following in the request:

   * ID of the folder where the cluster should be placed, in the `folderId` parameter.
   * Cluster name in the `name` parameter.
   * Cluster environment in the `environment` parameter.
   * Network ID in the `networkId` parameter.
   * Cluster configuration in the `configSpec` parameter.
   * Configuration of the cluster hosts in one or more `hostSpecs` parameters.

   
   * [Security group](../concepts/network.md#security-groups) identifiers in the `securityGroupIds` parameter.


   * Database configuration in one or more `databaseSpecs` parameters.

      {% include [db-name-limits](../../_includes/mdb/mmg/note-info-db-name-limits.md) %}

   * User settings in one or more `userSpecs` parameters.
   * Cluster deletion protection settings in the `deletionProtection` parameter.

      {% include [deletion-protection-limits-db](../../_includes/mdb/deletion-protection-limits-data.md) %}

{% endlist %}


{% note warning %}

If you specified security group IDs when creating a cluster, you may also need to [configure security groups](connect/index.md#configuring-security-groups) to connect to the cluster.

{% endnote %}


## Examples {#examples}

### Creating a single-host cluster {#creating-a-single-host-cluster}

{% list tabs %}

- CLI

   To create a cluster with a single host, provide a single `--host` parameter.

   Create a {{ mmg-name }} cluster with the following test characteristics:

   
   * Name: `mymg`.
   * Environment: `production`.
   * Network: `{{ network-name }}`.
   * Security group ID: `{{ security-group }}`.
   * `{{ host-class }}` host in the `b0rcctk2rvtr********` subnet in the `{{ region-id }}-a` availability zone: 1.
   * Network SSD storage (`{{ disk-type-example }}`): 20 GB.
   * User: `user1`, with the `user1user1` password.
   * Database: `db1`.
   * Protection against accidental cluster deletion: Enabled.


   Run the following command:

   
   ```bash
   {{ yc-mdb-mg }} cluster create \
     --name mymg \
     --environment production \
     --network-name {{ network-name }} \
     --security-group-ids {{ security-group }} \
     --mongod-resource-preset {{ host-class }} \
     --host zone-id={{ region-id }}-a,subnet-id=b0rcctk2rvtr******** \
     --mongod-disk-size 20 \
     --mongod-disk-type {{ disk-type-example }} \
     --user name=user1,password=user1user1 \
     --database name=db1 \
     --deletion-protection=true
   ```


- {{ TF }}

   Create a {{ mmg-name }} cluster and a network for it with the following test characteristics:

   * Name: `mymg`
   * Version: `{{ versions.tf.latest }}`
   * Environment: `PRODUCTION`
   * Cloud ID: `{{ tf-cloud-id }}`
   * Folder ID: `{{ tf-folder-id }}`
   * Network: `mynet`
   * Host class: `{{ host-class }}`
   * Number of `host` blocks: 1
   * Subnet: `mysubnet` Network settings:

      * Availability zone: `{{ region-id }}-a`
      * Range: `10.5.0.0/24`

   
   * Security group: `mymg-sg`. The group rules allow TCP connections to the cluster from the internet via port `{{ port-mmg }}`.


   * SSD network storage: `{{ disk-type-example }}`
   * Storage size: 20 GB
   * User: `user1`
   * Password: `user1user1`
   * Database: `db1`
   * Protection against accidental cluster deletion: Enabled

   Configuration file for a single-host cluster:

   
   
   ```hcl
   resource "yandex_mdb_mongodb_cluster" "mymg" {
     name                = "mymg"
     environment         = "PRODUCTION"
     network_id          = yandex_vpc_network.mynet.id
     security_group_ids  = [ yandex_vpc_security_group.mymg-sg.id ]
     deletion_protection = true

     cluster_config {
       version = "{{ versions.tf.latest }}"
     }

     database {
       name = "db1"
     }

     user {
       name     = "user1"
       password = "user1user1"
       permission {
         database_name = "db1"
       }
     }

     resources_mongod {
       resource_preset_id = "{{ host-class }}"
       disk_type_id       = "{{ disk-type-example }}"
       disk_size          = 20
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
     }
   }

   resource "yandex_vpc_network" "mynet" {
     name = "mynet"
   }

   resource "yandex_vpc_security_group" "mymg-sg" {
     name       = "mymg-sg"
     network_id = yandex_vpc_network.mynet.id

     ingress {
       description    = "MongoDB"
       port           = {{ port-mmg }}
       protocol       = "TCP"
       v4_cidr_blocks = [ "0.0.0.0/0" ]
     }
   }

   resource "yandex_vpc_subnet" "mysubnet" {
     name           = "mysubnet"
     zone           = "{{ region-id }}-a"
     network_id     = yandex_vpc_network.mynet.id
     v4_cidr_blocks = ["10.5.0.0/24"]
   }
   ```




{% endlist %}

### Creating sharded clusters {#creating-a-sharded-cluster}

You can create {{ mmg-name }} clusters with [standard](#std-sharding) or [advanced](#adv-sharding) sharding. For more information about sharding types, see [{#T}](../concepts/sharding.md#shard-management).

#### Standard sharding {#std-sharding}

Create a {{ mmg-name }} cluster and a network for it with multiple hosts:

* One `MONGOD` host.
* Three `MONGOINFRA` hosts.

Cluster test characteristics:

* Name: `mymg`.
* Environment: `PRODUCTION`.
* Protection against accidental cluster deletion: Enabled.
* Version: `{{ versions.tf.latest }}`.
* Database: `db1`.
* User: `user1`.
* Password: `user1user1`.
* `MONGOD` host class: `{{ host-class }}`.
* `MONGOINFRA` host class: `c3-c2-m4`.
* SSD network storage: `{{ disk-type-example }}`.
* Storage size: 10 GB.

Network characteristics:

* Network: `mynet`.
* Security group: `mymg-sg` with the `{{ security-group }}` ID. In {{ TF }}, a group is created with the rule allowing TCP connections to the cluster from the internet on port `{{ port-mmg }}`.

* Subnet: `mysubnet`.
* Availability zone: `{{ region-id }}-a`.
* Range: `10.5.0.0/24` (only for {{ TF }}).

{% list tabs %}

- CLI

   To create a {{ mmg-name }} cluster with standard sharding, run this command:

   ```bash
   {{ yc-mdb-mg }} cluster create \
      --name mymg \
      --environment production \
      --deletion-protection=true \
      --mongodb-version {{ versions.cli.latest }} \
      --database name=db1 \
      --user name=user1,password=user1user1 \
      --mongod-resource-preset {{ host-class }} \
      --mongod-disk-type {{ disk-type-example }} \
      --mongod-disk-size 10 \
      --host type=mongod,`
        `zone-id={{ region-id }}-a,`
        `subnet-name=mysubnet \
      --mongoinfra-resource-preset c3-c2-m4 \
      --mongoinfra-disk-type {{ disk-type-example }} \
      --mongoinfra-disk-size 10 \
      --host type=mongoinfra,`
        `zone-id={{ region-id }}-a,`
        `subnet-name=mysubnet \
      --host type=mongoinfra,`
        `zone-id={{ region-id }}-a,`
        `subnet-name=mysubnet \
      --host type=mongoinfra,`
        `zone-id={{ region-id }}-a,`
        `subnet-name=mysubnet \
      --network-name mynet \
      --security-group-ids {{ security-group }}
   ```

- {{ TF }}

   The configuration file for a cluster with standard sharding is as follows:

   ```hcl
   resource "yandex_mdb_mongodb_cluster" "mymg" {
     name                = "mymg"
     environment         = "PRODUCTION"
     network_id          = yandex_vpc_network.mynet.id
     security_group_ids  = [ yandex_vpc_security_group.mymg-sg.id ]
     deletion_protection = true

     cluster_config {
       version = "{{ versions.tf.latest }}"
     }

     database {
       name = "db1"
     }

     user {
       name     = "user1"
       password = "user1user1"
       permission {
         database_name = "db1"
       }
     }

     resources_mongod {
       resource_preset_id = "{{ host-class }}"
       disk_type_id       = "{{ disk-type-example }}"
       disk_size          = 10
     }

     resources_mongoinfra {
       resource_preset_id = "c3-c2-m4"
       disk_type_id       = "{{ disk-type-example }}"
       disk_size          = 10
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongod"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongoinfra"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongoinfra"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongoinfra"
     }

   resource "yandex_vpc_network" "mynet" {
     name = "mynet"
   }

   resource "yandex_vpc_security_group" "mymg-sg" {
     name       = "mymg-sg"
     network_id = yandex_vpc_network.mynet.id

     ingress {
       description    = "MongoDB"
       port           = {{ port-mmg }}
       protocol       = "TCP"
       v4_cidr_blocks = [ "0.0.0.0/0" ]
     }
   }

   resource "yandex_vpc_subnet" "mysubnet" {
     name           = "mysubnet"
     zone           = "{{ region-id }}-a"
     network_id     = yandex_vpc_network.mynet.id
     v4_cidr_blocks = ["10.5.0.0/24"]
   }
   ```

{% endlist %}

#### Advanced sharding {#adv-sharding}

Create a {{ mmg-name }} cluster and a network for it with multiple hosts:

* One `MONGOD` host.
* Two `MONGOS` hosts.
* Three `MONGOCFG` hosts.

Cluster test characteristics:

* Name: `mymg`.
* Environment: `PRODUCTION`.
* Protection against accidental cluster deletion: Enabled.
* Version: `{{ versions.tf.latest }}`.
* Database: `db1`.
* User: `user1`.
* Password: `user1user1`.
* Host class: `{{ host-class }}`.
* SSD network storage: `{{ disk-type-example }}`.
* Storage size: 10 GB.

Network characteristics:

* Network: `mynet`.
* Security group: `mymg-sg` with the `{{ security-group }}` ID. In {{ TF }}, a group is created with the rule allowing TCP connections to the cluster from the internet on port `{{ port-mmg }}`.
* Subnet: `mysubnet`.
* Availability zone: `{{ region-id }}-a`.
* Range: `10.5.0.0/24` (only for {{ TF }}).

{% list tabs %}

- CLI

   To create a {{ mmg-name }} cluster with extended sharding, run this command:

   ```bash
   {{ yc-mdb-mg }} cluster create \
     --name mymg \
     --environment production \
     --deletion-protection=true \
     --mongodb-version {{ versions.cli.latest }} \
     --database name=db1 \
     --user name=user1,password=user1user1 \
     --mongod-resource-preset {{ host-class }} \
     --mongod-disk-type {{ disk-type-example }} \
     --mongod-disk-size 10 \
     --host type=mongod,`
       `zone-id={{ region-id }}-a,`
       `subnet-name=mysubnet \
     --mongos-resource-preset {{ host-class }} \
     --mongos-disk-type {{ disk-type-example }} \
     --mongos-disk-size 10 \
     --host type=mongos,`
       `zone-id={{ region-id }}-a,`
       `subnet-name=mysubnet \
     --host type=mongos,`
       `zone-id={{ region-id }}-a,`
       `subnet-name=mysubnet \
     --mongocfg-resource-preset {{ host-class }} \
     --mongocfg-disk-type {{ disk-type-example }} \
     --mongocfg-disk-size 10 \
     --host type=mongocfg,`
       `zone-id={{ region-id }}-a,`
       `subnet-name=mysubnet \
     --host type=mongocfg,`
       `zone-id={{ region-id }}-a,`
       `subnet-name=mysubnet \
     --host type=mongocfg,`
       `zone-id={{ region-id }}-a,`
       `subnet-name=mysubnet \
     --network-name mynet \
     --security-group-ids {{ security-group }}
   ```

- {{ TF }}

   The configuration file for a cluster with advanced sharding is as follows:

   ```hcl
   resource "yandex_mdb_mongodb_cluster" "mymg" {
     name                = "mymg"
     environment         = "PRODUCTION"
     network_id          = yandex_vpc_network.mynet.id
     security_group_ids  = [ yandex_vpc_security_group.mymg-sg.id ]
     deletion_protection = true

     cluster_config {
       version = "{{ versions.tf.latest }}"
     }

     database {
       name = "db1"
     }

     user {
       name     = "user1"
       password = "user1user1"
       permission {
         database_name = "db1"
       }
     }

     resources_mongod {
       resource_preset_id = "{{ host-class }}"
       disk_type_id       = "{{ disk-type-example }}"
       disk_size          = 10
     }

     resources_mongos {
       resource_preset_id = "{{ host-class }}"
       disk_type_id       = "{{ disk-type-example }}"
       disk_size          = 10
     }

     resources_mongocfg {
       resource_preset_id = "{{ host-class }}"
       disk_type_id       = "{{ disk-type-example }}"
       disk_size          = 10
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongod"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongos"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongos"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongocfg"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongocfg"
     }

     host {
       zone_id   = "{{ region-id }}-a"
       subnet_id = yandex_vpc_subnet.mysubnet.id
       type      = "mongocfg"
     }
   }

   resource "yandex_vpc_network" "mynet" {
     name = "mynet"
   }

   resource "yandex_vpc_security_group" "mymg-sg" {
     name       = "mymg-sg"
     network_id = yandex_vpc_network.mynet.id

     ingress {
       description    = "MongoDB"
       port           = {{ port-mmg }}
       protocol       = "TCP"
       v4_cidr_blocks = [ "0.0.0.0/0" ]
     }
   }

   resource "yandex_vpc_subnet" "mysubnet" {
     name           = "mysubnet"
     zone           = "{{ region-id }}-a"
     network_id     = yandex_vpc_network.mynet.id
     v4_cidr_blocks = ["10.5.0.0/24"]
   }
   ```

{% endlist %}
