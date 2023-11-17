# Set up a secure network configuration

## Reserve two static public IP addresses {#reserve-ips}

For your internet service to run, you need two static public IP addresses: one to be assigned to the VPN gateway and the other to the network load balancer.

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), open **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}** in the folder where you want to reserve the IP addresses.
   1. Open the **{{ ui-key.yacloud.vpc.switch_addresses }}** tab. Click **{{ ui-key.yacloud.vpc.addresses.button_create }}**.
   1. In the window that opens, select the `{{ region-id }}-b` availability zone. Click **{{ ui-key.yacloud.vpc.addresses.popup-create_button_create }}**.
   1. Click **{{ ui-key.yacloud.vpc.addresses.button_create }}** once again.
   1. In the window that opens, select the `{{ region-id }}-a` availability zone. Click **{{ ui-key.yacloud.vpc.addresses.popup-create_button_create }}**.

   {% note info %}

   {% include [zone-c-deprecation](../../_includes/vpc/zone-c-deprecation.md) %}

   {% endnote %}

{% endlist %}

## Create VMs for the service in all availability zones {#create-vms}

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), open your folder and click **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}**. Select **{{ ui-key.yacloud.iam.folder.dashboard.value_compute }}**.
   1. Enter the VM name: `web-node-a`.
   1. Select the `{{ region-id }}-a` availability zone.
   1. Under **{{ ui-key.yacloud.compute.instances.create.section_image }}**, go to the **{{ ui-key.yacloud.compute.instances.create.image_value_marketplace }}** tab, click **{{ ui-key.yacloud.compute.instances.create.image_button_show-all-products }}**, and select the [Drupal](https://cloud.yandex.com/en-ru/marketplace/products/f2e90bncf96u25a9cirp) image.
   1. Under **{{ ui-key.yacloud.compute.instances.create.section_network }}**, select the `subnet-a` subnet. Under **{{ ui-key.yacloud.component.compute.network-select.field_external }}**, select `{{ ui-key.yacloud.compute.instances.create.value_address-none }}`.
   1. In the **{{ ui-key.yacloud.compute.instances.create.section_access }}** field, enter the login and SSH key to access the VM.
   1. Click **{{ ui-key.yacloud.compute.instances.create.button_create }}**.
   1. Repeat the steps for VMs named `web-node-b` and `web-node-c`. Create them in the `{{ region-id }}-b` and `{{ region-id }}-c` availability zones and connect them to `subnet-b` and `subnet-c`, respectively.

{% endlist %}

## Create an IPSec instance for remote access {#create-ipsec-instance}

To provide secure access to your resources, create an IPSec instance.

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), open your folder and click **{{ ui-key.yacloud.iam.folder.dashboard.button_add }}**. Select **{{ ui-key.yacloud.iam.folder.dashboard.value_compute }}**.
   1. Enter the VM name: `vpc`.
   1. Select the `{{ region-id }}-a` availability zone.
   1. Under **{{ ui-key.yacloud.compute.instances.create.section_image }}**, go to the **{{ ui-key.yacloud.compute.instances.create.image_value_marketplace }}** tab, click **{{ ui-key.yacloud.compute.instances.create.image_button_show-all-products }}**, and select the [IPSec instance](https://cloud.yandex.com/marketplace/products/f2e70ohdvsd0jgp2302j) image.
   1. Under **{{ ui-key.yacloud.compute.instances.create.section_network }}**, select the `subnet-a` subnet. Under **{{ ui-key.yacloud.component.compute.network-select.field_external }}**, select a reserved IP address from the list.
   1. In the **{{ ui-key.yacloud.compute.instances.create.section_access }}** field, enter the login and SSH key to access the VM.
   1. Click **{{ ui-key.yacloud.compute.instances.create.button_create }}**.

{% endlist %}

## Configure VPN routing {#vpn-routing}

Configure routing between the remote network and your IPSec instance.  In the example, we'll use the subnet `192.168.0.0/24`.

### Create a route table {#create-route-table}

Create a route table and add [static routes](../../vpc/concepts/static-routes.md):

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), open the **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}** section in the folder where you want to configure routing.
   1. Select the network to create the route table in.
   1. In the left-hand panel, select ![image](../../_assets/vpc/route-tables.svg) **{{ ui-key.yacloud.vpc.network.switch_route-table }}**.
   1. Click **{{ ui-key.yacloud.common.create }}**.
   1. Enter the route table name: `vpn-route`.
   1. Click **{{ ui-key.yacloud.vpc.route-table-form.label_add-static-route }}**.
   1. In the window that opens, enter the remote site's destination subnet prefix. In this example, it is `192.168.0.0/24`.
   1. In the **{{ ui-key.yacloud.vpc.add-static-route.field_next-hop-address }}** field, enter the internal IP address of the IPSec gateway. Click **{{ ui-key.yacloud.vpc.add-static-route.button_add }}**.
   1. Click **{{ ui-key.yacloud.vpc.route-table.create.button_create }}**.

