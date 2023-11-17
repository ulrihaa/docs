To set up the [maintenance window](../../../../managed-mysql/concepts/maintenance.md) (for disabled clusters as well), add the `maintenance_window` block to the cluster description:

```hcl
resource "yandex_mdb_mysql_cluster" "<cluster_name>" {
  ...
  maintenance_window {
    type = <maintenance_type>
    day  = <day_of_week>
    hour = <hour>
  }
  ...
}
```

{% include [maintenance-window-description](../../terraform/maintenance-window-description.md) %}
