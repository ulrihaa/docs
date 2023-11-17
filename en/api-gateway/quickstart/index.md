---
title: "Getting started with the {{ api-gw-full-name }} (API gateways)"
description: "This guide will show you how to create and test different types of extensions. First, you will set up an API gateway for getting static responses and then add integration for invoking functions."
---

# Getting started with {{ api-gw-name }}

This guide will show you how to create and test different types of extensions. First, you will set up an API gateway for getting [static responses](../concepts/extensions/dummy.md) and then add integration for [invoking functions](../concepts/extensions/cloud-functions.md).

## Getting started {#before-you-begin}

To get started in {{ yandex-cloud }}:
1. Log in to the [management console]({{ link-console-main }}). If you do not have an account yet, go to the management console and follow the guide.
1. On the [**{{ ui-key.yacloud.component.navigation-menu.label_billing }}**]({{ link-console-billing }}) page, make sure you have a [billing account](../../billing/concepts/billing-account.md) linked and it has the `ACTIVE` or `TRIAL_ACTIVE` status. If you do not yet have a billing account, [create one](../../billing/quickstart/index.md#create_billing_account).
1. If you do not have a folder yet, [create one](../../resource-manager/operations/folder/create.md).

## Create an API gateway {#create-api-gw}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder where you want to create an API gateway.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_api-gateway }}**.
   1. Click **{{ ui-key.yacloud.serverless-functions.gateways.list.button_create }}**.
   1. In the **{{ ui-key.yacloud.serverless-functions.gateways.form.field_name }}** field, enter `numbers`.
   1. (Optional) In the **{{ ui-key.yacloud.serverless-functions.gateways.form.field_description }}** field, enter a description.
   1. In the **{{ ui-key.yacloud.serverless-functions.gateways.form.field_spec }}** section, add a specification:

      ```
      openapi: "3.0.0"
      info:
        version: 1.0.0
        title: Test API
      paths:
        /hello:
          get:
            summary: Say hello
            operationId: hello
            parameters:
              - name: user
                in: query
                description: User name to appear in greetings
                required: false
                schema:
                  type: string
                  default: 'world'
            responses:
              '200':
                description: Greeting
                content:
                  'text/plain':
                     schema:
                       type: "string"
            x-yc-apigateway-integration:
              type: dummy
              http_code: 200
              http_headers:
                'Content-Type': "text/plain"
              content:
                'text/plain': "Hello, {user}!\n"
      ```
   1. Click **{{ ui-key.yacloud.serverless-functions.gateways.form.button_create-gateway }}**.

- {{ TF }}

   {% include [terraform-definition](../../_tutorials/terraform-definition.md) %}

   {% include [terraform-install](../../_includes/terraform-install.md) %}

   {% include [terraform-create](../../_includes/api-gateway/terraform-create.md) %}

{% endlist %}

## Access the API gateway {#api-gw-test}

1. In the [management console]({{ link-console-main }}), select the folder containing the API gateway.
1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_api-gateway }}** and click the name of the created API gateway.
1. Copy the **{{ ui-key.yacloud.serverless-functions.gateways.overview.label_domain }}** field value and create a link, such as `https://<domain>/hello?user=API`. Your link should look like this:

   ```
   https://falrnjna8r5vj88ero6a.apigw.yandexcloud.net/hello?user=API
   ```

