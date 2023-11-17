# {{ tracker-full-name }} revision history for September 2023

* [Updates](#top-news)
* [Fixes and improvements](#fixes)

## Updates {#top-news}

### New settings for queue access {#new-queue-access}

The new access rights allow you to flexibly manage your team's queue permissions: from viewing, editing, or creating tasks, to editing queue settings. Moreover, you can now restrict access to the queue. You can configure these permissions both for individual users and groups of users.

You still can override access rights in tasks using components and assign rights depending on roles in the task. Now these settings can be accessed from the new interface and are easier to use.

To manage access, go to the **{{ ui-key.startrek.ui_components_page-queue-admin_QueueAdminPageContent.menu-item-permissions }}** tab in queue settings. On this page, you can also view queue access rights for any user or user group.

For more details on setting up access rights, see [{#T}](../manager/queue-access.md).

### Advanced time tracking {#extended-spent-time}

Now you can easier track spent time thanks to [advanced time tracking](../user/time-spent.md#extended-spent-time). This feature allows you to:

* Specify the spent time directly in the task right-hand panel.
* Edit and delete spent time records without using the API.
* Track how much time is left to complete a task.
* Use a convenient time presentation form.
* Specify the spent time without measurement units.

Advanced time tracking can be enabled and configured separately for each queue. For more information, see [{#T}](../manager/queue-spent-time.md).

### Integrations with forms within {{ tracker-name }} {#forms-integrations}

In the queue integration settings, you can now create and integrate [{{ forms-full-name }}](../../forms/index.yaml) forms. To do this:

1. In the settings for the queue for which you want to create or integrate the form, select **{{ ui-key.startrek.blocks-desktop_b-page-queue-admin.tab-title--integrations }}**.

1. Under **{{ ui-key.startrek.ui_components_queue-admin-tab-integrations_IntegrationForms.title }}**, click **{{ ui-key.startrek.ui_components_queue-admin-tab-integrations_IntegrationForms.settings-button-text }}**. On this page, you can work with forms:

   * Create new forms and add existing ones. To do this, click **{{ ui-key.startrek.ui_components_page-queue-admin_queue-admin-tab-integrations-forms_components_FormsMenu.add-form }}** in the upper-right corner and select **{{ ui-key.startrek.ui_components_page-queue-admin_queue-admin-tab-integrations-forms_components_FormsMenu.add-form }}** or **{{ ui-key.startrek.ui_components_page-queue-admin_queue-admin-tab-integrations-forms_components_FormsMenu.create-new }}**.

   * Edit and delete previously added forms. To do this, in the list of forms, click ![](../../_assets/tracker/svg/actions.svg) to the right of the record of interest and select an action.

### Portfolios {#portfolios}

{{ tracker-name }} now provides portfolios where you can combine [projects](../manager/project-new.md) and other portfolios. Portfolios help you structure and manage your project activities.

Portfolios are available in the [section]({{ link-tracker }}pages/projects/list) ![](../../_assets/tracker/svg/project.svg)&nbsp;**{{ ui-key.startrek.blocks-desktop_b-queues-info.projects }}**. There you can create portfolios, populate them with projects and other portfolios, update statuses, and track progress. With portfolios, you can also use Gantt charts to easily track and adjust your project schedule.

For more information on portfolios, see [{#T}](../manager/portfolio.md).

### Project milestones {#milestones}

Added a new [issue type](../manager/add-ticket-type.md): ![](../../_assets/tracker/svg/milestone.svg) **{{ ui-key.startrek.ui_components_issues_create-issue-popup_NewIssueForm.milestone-type }}**. You can use it to track important project development stages. Note that milestones have no **{{ ui-key.startrek.ui_components_Project.sidebar-param-title--startDate }}** and **{{ ui-key.startrek.ui_components_Project.sidebar-param-title--endDate }}** fields. They only have the **{{ ui-key.startrek.components_FormCreateIssue.field--dueDate }}** field.

Project milestones in {{ tracker-name }} are displayed in the **{{ ui-key.startrek.ui_components_projects_HeaderTabs.description-tab }}** tab under the project description. On the [project Gantt chart](../gantt/project.md), milestones are shown as a diamond. Now you can add milestones to your project on these pages.

To use milestones in a queue, add them to the [queue workflow](../manager/add-workflow.md).

For more information on milestones, see [{#T}](../manager/milestones.md).

### Visual workflow editor leaves beta {#new-default-workflow-page}

The new [queue workflow](../manager/add-workflow.md) editor has left beta and is now available by default. Because of this, we have now removed the **Task Types** section from queue settings: all these options are now available under **Workflows**. To open workflows:

1. Navigate to the required queue's settings.
1. Select the **{{ ui-key.startrek.ui_components_page-queue-admin_QueueAdminPageContent.menu-item-workflows }}** tab.

### Copying workflows from other queues {#copy-workflow}

In the workflow editor, you can now copy workflows from one queue and use them in another queue. To do this:

1. Navigate to the settings of the queue for which you want to set up a workflow.
1. Select the **{{ ui-key.startrek.ui_components_page-queue-admin_QueueAdminPageContent.menu-item-workflows }}** tab.
1. In the top-right corner, tap ![](../../_assets/tracker/svg/copy-workflow.svg).
1. In the pop-up window, select the queue and its workflow you want to copy, and enter the name for the new workflow.
1. Click **{{ ui-key.startrek.ui_components_queue-admin-tab-workflows_CopyWorkflowDialog.action-copy }}**.

## Fixes and improvements {#fixes}

### Fixed transition display on the workflow page {#fix-workflow-transition}

If you set a status transition into itself in the workflow editor, the transition line now does not cross the status block and displays correctly.