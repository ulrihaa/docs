---
title: "How to create a function version"
description: "Follow this guide to create a function version."
---

# Creating a function version

{% include [create-version](../../../_includes/functions/create-version.md) %}

## Preparing a ZIP archive with the function code {#zip-archive}

{% include [create-js-zip](../../../_includes/functions/create-js-zip.md) %}

## Creating a function version {#version-create}

When creating a version, set the following parameters:

* _{{ ui-key.yacloud.serverless-functions.item.editor.field_runtime }}_: Provides additional libraries and environment variables that can be accessed from the function code. It corresponds to the programming language that your function is written in. For more information, see [Runtime environment](../../concepts/runtime/index.md).
* _{{ ui-key.yacloud.serverless-functions.item.editor.field_entry }}_: Function to be invoked as a handler. To read more about the handler, see [Programming model](../../concepts/function.md#programming-model).
* _{{ ui-key.yacloud.serverless-functions.item.editor.field_timeout }}_: Maximum function execution time, after which the service will terminate its execution without waiting for a response. It includes the time of the first initialization when the function is first run.

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder containing your function.
   1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_serverless-functions }}**.
   1. Select the function to create a version of.
   1. Under **{{ ui-key.yacloud.serverless-functions.item.overview.label_title-latest-version }}**, click **{{ ui-key.yacloud.serverless-functions.item.overview.button_editor-create }}**.
   1. Select the [runtime environment](../../concepts/runtime/index.md). Disable the **{{ ui-key.yacloud.serverless-functions.item.editor.label_with-template }}** option.
   1. Click **{{ ui-key.yacloud.serverless-functions.item.editor.button_action-continue }}**.
   1. Prepare the function code:
      * **{{ ui-key.yacloud.serverless-functions.item.editor.field_runtime }}**: `nodejs12`
      * **{{ ui-key.yacloud.serverless-functions.item.editor.field_method }}**: `{{ ui-key.yacloud.serverless-functions.item.editor.value_method-zip-file }}`
      * **{{ ui-key.yacloud.serverless-functions.item.editor.field_file }}**: `hello-js.zip`
      * **{{ ui-key.yacloud.serverless-functions.item.editor.field_entry }}**: `index.handler`
   1. Set the version parameters:
      * **{{ ui-key.yacloud.serverless-functions.item.editor.field_timeout }}**: `5`
      * **{{ ui-key.yacloud.serverless-functions.item.editor.field_resources-memory }}**: `128 {{ ui-key.yacloud.common.units.label_megabyte }}`
      * [**{{ ui-key.yacloud.forms.label_service-account-select }}**](../../../iam/concepts/users/service-accounts.md): `{{ ui-key.yacloud.component.service-account-select.label_no-service-account }}`
      * [**{{ ui-key.yacloud.serverless-functions.item.editor.field_environment-variables }}**](../../concepts/runtime/environment-variables.md): `{{ ui-key.yacloud.common.not-selected }}`
   1. Click **{{ ui-key.yacloud.serverless-functions.item.editor.button_deploy-version }}**.

- CLI

   {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

   To create a function version, run the command:

   
   ```
   yc serverless function version create \
     --function-name=my-nodejs-function \
     --runtime nodejs12 \
     --entrypoint index.handler \
     --memory 128m \
     --execution-timeout 5s \
     --source-path ./hello-js.zip
   ```


   Where:

   * `--function-name`: Name of the function you want to create a version of.
   * `--runtime`: Runtime environment.
   * `--entrypoint`: Entry point specified in the `<filename without extension>`.`<handler name>` format.
   * `--memory`: Amount of RAM.
   * `--execution-timeout`: Maximum function execution time before the timeout is reached.
   * `--source-path`: ZIP archive with the function code and required dependencies.

   Result:

   
   ```
   done (1s)
   id: d4evvn8obisa********
   function_id: d4elpv8pft63********
   created_at: "2020-08-01T19:09:19.531Z"
   runtime: nodejs12
   entrypoint: index.handler
   resources:
   memory: "134217728"
   execution_timeout: 5s
   image_size: "4096"
   status: ACTIVE
   tags:
   - $latest
   log_group_id: ckg3qh8h363p********
   ```


- {{ TF }}

   {% include [terraform-definition](../../../_tutorials/terraform-definition.md) %}

   {% include [terraform-install](../../../_includes/terraform-install.md) %}

   To create a new function version:

   1. Open the {{ TF }} configuration file and change the function parameters:

      * `yandex_function`: Description of the function being created and its source code.
         * `name`: Function name.
         * `description`: Text description of the function.
         * `user_hash`: Arbitrary string that identifies the function version. When the function changes, update this string, too. The function will update when this string is updated.
         * `runtime`: Function [runtime environment](../../concepts/runtime/index.md)
         * `entrypoint`: Function name in the source code that will serve as an entry point to the applications.
         * `memory`: Amount of memory allocated for function execution, in MB.
         * `execution_timeout`: Function execution timeout
         * `service_account_id`: ID of the service account that should be used to invoke the function.
         * `content`: Function source code
            * `content.0.zip_filename`: Name of the ZIP archive that contains the function source code.

      Example of the configuration file structure:

      ```
      resource "yandex_function" "test-function" {
          name               = "test-function"
          description        = "Test function"
          user_hash          = "first-function"
          runtime            = "python311"
          entrypoint         = "main"
          memory             = "128"
          execution_timeout  = "10"
          service_account_id = "<service account ID>"
          content {
              zip_filename = "<path to ZIP archive>"
          }
      }
      ```

      {% note info %}

      If the function name or description is changed, the version will not be created.

      {% endnote %}

      For more information about the `yandex_function` resource parameters, see the [provider documentation]({{ tf-provider-resources-link }}/function).

   1. Check the configuration using this command:

      ```
      terraform validate
      ```

      If the configuration is correct, you will get this message:

      ```
      Success! The configuration is valid.
      ```

   1. Run this command:

      ```
      terraform plan
      ```

      The terminal will display a list of resources with parameters. No changes will be made at this step. If the configuration contains any errors, {{ TF }} will point them out.

   1. Apply the configuration changes:

      ```
      terraform apply
      ```
   1. Confirm the changes: type `yes` into the terminal and press **Enter**.

   You can check the new version using the [management console]({{ link-console-main }}) or this [CLI](../../../cli/quickstart.md) command:

   ```
   yc serverless function version list --function-name <function name>
   ```

- API

   To create a function version, use the [createVersion](../../functions/api-ref/Function/createVersion.md) REST API method for the [Function](../../functions/api-ref/Function/index.md) resource or the [FunctionService/CreateVersion](../../functions/api-ref/grpc/function_service.md#CreateVersion) gRPC API call.


- {{ yandex-cloud }} Toolkit

   You can create a function version using the [{{ yandex-cloud }} Toolkit plugin](https://github.com/yandex-cloud/ide-plugin-jetbrains/blob/master/README.en.md) for the IDE family on the [JetBrains](https://www.jetbrains.com/) [IntelliJ platform](https://www.jetbrains.com/opensource/idea/).


{% endlist %}

{% note info %}

To ensure the integrity of version links, you cannot update or delete function versions. For more information about relationships between resources, see [{#T}](../../concepts/function.md).

{% endnote %}
