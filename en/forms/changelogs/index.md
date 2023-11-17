# {{ forms-full-name }} revision history for October 2023

* [Enhanced integration with {{ sf-full-name }}](#integration-with-functions)


## Enhanced integration with {{ sf-full-name }} {#integration-with-functions}

Before [setting up integration with {{ sf-full-name }}](../call-function.md), it is critical to specify the key ID and secret key. You can do this in the form settings: go to **Settings** → **Advanced** and fill in the fields under **Cloud function key**.

To make sure users do not miss specifying the keys, we added a verification and prompts to the {{ forms-full-name }} interface: now, integrations with no keys are displayed as locked and the user can switch to the add keys page from a prompt.


