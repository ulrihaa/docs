---
title: "How to remove a user from a community"
description: "In this tutorial, you will learn how to remove a user from a community in {{ ml-platform-name }}."
---

# Removing a user from a community

{% note info %}

You can only remove a user if you have the `{{ roles-datasphere-communities-admin }}` role in the community.

{% endnote %}

1. Open the {{ ml-platform-name }} [home page]({{ link-datasphere-main }}).
1. In the left-hand panel, select ![community-panel](../../../_assets/datasphere/communities.svg) **{{ ui-key.yc-ui-datasphere.common.spaces }}**.
1. Select the community to remove users from.
1. Go to the **{{ ui-key.yc-ui-datasphere.common.members }}** tab.
1. Click ![image](../../../_assets/horizontal-ellipsis.svg) next to the user and select **{{ ui-key.yc-ui-datasphere.common.member.remove }}**.
1. Click **{{ ui-key.yc-ui-datasphere.common.submit }}**.
1. If you added the user via a link, recreate it:
    * Click **{{ ui-key.yc-ui-datasphere.common.add-member }}**.
    * At the bottom of the window that opens, click **{{ ui-key.yc-ui-datasphere.invite-link.reset-invitation-link }}** ⟶ **{{ ui-key.yc-ui-datasphere.invite-link.reset-link }}**.
