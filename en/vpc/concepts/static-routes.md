---
description: "With static routing, you can route traffic from a subnet to the specified IP address ranges through the VMs specified as the next hop. Routing is based on route tables. Route tables are linked to a subnet and cannot contain duplicate prefixes."
keywords:
  - static routing
  - route table
  - routing
---

# Static routes and route tables

When you create a virtual machine (VM) in {{ yandex-cloud }}, it receives a [set of parameters](../../compute/concepts/network.md) for configuring its network environment from the virtual network. The virtual network transmits the values of these parameters to the VM using DHCP. The required network environment parameters for VMs include:

* [Internal IP addresses](../../compute/concepts/network.md#internal-ip) for each network interface of the VM.
* Subnet mask: Defines the size of the [subnet](./network.md#subnet) the VM's network interface connects to.
* [Host name and FQDN of the VM](../../compute/concepts/network.md#hostname).
* Default gateway: The first IP address on the subnet to which the VM's network interface is connected. It will receive all outbound traffic from the VM to the outside world.

## VM route table {#rt-vm}

In {{ yandex-cloud }}, VM instances are typically created with a single network interface. At the time of creation, the VM's route table includes only one route: the one to the default gateway with the prefix `0.0.0.0/0`. For this route (prefix), the gateway is always the **first IP address** on the subnet to which the VM's network interface is connected.

Let's assume a VM's network interface is connected to a subnet with the prefix `192.168.10.0/24`. When the VM was created, its network interface was assigned the IP address `192.168.10.5` on the subnet. The route table for the VM will appear as follows:

```bash
ip route
default via 192.168.10.1 dev eth0 proto dhcp src 192.168.10.5 metric 100
```

This means that all traffic bound for the virtual network must go through the gateway `192.168.10.1` (`eth0` interface).

{% note alert %}

Changing the IP address for the default gateway in the VM's route table may lead to a complete loss of connectivity with the VM.

{% endnote %}

If using a VM with multiple network interfaces, keep in mind that the virtual network will configure a different default gateway for each network interface. To prevent routing conflicts, leave only one default gateway by using the `ip route del` command to delete the route table entries associated with the other gateways.

If you create a VM with multiple network interfaces, the route table within the VM will only allow you to select the network interface for outgoing traffic based on specific destination IP prefixes.

## {{ vpc-short-name }} route tables {#rt-vpc}

VM route tables do not support forwarding traffic directly from one VM within a subnet to another. In all cases, traffic is directed to the virtual network's default gateway.

If you need granular routing at the virtual network level, use {{ vpc-short-name }} route tables. This {{ yandex-cloud }} tool can be useful when processing network traffic on specialized VM instances, such as firewalls, NGFWs, secure gateways, and VPNs.

{{ vpc-short-name }} route tables enable you to control the routing of IPv4 traffic for VM instances. {{ vpc-name }} does not currently support IPv6 protocol.

{{ vpc-short-name }} route tables are created within [cloud networks](https://cloud.yandex.ru/docs/vpc/concepts/network#network) and can be applied to any [subnet](https://cloud.yandex.ru/docs/vpc/concepts/network#subnet) on the same network. You cannot apply a route table to subnets belonging to a different cloud network.

A {{ vpc-short-name }} route table can include one or multiple entries. Each entry defines a static route within the virtual network.

### Static routes {#static}

Each {{ vpc-short-name }} route table entry must include:

* `Destination prefix`: Prefix of the destination IPv4 route in CIDR notation, e.g., `10.20.30.0/24`.
* `Next hop`: Type of the gateway that will handle outgoing traffic for the specified destination prefix. Allowed values include:
   * `IP address`: IP address of the destination gateway, e.g., the [internal IP of a VM](../../compute/concepts/network.md#internal-ip) within one of the subnets.
   * `Gateway`, to send traffic through a [NAT gateway](./gateways.md#nat-gateway). For this gateway type, specify the name of an already existing NAT gateway on the cloud network.

If you create multiple entries with overlapping prefixes, the prefix with the larger subnet mask will have higher priority. For example, between two entries with the `172.16.0.0/20` and `172.16.0.0/24` destination prefixes, the entry with the `172.16.0.0/24` prefix will be used for sending traffic, as it has higher priority.

When creating a static route with an `IP address` as the `next hop`, you can specify an unused internal IP address on the cloud network. In this case, the virtual network will discard all traffic to the destination prefix of the route until you run a VM with that IP address.

Static routes can use the default route prefix, `0.0.0.0/0`.This means that all traffic not directed through more specific routes will be sent through the gateway IP address specified for this prefix.

When creating a static route with a `Gateway` as the `next hop`, you can only specify the default route prefix `0.0.0.0/0` in the `Destination prefix`. This `next hop` type does not support other prefixes.


## Limitations {#restrictions}

1. A {{ vpc-short-name }} route table can only have one entry per destination prefix. Duplicating destination prefixes within the same {{ vpc-short-name }} route table is not allowed. This also applies to the default route prefix `0.0.0.0/0`.
1. {{ vpc-short-name }} route tables cannot use link-local IP address prefixes, including `169.254.0.0/16` and more specific prefixes, as they are reserved for internal use by {{ vpc-name }}.
1. You cannot use the IP address of a load balancer's [traffic listener](../../network-load-balancer/concepts/listener.md) as the `next hop`.
1. When using {{ vpc-short-name }} route tables to route reverse traffic from internal load balancer [targets](../../network-load-balancer/concepts/target-resources.md), consider the [specifics of traffic routing](../../network-load-balancer/concepts/specifics.md#nlb-int-routing).
1. You cannot use IP addresses of an application-level load balancer's [traffic listener](../../application-load-balancer/concepts/application-load-balancer.md#listener) as the `next hop`.
1. A {{ yandex-cloud }} virtual network does not allow transmitting traffic through itself. In other words, only [private IP address ranges in {{ vpc-name }}](../../vpc/concepts/network.md#subnet) can be used as destination prefixes and gateways for static routes in {{ vpc-short-name }} route tables. Traffic to public destination prefixes or gateways with public IP addresses in the {{ vpc-short-name }} route table will be discarded.
1. To learn more about the quantitative restrictions on the use of route tables and static routes, see [Quotas and limits](./limits.md#vpc-quotas) in the {{ vpc-name }} documentation.


## Static route use cases {#refs}

1. [Creating and setting up a NAT gateway](../operations/create-nat-gateway.md).
1. [Routing through a NAT instance](../../tutorials/routing/nat-instance.md).
1. [Creating an IPSec VPN tunnel](../../tutorials/routing/ipsec-vpn.md).
1. [Creating and configuring a UserGate gateway in firewall mode](../../tutorials/routing/usergate-firewall.md).
1. [Implementing a secure high-availability network infrastructure with a dedicated DMZ based on the Next-Generation Firewall](../../tutorials/routing/high-accessible-dmz.md).
