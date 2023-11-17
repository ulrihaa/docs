---
title: "How to view detailed information about the {{ mes-name }} cluster state"
description: "This guide describes how you can view detailed information about the state of a {{ mes-name }} cluster."
---

# Monitoring the state of {{ ES }} clusters and hosts

{% include [Elasticsearch-end-of-service](../../_includes/mdb/mes/note-end-of-service.md) %}

{% include [monitoring-introduction](../../_includes/mdb/monitoring-introduction.md) %}

New data for charts is received every {{ graph-update }}.

{% include [note-monitoring-auto-units](../../_includes/mdb/note-monitoring-auto-units.md) %}

{% include [alerts](../../_includes/mdb/alerts.md) %}

## Monitoring cluster state {#monitoring-cluster}

To view detailed information about the {{ mes-name }} cluster state:

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-elasticsearch }}**.
   1. Click the cluster name and open the **{{ ui-key.yacloud.mdb.cluster.switch_monitoring }}** tab.

   1. {% include [open-in-yandex-monitoring](../../_includes/mdb/open-in-yandex-monitoring.md) %}

   The page displays the following charts:

   * **Active shards**: Number of active [primary shards](../concepts/scalability-and-resilience.md) and the total number of active shards in the cluster.
   * **Deletion rate**: Number of delete operations per second, per host.
   * **Disk space usage percent**: Shows how much disk space is used on each host (in %).
   * **Flushes**: Number of transaction log flush operations per host.

   * **Health status**: Shows the cluster's health and technical condition:

      * 0 (<q>red</q>): Cluster is unhealthy or partially functional. At least one of the primary shards is unavailable. If the cluster responds to queries, the search results will be incomplete.
      * 1 (<q>yellow</q>): Cluster is healthy. There is no access to at least one of the replica shards. The search results in the cluster responses are complete; however, if there are more unavailable shards, cluster performance will be disrupted.
      * 2 (<q>green</q>): Cluster is healthy. All cluster shards are available.

   * **Indexing rate**: Number of indexing operations per second, per host.
   * **JVM heap**: The use of JVM heap memory per host (in bytes).
   * **JVM heap pressure**: The use of a pool of long-lived JVM objects per host (%).
   * **JVM old collections**: The number of garbage collection cycles in the pool of long-lived JVM objects per host.
   * **JVM young collections**: The number of garbage collection cycles in the pool of new JVM objects per host.
   * **Merges**: The number of index segment merges per host.
   * **Nodes**: The number of hosts with the [`Data node` role](../concepts/hosts-roles.md) and the total number of hosts in the cluster.
   * **Open file descriptors**: The number of open file descriptors per host.
   * **Other shards**: Number of inactive shards in each of the following states:

      * Delayed unassigned: Host assignment is delayed.
      * Unassigned: There is no assigned host.
      * Relocating: Moving to another host.
      * Initializing: Being initialized.

   * **Process CPU**: Usage of processor cores on each host due to the JVM {{ ES }} process.
   * **Query cache**: The number of queries in the cache on each host.
   * **Read bytes**: Disk read rate on each host (bytes per second).
   * **Read operations**: Number of read operations per second, per host.
   * **Refreshes**: The number of index segment refresh operations per host.
   * **Search queries**: The number of search queries per second, per host.
   * **Segments**: Number of index segments per host.
   * **Store size**: The size of index storage on disk (in bytes).
   * **Write bytes**: Disk write rate on each host (bytes per second).
   * **Write operations**: Number of write operations per second, per host.

   {% note info %}

   {% include [monitoring-provides](../../_includes/mdb/monitoring-provides.md) %}

   {% endnote %}

{% endlist %}

## Monitoring the state of hosts {#monitoring-hosts}

To view detailed information about the state of individual {{ mes-name }} hosts:

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-elasticsearch }}**.
   1. Click the cluster name and open the **{{ ui-key.yacloud.mdb.cluster.hosts.label_title }}** tab.
   1. Select the **{{ ui-key.yacloud.mdb.cluster.hosts.switch_monitoring }}** tab.
   1. Select the host from the drop-down list.

   This page displays charts showing the load on an individual host in the cluster:

   * **CPU**: Load on processor cores. As the load goes up, the `Idle` value goes down.
   * **Disk bytes**: Speed of disk operations (bytes per second).
   * **Disk IOPS**: Number of disk operations per second.
   * **Memory**: Use of RAM, in bytes. At high loads, the `Free` value goes down, while the other values go up.
   * **Network bytes**: Speed of data exchange over the network, in bytes per second.
   * **Network packets**: Number of packets exchanged over the network, per second.

