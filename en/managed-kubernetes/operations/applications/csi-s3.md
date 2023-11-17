# Installing Container Storage Interface for S3

[Container Storage Interface for S3](/marketplace/products/yc/csi-s3) (_CSI_) enables you to dynamically reserve [buckets](../../../storage/concepts/bucket.md) of S3-compatible storages and mount them in [{{ managed-k8s-name }} cluster](../../concepts/index.md#kubernetes-cluster) [pods](../../concepts/index.md#pod) as [persistent volumes](../../concepts/volume.md#persistent-volume) (_PersistentVolume_). The connection is made using the [FUSE](https://en.wikipedia.org/wiki/Filesystem_in_Userspace) implementation of the [GeeseFS](https://github.com/yandex-cloud/geesefs) file system.

## Creating a service account {#create-sa-key}

1. {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

1. [Create a service account](../../../iam/operations/sa/create.md) with the `storage.editor` [role](../../../iam/concepts/access-control/roles.md).
1. [Create a static access key](../../../iam/operations/sa/create-access-key.md) for the [service account](../../../iam/concepts/users/service-accounts.md). Save the [key](../../../iam/concepts/authorization/key.md) ID and secret key, you will need them when installing the application.
1. (Optional) To make new volumes fit into a single bucket with different prefixes, create an [{{ objstorage-full-name }}](../../../storage/) [bucket](../../../storage/operations/buckets/create.md). Save the bucket name, you will need it when installing the application. Skip this step if you need to create a separate bucket for each volume.

## Installation using {{ marketplace-full-name }} {#marketplace-install}

1. Go to the [folder page]({{ link-console-main }}) and select **{{ ui-key.yacloud.iam.folder.dashboard.label_managed-kubernetes }}**.
1. Click the {{ managed-k8s-name }} cluster name and select the ![image](../../../_assets/marketplace.svg) **{{ ui-key.yacloud.k8s.cluster.switch_marketplace }}** tab.
1. Under **Applications available for installation**, select [Container Storage Interface for S3](/marketplace/products/yc/csi-s3) and click **{{ ui-key.yacloud.marketplace-v2.button_use }}**.
1. Configure the application:
   * **Namespace**: Select the `kube-system` [namespace](../../concepts/index.md#namespace).
   * **Application name**: Specify the application name, e.g., `csi-s3`.
   * **Create storage class**: Select this option to create a new [storage class](../volumes/manage-storage-class.md) when deploying the application.
   * **Create secret**: Select this option to create a new secret for a storage class when installing the application.
   * **S3 key ID**: Copy the service account key ID into this field.
   * **S3 secret key**: Copy the service account secret key into this field.
   * **General S3 bucket for volumes**: Specify the name of the general bucket where [dynamically allocated volumes](../../concepts/volume.md#dynamic-provisioning) will be created. For CSI to create a new bucket for each volume, leave this field blank.
   * **S3 service address**: The address of the S3 service to be used by the application. The default address is `https://{{ s3-storage-host }}`.
   * **GeeseFS mounting options**: Mounting options for GeeseFS. For a complete list of options, see the [GeeseFS documentation](https://github.com/yandex-cloud/geesefs).
   * **Volume cleanup policy**: Select the policy to clean up PersistentVolumes when deleting PersistentVolumeClaims:
     * **Retain**: Retain a volume.
     * **Delete**: Delete a volume.
   * **Storage class name**: If you previously selected the **Create storage class** option, specify the name of the new storage class.
   * **Secret name**: If you previously selected the **Create secret** option, specify the name of the new secret to be created for the storage class. Otherwise, specify the name of the existing secret to be used for the storage class.
   * **Ignore all taints**: Select this option if you want the CSI driver used to mount the file system on nodes to ignore all taints set for the {{ managed-k8s-name }} cluster nodes.
1. Click **{{ ui-key.yacloud.k8s.cluster.marketplace.button_install }}**.
1. Wait for the application to change its status to `Deployed`.

## Installation using a Helm chart {#helm-install}

1. {% include [Install Helm](../../../_includes/managed-kubernetes/helm-install.md) %}
1. {% include [Install and configure kubectl](../../../_includes/managed-kubernetes/kubectl-install.md) %}
1. To install a [Helm chart](https://helm.sh/docs/topics/charts/) with CSI, run the following command:

   ```bash
   export HELM_EXPERIMENTAL_OCI=1 && \
   helm pull oci://{{ mkt-k8s-key.yc_csi-s3.helmChart.name }} \
     --version {{ mkt-k8s-key.yc_csi-s3.helmChart.tag }} \
     --untar && \
   helm install \
     --namespace <namespace> \
     --create-namespace \
     --set secret.accessKey=<key_ID> \
     --set secret.secretKey=<secret_key> \
     csi-s3 .
   ```

When installing a CSI application, the only required parameters are `secret.accessKey` and `secret.secretKey`. You can skip other parameters or redefine them in the install command using the `--set <parameter_name>=<new_value>` key.

The list of parameters available for redefining and their default values are shown in the table below:

| Parameter name | Description | Default value |
--- | --- | ---
`storageClass.create` | Whether a new storage class needs to be created | `true`
`storageClass.name` | Storage class name | `csi-s3`
`storageClass.singleBucket` | Use a single bucket for all PersistentVolumeClaims
`storageClass.mountOptions` | GeeseFS mounting options | `--memory-limit 1000 --dir-mode 0777 --file-mode 0666`
`storageClass.reclaimPolicy` | Volume cleanup policy | `Delete`
`storageClass.annotations` | Description for a storage class
`secret.create` | Whether a new secret needs to be created | `true`
`secret.name` | Secret name | `csi-s3-secret`
`secret.accessKey` | Key ID
`secret.secretKey` | Secret key
`secret.endpoint` | S3 service address | `https://{{ s3-storage-host }}`

## See also {#see-also}

* [CSI specification](https://github.com/container-storage-interface/spec/blob/master/spec.md).
* [Integration with {{ objstorage-name }}](../volumes/s3-csi-integration.md).
* [Working with persistent and dynamic volumes in {{ k8s }}](../../concepts/volume.md).