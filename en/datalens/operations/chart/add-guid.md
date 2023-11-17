# Adding ID as a parameter

To pass a filter to a chart as a parameter:


{% include [datalens-workbooks-collections-select-note](../../../_includes/datalens/operations/datalens-workbooks-collections-select-note.md) %}


1. In the left-hand panel, click ![image](../../../_assets/datalens/datasets.svg) **Datasets** and select the dataset you need. If you do not have a dataset, [create one](../dataset/create.md).
1. In the top-left corner, select the **Fields** tab.
1. To the right of the field's row, click ![image](../../../_assets/datalens/horizontal-ellipsis.svg) and select **Copy ID**.
1. Paste the ID into the chart URL as a request parameter. As a result, you should get a link that would look like `{{ link-datalens-main }}/wizard/yfn1k6yxud7yr-example-chart?17ecb9a1-c8a5-4811-b53e-c8229f88fcba=<value>`, where:

   * `17ecb9a1-c8a5-4811-b53e-c8229f88fcba`: Field ID.
   * `<value>`: One of the field values used as a filter.
