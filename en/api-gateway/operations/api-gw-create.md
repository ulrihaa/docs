---
title: "Creating API gateways"
description: "Follow this guide to create an API gateway."
---

# Creating API gateways

Before you start, prepare an API specification file based on the [OpenAPI 3.0](https://github.com/OAI/OpenAPI-Specification) standard.

{% cut "Sample `hello-world.yaml` specification file" %}

```yaml
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

{% endcut %}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder where you want to create an API gateway.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_api-gateway }}**.
   1. Click **{{ ui-key.yacloud.serverless-functions.gateways.list.button_create }}**.
   1. Enter a name for the API gateway in the **{{ ui-key.yacloud.serverless-functions.gateways.form.field_name }}** field.
   1. (Optional) In the **{{ ui-key.yacloud.serverless-functions.gateways.form.field_description }}** field, enter a description for the API gateway.
   1. In the **{{ ui-key.yacloud.serverless-functions.gateways.form.field_spec }}** section, add the OpenAPI specification text.
   1. Configure additional API gateway settings if needed.
   1. Click **{{ ui-key.yacloud.serverless-functions.gateways.form.button_create-gateway }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   To create an API gateway:

   1. View a description of the CLI command for creating an API gateway:

      ```bash
      {{ yc-serverless }} api-gateway create --help
      ```

   1. Specify gateway parameters in the create command (not all the supported parameters are listed):

      ```bash
      {{ yc-serverless }} api-gateway create \
         --name <gateway_name> \
         --spec=<path_to_specification_file>
      ```

- {{ TF }}

   {% include [terraform-definition](../../_tutorials/terraform-definition.md) %}

   {% include [terraform-install](../../_includes/terraform-install.md) %}

   {% include [terraform-create](../../_includes/api-gateway/terraform-create.md) %}


- {{ yandex-cloud }} Toolkit

   You can create an API gateway using the [{{ yandex-cloud }} Toolkit plugin](https://github.com/yandex-cloud/ide-plugin-jetbrains/blob/master/README.en.md) for the family of IDEs on the [IntelliJ platform](https://www.jetbrains.com/opensource/idea/) from [JetBrains](https://www.jetbrains.com/).


- API

   To create an API gateway, use the [create](../apigateway/api-ref/ApiGateway/create.md) REST API method for the [ApiGateway](../apigateway/api-ref/ApiGateway/index.md) resource or the [ApiGatewayService/Create](../apigateway/api-ref/grpc/apigateway_service.md#Create) gRPC API call.

{% endlist %}