{% endlist %}


## Alert settings in {{ monitoring-full-name }} {#monitoring-integration}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder with the cluster you want to configure alerts for.
   1. In the list of services, select ![image](../../_assets/monitoring.svg) **{{ ui-key.yacloud.iam.folder.dashboard.label_monitoring }}**.
   1. Under **{{ ui-key.yacloud_monitoring.homepage.title_service-dashboards }}**, select:

      * **{{ ui-key.yacloud_monitoring.services.label_managed-elasticsearch }}** to configure cluster alerts.
      * **{{ ui-key.yacloud_monitoring.services.label_managed-elasticsearch }} — Host Overview** to configure host alerts.

   1. In the chart you need, click ![options](../../_assets/horizontal-ellipsis.svg) and select **{{ ui-key.yacloud_monitoring.dashboard.dash.create-alert }}**.
   1. If the chart shows multiple metrics, select a data query to generate a metric and click **{{ ui-key.yacloud_monitoring.dialog.confirm.button_continue }}**. For more information about the query language, see the [{{ monitoring-full-name }} documentation](../../monitoring/concepts/querying.md).
   1. Set the `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.alarm }}` and `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.warn }}` thresholds to trigger an alert.
   1. Click **{{ ui-key.yacloud_monitoring.alert.button_create-alert }}**.

{% endlist %}

{% include [other-indicators](../../_includes/mdb/other-indicators.md) %}

The recommended thresholds are as follows:

| Metric | Parameter | Formula | `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.alarm }}` | `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.warn }}` |
|----------------------------------------------|:-----------------------------------------:|:----------------------:|:---------------------------------:|:---------------------------------:|
| Cluster status | `elasticsearch_status` | `bottom_last(1)` | `Equal to 0` | `Equal to 1` |
| Number of unassigned shards | `elasticsearch_unassigned_shards` | `top_last(1)` | `Greater than 0` |                                   |
| Number of relocated shards | `elasticsearch_relocating_shards` | `top_last(1)` | `Greater than 0` |                                   |
| Number of initialized shards | `elasticsearch_initializing_shards` | `top_last(1)` | `Greater than 0` |                                   |
| Number of delayed assignment shards | `elasticsearch_delayed_unassigned_shards` | `top_last(1)` | `Greater than 0` |                                   |
| JVM heap memory used | `elasticsearch_jvm_mem_heap_used_percent` | `top_last(1)` | Over 90% of host RAM |                                   |
| Storage space used | `elasticsearch_fs_total_used_percent` | `top_last(1)` | Over 90% of the storage size | Over 85% of the storage size |
| Using the JVM long-lived object pool | `elasticsearch_jvm_mem_heap_pressure` | `top_last(1)` | Over 90% of host RAM | Over 75% of host RAM |
| Storage space used | `disk.used_bytes` | N/A | 90% of the storage size | 80% of the storage size |

For the `disk.used_bytes` metric, the `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.alarm }}` and `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.warn }}` thresholds are only set in bytes. For example, here are the recommended values for a 100 GB disk:

* `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.alarm }}`: `96,636,764,160` bytes (90%)
* `{{ ui-key.yacloud_monitoring.alert-template.threshold-status.warn }}`: `85,899,345,920` bytes (80%)

You can view the host's current storage size and RAM amount in the [detailed information about the cluster](cluster-list.md#get-cluster). For a complete list of supported metrics, see the [{{ monitoring-name }} documentation](../../monitoring/metrics-ref/index.md#managed-elasticsearch).


## Cluster state and status {#cluster-health-and-status}

{% include [health-and-status](../../_includes/mdb/monitoring-cluster-health-and-status.md) %}

To view a cluster's state and status:

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), go to the folder page and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-elasticsearch }}**.
   1. Hover over the indicator in the **{{ ui-key.yacloud.common.availability }}** column in the required cluster row.

- API

   Use the [get](../api-ref/Cluster/get.md) REST API method for the [Cluster](../api-ref/Cluster/index.md) resource or the [ClusterService/Get](../api-ref/grpc/cluster_service.md#Get) gRPC API call and provide the cluster ID in the `clusterId` request parameter.

   The cluster health and status will be shown in the `health` and `status` parameters, respectively.

   {% include [get-cluster-id](../../_includes/managed-elasticsearch/get-cluster-id.md) %}

{% endlist %}

### Cluster states {#cluster-health}

{% include [monitoring-cluster-health](../../_includes/mdb/monitoring-cluster-health.md) %}

### Cluster statuses {#cluster-status}

{% include [monitoring-cluster-status](../../_includes/mdb/monitoring-cluster-status.md) %}

