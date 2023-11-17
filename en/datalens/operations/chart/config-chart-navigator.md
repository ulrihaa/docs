# Configuring the navigator

You can configure the navigator if the following conditions are met:

* Chart type: **Line chart**, **Stacked area chart**, or **Normalized area chart**.
* The **X** section contains a field of the `Date` or `Date and time` type.

To set up the navigator:


{% include [datalens-workbooks-collections-select-note](../../../_includes/datalens/operations/datalens-workbooks-collections-select-note.md) %}


1. In the left-hand panel, click ![image](../../../_assets/datalens/chart.svg) **Charts** and select a chart to set up the navigator for.
1. On the left side of the screen above the chart, click ![image](../../../_assets/datalens/gear.svg).
1. In the **Chart settings** window, enable the **Navigator** option.
1. Select the navigator display mode:

   * **All lines** to display all chart lines in the navigator.
   * **Choose lines** to display the chosen lines in the navigator.

1. Specify the **Default period**. Each time you open the chart, the last period corresponding to this setting is displayed. Set `0` or leave the field empty to display the entire range of values.
1. Click **Apply**. The navigator is displayed at the bottom of the chart.

{% cut "Examples of operations with navigator" %}

![image](../../../_assets/datalens/chart-settings/02-navigator.gif).

{% endcut %}


