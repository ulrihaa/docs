# Creating HTTP router for gRPC traffic 

To create an [HTTP router](../concepts/http-router.md) and add a [route](../concepts/http-router.md#routes) to it:

{% list tabs %}

- Management console

   1. In the left-hand menu, select **{{ ui-key.yacloud.alb.label_http-routers }}**.
   1. Click **{{ ui-key.yacloud.alb.button_http-router-create }}**.
   1. Enter the router name.
   1. Under **{{ ui-key.yacloud.alb.label_virtual-hosts }}**, click **{{ ui-key.yacloud.alb.button_virtual-host-add }}**.
   1. Enter **{{ ui-key.yacloud.common.name }}**.
   1. In the **{{ ui-key.yacloud.alb.label_authority }}** field, type `*` or specify the IP address of the load balancer.
   1. Click **{{ ui-key.yacloud.alb.button_add-route }}** and select **{{ ui-key.yacloud.alb.label_route-type }}**: `{{ ui-key.yacloud.alb.label_proto-grpc }}`.
      1. Enter **{{ ui-key.yacloud.common.name }}**.
      1. Under **{{ ui-key.yacloud.alb.label_fqmn }}**, select one of the options:

         * `{{ ui-key.yacloud.alb.label_match-prefix }}` to route all requests starting with a specific FQMN. In the input field, specify the `/<first_word_in_service_name>`, e.g., `/helloworld`.
         * `{{ ui-key.yacloud.alb.label_match-exact }}` to route all requests that match the specified FQMN.
         * `{{ ui-key.yacloud.alb.label_match-regex }}` to route all requests that match a [RE2](https://github.com/google/re2/wiki/Syntax) [regular expression](https://en.wikipedia.org/wiki/Regular_expression).

         {% note warning %}

         The FQMN must start with a slash `/` and contain part of the name of the service that the procedure call is redirected to.

         {% endnote %}

      1. In the **{{ ui-key.yacloud.alb.label_route-action }}** field, select one of the options: `{{ ui-key.yacloud.alb.label_route-action-route }}` or `{{ ui-key.yacloud.alb.label_route-action-statusResponse }}`. Depending on the selected option:

         * `{{ ui-key.yacloud.alb.label_route-action-route }}`:

            * In the **{{ ui-key.yacloud.alb.label_backend-group }}** list, select the backend group name from the same folder where you create the router.
            * (Optional) In the **{{ ui-key.yacloud.alb.label_host-rewrite }}** field, select one of the options:

               * `none`: Do not rewrite.
               * `rewrite`: Rewrite the header to the specified value.
               * `auto`: Automatically rewrite the header to the target VM address.

            * (Optional) In the **{{ ui-key.yacloud.alb.label_timeout }}** field, specify the maximum connection time.
            * (Optional) In the **{{ ui-key.yacloud.alb.label_idle-timeout }}** field, specify the maximum connection keep-alive time without data transmission.

         * `{{ ui-key.yacloud.alb.label_route-action-statusResponse }}`:

            * In the **{{ ui-key.yacloud.alb.label_grpc-status-code }}** field, select the code to be used for response.

   1. Click **{{ ui-key.yacloud.common.create }}**.

- CLI

   {% include [cli-install](../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../_includes/default-catalogue.md) %}

   1. View a description of the CLI command to create an HTTP router:

      ```bash
      yc alb http-router create --help
      ```

   1. Run the following command:

      ```bash
      yc alb http-router create <HTTP_router_name>
      ```

      Result:

      ```text
      id: a5dcsselagj4********
      name: <HTTP_router_name>
      folder_id: aoerb349v3h4********
      created_at: "2022-06-16T21:04:59.438292069Z"
      ```

   1. View a description of the CLI command for creating a virtual host:

      ```bash
      yc alb virtual-host create --help
      ```

   1. Create a virtual host, specifying the name of the HTTP router and the virtual host settings:

      ```bash
      yc alb virtual-host create <virtual_host_name> \
        --http-router-name <HTTP_router_name> \
        --authority *
      ```

      Where `--authority`: Domains for the Host and `:authority` headers that will be associated with this virtual host. Wildcards are supported, for example, `*.foo.com` or `*-bar.foo.com`.

      Result:

      ```text
      done (1s)
      name: <virtual_host_name>
      authority:
      - *
      ```

   1. View a description of the CLI command for adding a host:

      ```bash
      yc alb virtual-host append-grpc-route --help
      ```

   1. Add a route, indicating the router ID or name and the routing parameters:

      ```bash
      yc alb virtual-host append-grpc-route <route_name> \
        --virtual-host-name <virtual_host_name> \
        --http-router-name <HTTP_router_name> \
        --prefix-fqmn-match / \
        --backend-group-name <backend_group_name> \
        --request-max-timeout 60s
      ```

      Where:

      * `--virtual-host-name`: Virtual host name.
      * `--http-router-name`: HTTP router name.
      * `--prefix-fqmn-match`: Parameter for routing all requests with a given prefix. The parameter should be followed with FQMN `/`.

         To specify a condition for routing, you can also use the following options:
         * `--exact-fqmn-match` to route all requests that match the specified FQMN. The parameter should be followed with `/<FQMN>/`.
         * `--regex-fqmn-match` to route all requests that match a [RE2](https://github.com/google/re2/wiki/Syntax) [regular expression](https://en.wikipedia.org/wiki/Regular_expression). The parameter should be followed with `/<regular_expression>`.
      * `--backend-group-name`: Name of the backend group.
      * `--request-max-timeout`: Maximum request idle timeout, seconds.

      For more information about the `yc alb virtual-host append-grpc-route` command parameters, see the [CLI reference](../../cli/cli-ref/managed-services/application-load-balancer/virtual-host/append-grpc-route.md).

      Result:

      ```text
      done (1s)
      name: <virtual_host_name>
      authority:
      - *
      routes:
      - name: <route_name>
        grpc:
         match:
           fqmn:
            prefix_match: /helloworld
         route:
           backend_group_id: ds7snban2dvn********
           max_timeout: 60s
      ```

- {{ TF }}

   {% include [terraform-definition](../../_tutorials/terraform-definition.md) %}

   {% include [terraform-install](../../_includes/terraform-install.md) %}

   1. In the configuration file, specify the parameters of the HTTP router and virtual host:

      ```hcl
      resource "yandex_alb_http_router" "tf-router" {
        name          = "<HTTP_router_name>"
        labels        = {
          tf-label    = "tf-label-value"
          empty-label = ""
        }
      }

      resource "yandex_alb_virtual_host" "my-virtual-host" {
        name                    = "<virtual_host_name>"
        http_router_id          = yandex_alb_http_router.tf-router.id
        route {
          name                  = "<route_name>"
          grpc_route {
            grpc_route_action {
              backend_group_id  = "<backend_group_ID>"
              max_timeout       = "60s"
            }
          }
        }
      }
      ```

      Where:

      * `yandex_alb_http_router`: HTTP router description:
         * `name`: HTTP router name. The name format is as follows:

            {% include [name-format](../../_includes/name-format.md) %}

         * `labels`: HTTP router [labels](../../resource-manager/concepts/labels.md). Set a key-value pair.
      * `yandex_alb_virtual_host`: Virtual host description:
         * `name`: Virtual host name. The name format is as follows:

            {% include [name-format](../../_includes/name-format.md) %}

         * `http_router_id`: HTTP router ID.
         * `grpc_route`: Description of the route for gRPC traffic:
            * `name`: Route name.
            * `grpc_route_action`: Parameter to indicate an action on gRPC traffic.
               * `backend_group_id`: Backend group ID.
               * `max_timeout`: Maximum request idle timeout, seconds.

      For more information about the parameters of resources used in {{ TF }}, see the provider documentation:

      * [Yandex_alb_http_router]({{ tf-provider-resources-link }}/alb_http_router) resource
      * [Yandex_alb_virtual_host]({{ tf-provider-resources-link }}/alb_virtual_host) resource

   1. Create resources

      {% include [terraform-validate-plan-apply](../../_tutorials/terraform-validate-plan-apply.md) %}

      {{ TF }} will create all the required resources. You can check the new resources and their configuration using the [management console]({{ link-console-main }}) or this [CLI](../../cli/quickstart.md) command:

      ```bash
      yc alb http-router get <HTTP_router_name>
      ```

- API

   Use the [create](../api-ref/HttpRouter/create.md) REST API method for the [HttpRouter](../api-ref/HttpRouter/index.md) resource or the [HttpRouterService/Create](../api-ref/grpc/http_router_service.md#Create) gRPC API call.

{% endlist %}