1. Access the API gateway using [curl](https://curl.haxx.se) with one of the commands:

   ```bash
   curl https://falrnjna8r5vj88ero6a.apigw.yandexcloud.net/hello?user=API
   curl https://falrnjna8r5vj88ero6a.apigw.yandexcloud.net/hello
   ```

   Result:

   ```
   Hello, API!
   Hello, world!
   ```

## Add an integration with the function {#functions}

### Create a function {#function}

Create a function to get a list of numbers. Read more about functions in the [{{ sf-name }}](../../functions/) documentation.

{% list tabs %}

- Management console

   To create a function:
   1. Create a function:
      1. In the [management console]({{ link-console-main }}), select the folder to create your function in.
      1. Click **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}**.
      1. Select **{{ ui-key.yacloud.iam.folder.dashboard.value_serverless-functions }}**.
      1. In the **{{ ui-key.yacloud.common.name }}** field, specify `list`.
      1. Click **{{ ui-key.yacloud.common.create }}**.
      1. [Make](../../functions/operations/function/function-public.md) your function public.
   1. Create the function version:
      1. In the window that opens, select the function you created.
      1. Under **{{ ui-key.yacloud.serverless-functions.item.overview.label_title-latest-version }}**, click **{{ ui-key.yacloud.serverless-functions.item.overview.button_editor-create }}**.
      1. In the window that opens, in the **{{ ui-key.yacloud.serverless-functions.item.editor.field_runtime }}** field, select `nodejs12`.
      1. In the **{{ ui-key.yacloud.serverless-functions.item.editor.field_method }}** field, select `{{ ui-key.yacloud.serverless-functions.item.editor.value_method-editor }}`.
      1. Click **{{ ui-key.yacloud.serverless-functions.item.editor.create-file }}** in the editor below.
         1. In the window that opens, enter the `index.js` file name.
         1. Click **{{ ui-key.yacloud.common.create }}**.
      1. Paste the following code in the `index.js` file:

         ```
         module.exports.handler = async (event) => {
             return {
                 "statusCode": 200,
                 "headers": {"content-type": "application/json"},
                 "body": "[0, 1, 2]"
             };
         };
         ```
      1. In the **{{ ui-key.yacloud.serverless-functions.item.editor.field_entry }}** field, specify `index.handler`.
      1. Click **{{ ui-key.yacloud.serverless-functions.item.editor.button_deploy-version }}**.

- {{ TF }}

   To create a function:

   1. Prepare a ZIP archive with the function code:
      1. Save the following code to a file named index.js:

         ```js
         module.exports.handler = async (event) => {
             return {
                 "statusCode": 200,
                 "headers": {"content-type": "application/json"},
                 "body": "[0, 1, 2]"
             };
         };
         ```

      1. Add `index.js` to the `hello-js.zip` archive.

   1. Describe the `yandex_function` resource parameters in the configuration file:

      ```hcl
      resource "yandex_function" "test-function" {
        name               = "test-function"
        description        = "Test function"
        user_hash          = "first-function"
        runtime            = "nodejs12"
        entrypoint         = "index.handler"
        memory             = "128"
        execution_timeout  = "10"
        service_account_id = "<Service account ID>"
        tags               = ["my_tag"]
        content {
          zip_filename = "<path to ZIP archive>"
        }
      }
      ```

      Where:

      * `name`: Function name.
      * `description`: Text description of the function.
      * `user_hash`: Arbitrary string that identifies the function version. When the function changes, update this string, too. The function will update when this string is updated.
      * `runtime`: The function [runtime environment](../../functions/concepts/runtime/index.md).
      * `entrypoint`: Function name in the source code that will serve as an entry point to the applications.
      * `memory`: Amount of memory allocated for function execution, in MB.
      * `execution_timeout`: Function execution timeout.
      * `service_account_id`: ID of the service account that should be used to invoke the function.
      * `tags`: Function tags.
      * `content`: Function source code.
      * `content.0.zip_filename`: Path to the ZIP archive containing the function source code.

      For more information about the `yandex_function` resource parameters, see the [provider documentation]({{ tf-provider-resources-link }}/function).

   1. Make sure the configuration files are valid.

      1. In the command line, go to the directory where you created the configuration file.
      1. Run a check using this command:

         ```
         terraform plan
         ```

      If the configuration is described correctly, the terminal will display a list of created resources and their parameters. If the configuration contains any errors, {{ TF }} will point them out.

   1. Deploy cloud resources.

      1. If the configuration does not contain any errors, run this command:

         ```
         terraform apply
         ```

      1. Confirm creating the resources: type `yes` in the terminal and press **Enter**.

         All the resources you need will then be created in the specified folder. You can check the new resources and their configuration using the [management console]({{ link-console-main }}) or these [CLI](../../cli/quickstart.md) commands:

         ```
         yc serverless function list
         ```

{% endlist %}

### Extend the API gateway specification {#update}

Add function information to the API gateway specification.

{% list tabs %}