{% endlist %}

### Link the route table to all subnets {#associate-route-table}

To use static routes, link the route table to a subnet. To do this:

{% list tabs %}

- Management console

   1. In [the management console]({{ link-console-main }}), open the **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}** in the folder where you want to configure routing.
   1. Select the network with the subnets to assign the route table to.
   1. In the required subnet row, click ![image](../../_assets/options.svg).
   1. In the menu that opens, select **{{ ui-key.yacloud.vpc.subnetworks.button_action-add-route-table }}**.
   1. In the window that opens, select the created table from the list.
   1. Click **{{ ui-key.yacloud.vpc.subnet.add-route-table.button_add }}**.
   1. Link the `vpn-route` route table to all three subnets.

{% endlist %}

## Create and configure security groups {#create-security-group}

To distribute traffic between network segments, create security groups and set up rules for receiving and sending traffic.

### Create a security group for a VPN {#create-vpn-sg}

For a VPN to work properly, allow receiving and transmitting traffic to UDP ports `500` and `4500` from an external network. This is required for using the IPSec tunnel. You also need to allow traffic between the subnets of your virtual network and the network on the remote site.

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), open **{{ ui-key.yacloud.iam.folder.dashboard.label_vpc }}** in the folder where you want to create a security group.
   1. In the left-hand panel, select ![image](../../_assets/vpc/security-group.svg) **{{ ui-key.yacloud.vpc.switch_security-groups }}**.
   1. Click **{{ ui-key.yacloud.vpc.network.security-groups.button_create }}**.
   1. Enter the security group name: `vpn-sg`.
   1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-network }}** field, select the network that the security group will refer to.
   1. Under **{{ ui-key.yacloud.vpc.network.security-groups.forms.label_section-rules }}**, create traffic management rules:
      1. Select the **{{ ui-key.yacloud.vpc.network.security-groups.label_egress }}** tab.
      1. Click **{{ ui-key.yacloud.vpc.network.security-groups.button_add-rule }}**.
      1. In the window that opens, set the port to `500` in the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}** field.
      1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}** field, select `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_udp }}`.
      1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}** field, specify the public address of a remote VPN hub with mask `32`.
   1. Click **{{ ui-key.yacloud.common.save }}**.
   1. Click **{{ ui-key.yacloud.vpc.network.security-groups.button_add-rule }}**.
      1. In the window that opens, set the port to `4500` in the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}** field.
      1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}** field, select `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_udp }}`.
      1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}** field, specify the public address of a remote VPN hub with mask `32`.
   1. Click **{{ ui-key.yacloud.common.save }}**.
   1. Set up rules that allow traffic between the web servers and VMs on the remote site. Click **{{ ui-key.yacloud.vpc.network.security-groups.button_add-rule }}**.
      1. In the window that opens, click **{{ ui-key.yacloud.vpc.network.security-groups.forms.button_select-all-port-range }}** in the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}** field.
      1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}** field, select `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}`.
      1. In the **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}** field, specify the internal network CIDR: `10.0.0.0/8`.
      1. Click **{{ ui-key.yacloud.vpc.network.security-groups.forms.button_add-cidr }}** and specify the remote site CIDR: `192.168.0.0/24`.
   1. Create the same rules for incoming traffic.

{% endlist %}

### Create a security group for the internet service VMs {#create-service-sg}

Create a security group named `web-service-sg` and set up traffic rules.

#### Rules for outgoing traffic {#web-service-egress}

Allow outgoing connections to other VM instances in the security group:

* **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}`
* **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-sg }}`
* **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-sg-type }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-sg-type-self }}`

#### Rules for incoming traffic {#web-service-ingress}

Allow the following incoming connections:

1. HTTP connections from multiple test dummy IP addresses:
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_tcp }}`
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `80`
   * **{{ ui-key.yacloud.vpc.network.security-groups.label_destination-type-cidr }}**: `1.1.1.1/32`, `85.32.45.45/32`
