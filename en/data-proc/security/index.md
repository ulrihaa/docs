---
title: "Access management in {{ dataproc-full-name }}"
description: "Access management in the service for creation and management of Apache Hadoop® and Apache Spark™ clusters. To allow access to {{ dataproc-name }} resources (clusters and subclusters), assign the user the required roles from the list below."
---

# Access management in {{ dataproc-name }}

{{ yandex-cloud }} users can only perform operations on resources that are allowed by the roles assigned to them. If a user doesn't have any roles assigned, almost all operations are forbidden.

To allow access to {{ dataproc-name }} service resources (clusters and subclusters), assign the user the required roles from the list below. Currently, a role can only be assigned to a parent resource (folder or cloud). Roles are inherited by nested resources.

{% note info %}

For more information about role inheritance, see [{#T}](../../resource-manager/concepts/resources-hierarchy.md#access-rights-inheritance) in the {{ resmgr-full-name }} documentation.

{% endnote %}

## Assigning roles {#grant-role}

To assign a user a role:

{% include [grant-role-console](../../_includes/grant-role-console.md) %}

## Roles {#roles}

The list below shows all roles that are considered when verifying access rights in the {{ dataproc-name }} service.

{% include [mdb.dataproc.agent](../../_includes/iam/roles/dataproc-agent.md) %}

{% include [data-proc-roles](../../_includes/iam/roles/data-proc-roles.md) %}

{% include [managed-metastore-roles](../../_includes/iam/roles/managed-metastore-roles.md) %}


### {{ roles-mdb-viewer }} {#mdb-viewer}

The `{{ roles-mdb-viewer }}` role enables you to view information about {{ dataproc-name }} clusters and their runtime logs.

### {{ roles-mdb-admin }} {#mdb-admin}

A user with the role `{{ roles-mdb-admin }}` can manage {{ dataproc-name }} clusters, for example, create a cluster or create or delete a subcluster in a cluster.

It includes the `{{ roles-mdb-viewer }}` role.

### {{ roles-viewer }} {#viewer}

A user with the `{{ roles-viewer }}` role can connect to hosts in a {{ dataproc-name }} cluster if their SSH keys are linked to this cluster.

### {{ roles-editor }} {#editor}

Users with the `{{ roles-editor }}` role can manage any resource, including creating clusters and creating and deleting their subclusters.

It includes the `{{ roles-viewer }}` role.

### {{ roles-admin }} {#admin}

Users with the `{{ roles-admin }}` role can manage resource access rights, including allowing other users to create {{ dataproc-name }} clusters and to view information about user rights.

It includes the `{{ roles-editor }}` role.

{% include [cloud-roles](../../_includes/cloud-roles.md) %}