- Management console

   To update an API gateway specification:
   1. In the [management console]({{ link-console-main }}), select the folder where you want to update an API gateway.
   1. In the window that opens, select the API gateway and click ![image](../../_assets/options.svg).
   1. In the menu that opens, click **{{ ui-key.yacloud.serverless-functions.gateways.list.button_action-edit }}**.
   1. Under **{{ ui-key.yacloud.serverless-functions.gateways.form.field_spec }}**, add an extended version of the specification

      The `/numbers` method, which uses the `cloud_functions` type `x-yc-apigateway-integration` extension, invokes a function by ID.

      To ensure that the API gateway works properly, in the `function_id` parameter, specify the ID of the function to invoke.
      To enable the API gateway to access a private function, in the `service_account_id` parameter, specify a service account that has the rights to invoke the function.

      ```
      openapi: "3.0.0"
      info:
        version: 1.0.0
        title: Test API
      paths:
        /hello:
          get:
            summary: Say hello
            operationId: hello
            parameters:
              - name: user
                in: query
                description: User name to appear in greetings
                required: false
                schema:
                  type: string
                  default: 'world'
            responses:
              '200':
                description: Greeting
                content:
                  'text/plain':
                     schema:
                       type: "string"
            x-yc-apigateway-integration:
              type: dummy
              http_code: 200
              http_headers:
                'Content-Type': "text/plain"
              content:
                'text/plain': "Hello, {user}!\n"
        /numbers:
          get:
            summary: List some numbers
            operationId: listNumbers
            responses:
              '200':
                description: Another example
                content:
                  'application/json':
                     schema:
                       type: "array"
                       items:
                         type: "integer"
            x-yc-apigateway-integration:
              type: cloud_functions
              function_id: <function ID >
              service_account_id: <service account ID>
      ```

- {{ TF }}

   To add function information to the API gateway specification:

   1. Open the {{ TF }} configuration file and add the `/numbers` method, which uses the `cloud_functions` type `x-yc-apigateway-integration` extension to invoke a function by ID. Under `spec`, change the API gateway specification by specifying the following parameters:

      * `function_id`: Function ID.
      * `service_account_id`: ID of the service account with rights to invoke a function.

      Extended API gateway specification:

      ```hcl
      ...

        spec = <<-EOT
          openapi: "3.0.0"
          info:
            version: 1.0.0
            title: Test API
          paths:
            /hello:
              get:
                summary: Say hello
                operationId: hello
                parameters:
                  - name: user
                    in: query
                    description: User name to appear in greetings
                    required: false
                    schema:
                      type: string
                      default: 'world'
                responses:
                  '200':
                    description: Greeting
                    content:
                      'text/plain':
                        schema:
                          type: "string"
                x-yc-apigateway-integration:
                  type: dummy
                  http_code: 200
                  http_headers:
                    'Content-Type': "text/plain"
                  content:
                    'text/plain': "Hello again, {user}!\n"
            /numbers:
              get:
                summary: List some numbers
                operationId: listNumbers
                responses:
                  '200':
                    description: Another example
                    content:
                      'application/json':
                        schema:
                          type: "array"
                          items:
                            type: "integer"
                x-yc-apigateway-integration:
                  type: cloud_functions
                  function_id: <function ID>
                  service_account_id: <service account ID>
        EOT
      }
      ```

      For more information about resource parameters in {{ TF }}, see the [provider documentation]({{ tf-provider-resources-link }}/api_gateway).

   1. Make sure the configuration files are valid.

      1. In the command line, go to the directory where you created the configuration file.
      1. Run a check using this command:

         ```
         terraform plan
         ```

      If the configuration is described correctly, the terminal will display a list of created resources and their parameters. If the configuration contains any errors, {{ TF }} will point them out.

   1. Deploy cloud resources.

      1. If the configuration does not contain any errors, run this command:

         ```
         terraform apply
         ```

      1. Confirm creating the resources: type `yes` in the terminal and press **Enter**.

         All the resources you need will then be created in the specified folder. You can check the new resources and their configuration using the [management console]({{ link-console-main }}) or this [CLI](../../cli/quickstart.md) command:

         ```
         yc serverless api-gateway get <API gateway name>
         ```

{% endlist %}

### Access the function via the {#api-gw} API gateway

{% note info %}

For the API gateway to be able to invoke a function, [make](../../functions/operations/function/function-public.md) it public or [specify](../concepts/extensions/cloud-functions.md) in the specification the service account that has rights to invoke a function.

{% endnote %}

Access the API gateway:

```bash
curl https://falrnjna8r5vj88ero6a.apigw.yandexcloud.net/numbers
```

Result:

```
[0, 1, 2]
```

#### See also {#see-also}

* [Concepts when using the service](../concepts/index.md).
* [Step-by-step instructions for managing API gateways](../operations/index.md).
