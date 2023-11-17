# Automatic DNS scaling by cluster size

{{ managed-k8s-name }} supports automatic DNS scaling. The [{{ managed-k8s-name }} cluster](../concepts/index.md#kubernetes-cluster) runs the `kube-dns-autoscaler` app that tunes the number of CoreDNS replicas depending on:
* The number of {{ managed-k8s-name }} cluster [nodes](../concepts/index.md#node-group).
* [The number of vCPUs](../../compute/concepts/performance-levels.md) in the {{ managed-k8s-name }} cluster.

The number of replicas is calculated [by the formulas](#parameters).

To automate DNS scaling:
1. [{#T}](#configure-autoscaler)
1. [{#T}](#test-autoscaler)

If you no longer need automatic scaling, [disable it](#disable-autoscaler).

If you no longer need the resources you created, [delete them](#clear-out).

## Getting started {#before-you-begin}

1. Create {{ managed-k8s-name }} resources:

   {% list tabs %}

   - Manually

     1. {% include [k8s-ingress-controller-create-cluster](../../_includes/application-load-balancer/k8s-ingress-controller-create-cluster.md) %}

     1. {% include [k8s-ingress-controller-create-node-group](../../_includes/application-load-balancer/k8s-ingress-controller-create-node-group.md) %}

     1. [Configure {{ managed-k8s-name }} cluster security groups and node groups](../operations/connect/security-groups.md). The [security group](../../vpc/concepts/security-groups.md) of the {{ managed-k8s-name }} cluster must allow incoming connections on ports `443` and `6443`.

   - Using {{ TF }}

     1. {% include [terraform-install](../../_includes/terraform-install.md) %}
     1. Download [the file with provider settings](https://github.com/yandex-cloud/examples/tree/master/tutorials/terraform/provider.tf). Place it in a separate working directory and [specify the parameter values](../../tutorials/infrastructure-management/terraform-quickstart.md#configure-provider).
     1. Download the [k8s-cluster.tf](https://github.com/yandex-cloud/examples/tree/master/tutorials/terraform/managed-kubernetes/k8s-cluster.tf) configuration file of the {{ managed-k8s-name }} cluster to the same working directory. The file describes:
        * [Network](../../vpc/concepts/network.md#network).
        * [Subnet](../../vpc/concepts/network.md#subnet).
        * [Default security group and rules](../operations/connect/security-groups.md) needed to run the {{ managed-k8s-name }} cluster:
          * Rules for service traffic.
          * Rules for accessing the {{ k8s }} API and managing the {{ managed-k8s-name }} cluster with `kubectl` (through ports 443 and 6443).
        * {{ managed-k8s-name }} cluster.
        * {{ managed-k8s-name }} node group.
        * [Service account](../../iam/concepts/users/service-accounts.md) required to create the {{ managed-k8s-name }} cluster and node group.
     1. Specify the [folder ID](../../resource-manager/operations/folder/get-id.md) in the configuration file:
     1. Run the `terraform init` command in the directory with the configuration files. This command initializes the provider specified in the configuration files and enables you to use the provider resources and data sources.
     1. Make sure the {{ TF }} configuration files are correct using this command:

        ```bash
        terraform validate
        ```

        If there are any errors in the configuration files, {{ TF }} will point them out.
     1. Create the required infrastructure:

        {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

        {% include [explore-resources](../../_includes/mdb/terraform/explore-resources.md) %}

  {% endlist %}

1. {% include [Install and configure kubectl](../../_includes/managed-kubernetes/kubectl-install.md) %}

## Configure kube-dns-autoscaler {#configure-autoscaler}

### Make sure that the app is up and running {#verify-app}

Check the [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/) in the `kube-system` [namespace](../concepts/index.md#namespace):

```bash
kubectl get deployment --namespace=kube-system
```

Result:

```text
NAME                 READY  UP-TO-DATE  AVAILABLE  AGE
...
kube-dns-autoscaler  1/1    1           1          52m
```

### Define the scaling parameters {#parameters}

The `kube-dns-autoscaler` [pod](../concepts/index.md#pod) regularly polls the {{ k8s }} server for the number of {{ managed-k8s-name }} cluster nodes and cores. Based on this data, the number of CoreDNS replicas is calculated.

Two types of calculation are possible:
* Linear mode.
* Ladder mode (a step function).

For more information about calculating, see the [cluster-proportional-autoscaler](https://github.com/kubernetes-sigs/cluster-proportional-autoscaler#calculation-of-number-of-replicas) documentation.

In this example, we use the `linear` mode where calculations follow this formula:

```text
replicas = max( ceil( cores * 1/coresPerReplica ) , ceil( nodes * 1/nodesPerReplica ) )
```

Where:
* `coresPerReplica`: Configuration parameter indicating the number of CoreDNS replicas per vCPU of the {{ managed-k8s-name }} cluster.
* `nodesPerReplica`: Configuration parameter indicating the number of CoreDNS replicas per {{ managed-k8s-name }} cluster node.
* `cores`: Actual number of vCPUs in the {{ managed-k8s-name }} cluster.
* `nodes`: Actual number of nodes in the {{ managed-k8s-name }} cluster.
* `ceil`: Ceiling function that rounds up a decimal number to an integer.
* `max`: Max function that returns the largest of the two values.

The `preventSinglePointFailure` additional parameter is relevant for multi-node {{ managed-k8s-name }} clusters. If `true`, the minimum number of DNS replicas is two.

You can also define the `min` and `max` configuration parameters that set the minimum and maximum number of CoreDNS replicas in the {{ managed-k8s-name }} cluster:

```text
replicas = min(replicas, max)
replicas = max(replicas, min)
```

For more information about calculating, see the [cluster-proportional-autoscaler](https://github.com/kubernetes-sigs/cluster-proportional-autoscaler#calculation-of-number-of-replicas)documentation.

### Change the configuration {#edit-config}

1. Check the current settings.

   In this example, we are creating {{ managed-k8s-name }} `node-group-1` with the following parameters:
   * Number of {{ managed-k8s-name }} nodes: `3`
   * `vCPU cores`: 12

   By default, the `linear` mode and the following scaling parameters are used:
   * `coresPerReplica`: `256`.
   * `nodesPerReplica`: `16`.
   * `preventSinglePointFailure`: `true`.

   ```text
   replicas = max( ceil( 12 * 1/256 ), ceil( 3 * 1/16 ) ) = 1
   ```

   The `preventSinglePointFailure` parameter is `true`, meaning the number of CoreDNS replicas is two.

   To get the `coredns` pod data, run this command:

   ```bash
   kubectl get pods -n kube-system
   ```

   Result:

   ```bash
   NAME                      READY  STATUS   RESTARTS  AGE
   ...
   coredns-7c646474c9-4dmjl  1/1    Running  0         128m
   coredns-7c646474c9-n7qsv  1/1    Running  0         134m
   ```

1. Set new parameters.

   Change the configuration as follows:
   * `coresPerReplica`: `4`.
   * `nodesPerReplica`: `2`.
   * `preventSinglePointFailure`: `true`.

   ```text
   replicas = max( ceil( 12 * 1/4 ), ceil( 3 * 1/2 ) ) = 3
   ```

   To deliver the parameters to the `kube-dns-autoscaler` application, edit the appropriate [ConfigMap](https://kubernetes.io/docs/concepts/configuration/configmap/) using this command:

   ```bash
   kubectl edit configmap kube-dns-autoscaler --namespace=kube-system
   ```

   Once a text editor with the `kube-dns-autoscaler` configuration opens, change the line with the following parameters:

   ```text
   linear: '{"coresPerReplica":4,"nodesPerReplica":2,"preventSinglePointFailure":true}'
   ```

   Save your changes to see the operation output:

   ```text
   configmap/kube-dns-autoscaler edited
   ```

   The `kube-dns-autoscaler` application will upload the configuration and scale the DNS service with the new parameters.

## Test scaling {#test-autoscaler}

### Resize the {{ managed-k8s-name }} cluster {#resize-cluster}

Create a second {{ managed-k8s-name }} node group using this command:

```bash
yc managed-kubernetes node-group create \
  --name node-group-2 \
  --cluster-name dns-autoscaler \
  --location zone={{ region-id }}-a \
  --public-ip \
  --fixed-size 2 \
  --cores 4 \
  --core-fraction 5
```

Result:

```text
done (2m43s)
...
```

Now the {{ managed-k8s-name }} cluster has 5 nodes with 20 vCPUs. Calculate the number of replicas:

```text
replicas = max( ceil( 20 * 1/4 ), ceil( 5 * 1/2 ) ) = 5
```

### Check the changes in the number of CoreDNS replicas {#inspect-changes}

Run this command:

```bash
kubectl get pods -n kube-system
```

Result:

```text
NAME                      READY  STATUS   RESTARTS  AGE
...
coredns-7c646474c9-7l8mc  1/1    Running  0         3m30s
coredns-7c646474c9-n7qsv  1/1    Running  0         3h20m
coredns-7c646474c9-pv9cv  1/1    Running  0         3m40s
coredns-7c646474c9-r2lss  1/1    Running  0         49m
coredns-7c646474c9-s5jgz  1/1    Running  0         57m
```

### Set up the reducing of the number of {{ managed-k8s-name }} nodes {#reduce-nodes}

By default, {{ k8s-ca }} does not reduce the number of nodes in a {{ managed-k8s-name }} node group with auto scaling if these nodes contain pods from the `kube-system` namespace managed by the [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/), [ReplicaSet](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/), or [StatefulSet](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/) application replication controllers, such as CoreDNS pods. In this case, the number of {{ managed-k8s-name }} nodes per group cannot be less than the number of CoreDNS pods.

To allow the number of {{ managed-k8s-name }} nodes to decrease, configure the [PodDisruptionBudget](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) object for them, which enables you to stop two CoreDNS pods at a time:

```bash
kubectl create poddisruptionbudget <pdb name> \
  --namespace=kube-system \
  --selector k8s-app=kube-dns \
  --min-available=2
```

Result:

```text
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: <pdb name>
spec:
  minAvailable: 2
  selector:
    matchLabels:
      k8s-app: kube-dns
```

## Disable scaling {#disable-autoscaler}

Reset the number of replicas in the `kube-dns-autoscaler` application [Deployment](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/):

```bash
kubectl scale deployment --replicas=0 kube-dns-autoscaler --namespace=kube-system
```

Result:

```text
deployment.apps/kube-dns-autoscaler scaled
```

Check the result with this command:

```bash
kubectl get rs --namespace=kube-system
```

Result:

```text
NAME                 READY  UP-TO-DATE  AVAILABLE  AGE
...
kube-dns-autoscaler  0/0    0           0          3h53m
```

## Delete the resources you created {#clear-out}

Delete the resources you no longer need to avoid paying for them:

{% list tabs %}

- Manually

  [Delete the {{ managed-k8s-name }} cluster](../operations/kubernetes-cluster/kubernetes-cluster-delete.md).

- Using {{ TF }}

  To delete the infrastructure [created with {{ TF }}](#deploy-infrastructure):
  1. In the terminal window, go to the directory containing the infrastructure plan.
  1. Delete the `k8s-cluster.tf` configuration file.
  1. Make sure the {{ TF }} configuration files are correct using this command:

     ```bash
     terraform validate
     ```

     If there are any errors in the configuration files, {{ TF }} will point them out.
  1. Confirm updating the resources.

     {% include [terraform-apply](../../_includes/mdb/terraform/apply.md) %}

     All the resources described in the `k8s-cluster.tf` configuration file will be deleted.

{% endlist %}