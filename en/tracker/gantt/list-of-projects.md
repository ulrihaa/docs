# Gantt chart for a list of projects {{ tracker-name }}

Gantt charts for lists of {{ tracker-name }} projects show all projects along with the issues within each of them and the links between them.

## Creating a Gantt chart for a list of projects {#sec_projects}

To go to the Gantt chart:

1. Go to the [projects page]({{ link-tracker }}pages/projects) or select ![](../../_assets/tracker/svg/project.svg)&nbsp;**{{ ui-key.startrek.blocks-desktop_b-queues-info.projects }}** in the [left-hand panel]({{ link-tracker }}) and click **Full list of my projects**.

1. Select the **{{ ui-key.startrek.ui_components_filter-pane_ViewModeSwitch.mode-gantt }}** tab.
   * The project list is displayed to the left of the chart. To open the list of project issues, click ![](../../_assets/tracker/svg/arrow.svg) next to the project name. The issues are displayed as a hierarchical list.
   * The project timeframes are shown on the chart as bars plotted along the time axis. The left edge of each bar indicates the start date of work on the project, while the end of the bar shows its deadline.

1. To see only specific issues within your projects, set up filtering by issue. You can do this in the right-hand panel above the chart, under **{{ ui-key.startrek.ui_components_projects_ListFilterPane.filter-issues }}**, by adding the parameters you need one by one and setting their values.

1. To set up the time scale, select **{{ ui-key.startrek.ui_components_Gantt.scale-days }}**, **{{ ui-key.startrek.ui_components_Gantt.scale-month }}**, or **{{ ui-key.startrek.ui_components_Gantt.scale-quarters }}** in the bottom-right corner of the chart. The scale settings persist after refreshing the page or opening the chart again.

## Changing project timeframes {#timing}

* If the project timeframes are not shown on the chart (its start and end dates are not set), hover over the chart next to the project name, position the project bar along the time axis, and click the left mouse button.

* To shift the project start and end dates by the same value, drag the bar along the time axis.

* To change the project duration, drag the left edge of the bar (the start date) or the right edge of the bar (end date).

## Assigning an employee in charge {#assigned}

On a Gantt chart for a list of projects, you can appoint or change an employee in charge of a project.

1. Click ![](../../_assets/tracker/svg/pick-user.svg) on the project bar.

1. Enter the name of the employee in charge of a portfolio or project.

{% note info %}

If you do not see the ![](../../_assets/tracker/svg/pick-user.svg) button on the project bar, go to ![](../../_assets/tracker/svg/gantt-settings-button.svg)&nbsp;**{{ ui-key.startrek.ui_components_Gantt.timeline-config }}** and select the **{{ ui-key.startrek.ui_components_Gantt.show-issue-assignee }}** item under **{{ ui-key.startrek.ui_components_Gantt.task-bar }}**.

{% endnote %}

In a similar way, you can also use the chart to appoint and change assignees for each project's tasks.

## Chart settings {#settings}

### Displaying the issue bar {#issue-bar}

{% include [issue-bar](../../_includes/tracker/issue-bar.md) %}

### Timeline area {#timeline}

{% include [timeline-area](../../_includes/tracker/timeline-area.md) %}

{% include [external-blockers](../../_includes/tracker/external-blockers.md) %}

## Displaying the issue list section {#tasks-view}

* To find an issue or project in the list, enter text in the **{{ ui-key.startrek.ui_components_projects_ListFilterPane.search-placeholder }}** field.

* To set up their display order in the list, click ![](../../_assets/tracker/svg/sorting.svg)&nbsp;**{{ ui-key.startrek.ui_components_SortButton.default-title }}**, specify the parameter, and click ![](../../_assets/tracker/svg/new-first.svg). In this case, you will not be able to move issues manually.

* To filter the project list, use the filter builder. For this, on the ![](../../_assets/tracker/svg/filter.svg)&nbsp;**{{ ui-key.startrek.blocks-desktop_b-page-issues.page-title }}** panel, under **{{ ui-key.startrek.ui_components_projects_ListFilterPane.filter-projects }}**, select the parameters and specify the values to use for generating a project list. Under **{{ ui-key.startrek.ui_components_projects_ListFilterPane.filter-issues }}**, you can also set up additional filtering by issues within your projects.