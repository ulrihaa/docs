---
title: "Viewing monitoring charts in {{ sf-full-name }}"
description: "You can view a monitoring chart in {{ sf-full-name }} using the management console. To do this, select {{ sf-name }} and click the trigger to view monitoring charts for."
---

# Viewing trigger monitoring charts in {{ sf-name }}

You can monitor triggers using the monitoring tools in your management console. These tools display diagnostic information in the form of charts. Metric values are collected and charts are displayed by [{{ monitoring-name }}](../../../monitoring/).

The chart update period is 15 seconds.

## Viewing monitoring charts {#charts}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder containing your trigger.

   1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_serverless-functions }}**.

   1. Select a trigger to view its monitoring charts.

   1. Go to the **{{ ui-key.yacloud.component.navigation-menu.label_monitoring }}** tab.

   1. The following charts will open on the page:

      * **Request latency**: Average time it takes a trigger to process a request.
      * **Read events**: Number of events causing a trigger to fire.
      * **Function access errors**: Number of access errors when invoking a function.
      * **Function call errors**: Number of errors when invoking a function.
            * **DLQ access errors**: Number of errors when accessing the [Dead Letter Queue](../../concepts/dlq.md).
      * **Send to DLQ errors**: Number of errors when sending messages to the Dead Letter Queue.

   You can select the time period to display information for: hour, day, week, month, or a custom interval.

{% endlist %}

## Custom metrics {#metrics}

To get started with [metrics](../../../monitoring/concepts/data-model.md#metric), [dashboards](../../../monitoring/concepts/visualization/dashboard.md), and [alerts](../../../monitoring/concepts/alerting.md#alert) in {{ monitoring-name }}, in the top-right corner, click **{{ ui-key.yacloud.monitoring.button_open-in-monitoring }}**.

| Metric name | Units | Entity type | Explanations |
|----|----|----|----|
| `serverless.triggers.`<br/>`inflight` | Invocations | <ul><li>`request`: Function calls.</li></ul> | Number of concurrent function invocations. |
| `serverless.triggers.`<br/>`error_per_second` | Errors per second | <ul><li>`request`: Function calls.</li><li>`message_queue`: Accesses to the {{ message-queue-full-name }}</li><li>`dlq`: Accesses to the Dead Letter Queue</li></ul> | Error frequency when processing function invocations. |
| `serverless.triggers.`<br/>`access_error_per_second` | Errors per second | <ul><li>`request`: Function calls.</li><li>`message_queue`: Accesses to the {{ message-queue-full-name }}</li><li>`dlq`: Accesses to the Dead Letter Queue</li></ul> | Access error frequency when processing function invocations. |
| `serverless.triggers.`<br/>`retry_per_second` | Invocations per second | <ul><li>`request`: Function calls.</li></ul> | The frequency of repeat function invocations in the event of an error. |
| `serverless.triggers.`<br/>`read_events_per_second` | Events per second | <ul><li>`incoming`: Events causing any trigger to fire, except for a trigger for {{ message-queue-full-name }}.</li><li>`message_queue`: Events causing a trigger to fire for {{ message-queue-full-name }}.</li></ul> | Frequency of events causing a trigger to fire. |
| `serverless.triggers.`<br/>`execution_time_milliseconds` | Invocations per second | <ul><li>`request`: Function calls.</li></ul> | Function invocation frequency distribution histogram by request processing time in milliseconds. Request processing time intervals are shown in the `bin` label. |

### Custom metrics labels {#labels}

| Label name | Possible values | Explanations |
|----|----|----|
| `trigger` | Trigger name | The label contains the name of the trigger that the metric values refer to. |
| `id` | Trigger ID | The label contains the ID of the trigger the metric values refer to. |
| `type` | Entity type | The label contains the entity type the metric values refer to. |
| `bin` | Interval of histogram values | For metrics represented by a histogram, the label value describes the interval of the measured value during which the event occurred. |
