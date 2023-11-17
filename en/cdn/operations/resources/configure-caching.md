# Configuring resource caching

To configure the [caching](../../concepts/caching.md) parameters of a [resource](../../concepts/resource.md):

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder where your resource is located.

   1. Select **{{ ui-key.yacloud.iam.folder.dashboard.label_cdn }}**.

   1. Click the resource name.

   1. Go to the **{{ ui-key.yacloud.cdn.label_resource-cache }}** tab.

   1. In the top-right corner, click ![image](../../../_assets/edit.svg) **{{ ui-key.yacloud.common.edit }}**.

   1. Select and configure the caching options:

      * Under **{{ ui-key.yacloud.cdn.label_resource-cache-cdn-cache }}**:

         * Enable **{{ ui-key.yacloud.cdn.label_resource-cache-cdn-cache-enabled }}**.
         * Select the setting type: `{{ ui-key.yacloud.cdn.label_resource-cache-cdn-cache-settings-type-source-settings }}` or `{{ ui-key.yacloud.cdn.label_resource-cache-cdn-cache-settings-type-custom-settings }}`.
         * Select the cache lifetime from the list.
         * (Optional) For the `{{ ui-key.yacloud.cdn.label_resource-cache-cdn-cache-settings-type-custom-settings }}` setting type, set the cache lifetime for the required HTTP response codes.

      * Under **{{ ui-key.yacloud.cdn.label_resource-cache-browser-cache }}**:

         * Enable **{{ ui-key.yacloud.cdn.label_resource-cache-browser-cache-enabled }}**.

   1. (Optional) Under **{{ ui-key.yacloud.cdn.label_additional }}**:

      * Select the option to ignore Cookies.
      * Select the option to ignore the Query parameters.

   1. Click **{{ ui-key.yacloud.common.save }}**.

- CLI

   {% include [include](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

   1. View a description of the CLI update resource command:

      ```bash
      yc cdn resource update --help
      ```

   1. Get a list of all resources in the default folder:

      ```bash
      yc cdn resource list --format yaml
      ```

      Result:

      ```bash
      id: someidkfjqjfl325fw
      folder_id: somefolder7p3l5eobbd
      cname: testexample.com
      created_at: "2022-01-19T09:23:57.921365Z"
      updated_at: "2022-01-19T10:55:30.305141Z"
      active: true
      options:
        edge_cache_settings:
          enabled: true
          default value: "345600"
        cache_http_headers:
          enabled: true
          value:
          - content-type
          - content-length
          - connection
          - server
          - date
          - test
        stale:
          enabled: true
          value:
          - error
          - updating
        allowed_http_methods:
          value:
          - GET
          - POST
          - HEAD
          - OPTIONS
      origin_group_id: "89783"
      origin_group_name: My origins group
      origin_protocol: HTTP
      ssl_certificate:
        type: DONT_USE
        status: READY
      ```

   1. To change the cache lifetime, run:

      ```bash
      yc cdn resource update <resource ID> \
        --cache-expiration-time <time in seconds> \
        --browser-cache-expiration-time <time in seconds> \
        --ignore-cookie true \
        --ignore-query-string
      ```

      Where:

      * `--cache-expiration-time`: Cache lifetime in seconds.
      * `--browser-cache-expiration-time`: Browser cache lifetime in seconds.
      * `--ignore-cookie`: If `true`, ignores cookies.
      * `--ignore-query-string`: Ignore query parameters.

      For more information about the `yc cdn resource update` command, see the [CLI reference](../../../cli/cli-ref/managed-services/cdn/resource/update.md).

- {{ TF }}

   {% include [terraform-install](../../../_includes/terraform-install.md) %}

   1. In the configuration file, describe the parameters of the `yandex_cdn_resource` CDN resource to create:

      
      ```hcl
      terraform {
        required_providers {
          yandex = {
            source  = "yandex-cloud/yandex"
            version = "0.69.0"
          }
        }
      }

      provider "yandex" {
        token     = "<OAuth>"
        cloud_id  = "<cloud ID>"
        folder_id = "<folder ID>"
        zone      = "<availability zone>"
      }

      resource "yandex_cdn_resource" "my_resource" {
          cname               = "cdn1.yandex-example.ru"
          active              = false
          origin_protocol     = "https"
          secondary_hostnames = ["cdn-example-1.yandex.ru", "cdn-example-2.yandex.ru"]
          origin_group_id     = yandex_cdn_origin_group.my_group.id
          options {
            edge_cache_settings    = "345600"
            browser_cache_settings = "1800"
            ignore_cookie          = true
            ignore_query_params    = false
          }

      }
      ```



      Where:

      * `cname`: Primary domain name used for content distribution. This is a required parameter.
      * `active`: Flag indicating whether content is available to end users. `True`: Content from the CDN is available to clients. This is an optional parameter. The default value is `true`.
      * `origin_protocol`: Origin protocol. This is an optional parameter. The default value is `http`.
      * `secondary_hostnames`: Additional domain names. This is an optional parameter.
      * `origin_group_id`: ID of the [origin group](../../concepts/origins.md). This is a required parameter. Use the ID from the description of the origin group in the `yandex_cdn_origin_group` resource.
      * The `options` section contains additional parameters of CDN resources:
         * `browser_cache_settings`: Browser cache lifetime in seconds. This is an optional parameter. The default value is `0`.
         * `edge_cache_settings`: Cache lifetime for response codes in seconds. This is an optional parameter. The default value is `345600`.
         * `ignore_query_params`: Ignore query parameters. This is an optional parameter. The default value is `false`.
         * `ignore_cookie`: Ignore cookies. This is an optional parameter. The default value is `false`.

      For more information about `yandex_cdn_resource` parameters in {{ TF }}, see the [provider documentation]({{ tf-provider-resources-link }}/cdn_resource).

   1. In the command line, go to the directory with the {{ TF }} configuration file.

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

      You can check the changes to the CDN resource in the [management console]({{ link-console-main }}) or using the [CLI](../../../cli/quickstart.md):

      ```
      yc cdn resource list
      ```

- API

   Use the [update](../../api-ref/Resource/update.md) REST API method for the [Resource](../../api-ref/Resource/index.md) resource or the [ResourceService/Update](../../api-ref/grpc/resource_service.md#Update) gRPC API call.

{% endlist %}

{% include [after-changes-tip](../../../_includes/cdn/after-changes-tip.md) %}