1. HTTPS connections from multiple test dummy IP addresses:
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_tcp }}`
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `443`
   * **{{ ui-key.yacloud.vpc.network.security-groups.label_destination-type-cidr }}**: `1.1.1.1/32`, `85.32.45.45/32`
1. TCP connections for SSH access:
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_tcp }}`
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `22`
   * **{{ ui-key.yacloud.vpc.network.security-groups.label_destination-type-cidr }}**: `0.0.0.0/0`
1. Connections from other VM instances in the security group:
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}`
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-destination }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-destination-sg }}`
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-sg-type }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_sg-rule-sg-type-self }}`
1. Health checks from the network load balancer:
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_any }}`
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `80`
   * **{{ ui-key.yacloud.vpc.network.security-groups.label_destination-type-cidr }}**: `198.18.235.0/24` and `198.18.248.0/24`

## Assign the security groups to the VMs {#assign-security-groups-vm}

For the security group rules to take effect, assign the groups to the VM network interfaces.

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), open **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
   1. Select the `vpn` VM.
   1. Under **{{ ui-key.yacloud.compute.instance.overview.section_network }}**, click ![options](../../_assets/options.svg) and select **{{ ui-key.yacloud.compute.instance.overview.button_edit-network-interface }}**.
   1. In the window that opens, select the `vpn-sg` security group in the **{{ ui-key.yacloud.compute.instance.edit-network-interface.field_security-groups-ids }}** field.
   1. Click **{{ ui-key.yacloud.common.save }}**.
   1. Repeat the steps and assign the `web-service-sg` security group to the `web-node-a`, `web-node-b`, and `web-node-c` VMs.

{% endlist %}

## Create a network load balancer {#create-load-balancer}

The network load balancer will distribute the internet service's incoming traffic across the VMs in the target group.

{% list tabs %}

- Management console

   To create a network load balancer:
   1. In the [management console]({{ link-console-main }}), open **{{ ui-key.yacloud.iam.folder.dashboard.label_load-balancer }}** in the folder to create the load balancer in.
   1. Click **{{ ui-key.yacloud.load-balancer.network-load-balancer.button_create }}**.
   1. Enter the load balancer name: `web-service-lb`.
   1. In the **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_address-type }}** field, select `{{ ui-key.yacloud.common.label_list }}` and specify a static public address.
   1. Under **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.section_listeners }}**, click **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_add-listener }}**.
   1. In the window that opens, enter a name for the listener and specify port `80` in the **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.field_listener-port }}** and **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.field_listener-target-port }}** fields.  Click **{{ ui-key.yacloud.common.add }}**.
   1. Under **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.section_target-groups }}**, click **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_add-target-group }}**.
   1. In the **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.label_target-group-id }}** field, click the list and then click ![image](../../_assets/plus-sign.svg) **{{ ui-key.yacloud.load-balancer.network-load-balancer.form.button_create-target-group }}**.
   1. In the window that opens, enter the target group name: `web-tg`.
   1. Select the `web-node-a`, `web-node-b`, and `web-node-c` VMs.
   1. Click **{{ ui-key.yacloud.common.create }}**.
   1. Select the created target group from the list.
   1. Click **{{ ui-key.yacloud.common.create }}**.

{% endlist %}

## Test the infrastructure {#test}

Test the infrastructure and make sure that traffic to the internet service VMs only comes from the addresses allowed by the rules:

1. On your computer, run the `curl <Network load balancer public IP address>` command. Make sure no response is received.
1. Create a security group named `web-service-test-sg` with no rules and assign it to the `web-node-a`, `web-node-b` and `web-node-c` VMs.
1. In the `web-service-test-sg` security group, create the following rule for incoming traffic:
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-protocol }}**: `{{ ui-key.yacloud.vpc.network.security-groups.forms.value_tcp }}`
   * **{{ ui-key.yacloud.vpc.network.security-groups.forms.field_sg-rule-port-range }}**: `80`
   * **{{ ui-key.yacloud.vpc.network.security-groups.label_destination-type-cidr }}**: `<IP address of your computer>/32`
1. Run the `curl <Network load balancer public IP address>` command on your computer once again. Make sure the Drupal homepage HTML code is returned in response.
1. Delete the test security group.

## Delete the resources you created {#clear-out}

To stop paying for the deployed resources, delete the created [VMs](../../compute/operations/vm-control/vm-delete.md) and the [load balancer](../../network-load-balancer/operations/load-balancer-delete.md):
* `vpn`
* `web-node-a`
* `web-node-b`
* `web-node-c`
* `web-service-lb`

Release and [delete](../../vpc/operations/address-delete.md) the static public IP addresses you reserved.