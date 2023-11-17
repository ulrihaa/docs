# Updating a disk

After you create a [disk](../../concepts/disk.md), you can:
* [Change the name and description of a disk](#change-disk-name).
* [Increase the disk size](#change-disk-size), including that of a disk attached to a [running](../../concepts/vm-statuses.md#list-of-statuses) VM.

## Changing the name and description of a disk {#change-disk-name}

To change the name and description of a disk:

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the [folder](../../../resource-manager/concepts/resources-hierarchy.md#folder) where you will be editing a disk.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
   1. In the left-hand panel, select ![image](../../../_assets/compute/disks-pic.svg) **{{ ui-key.yacloud.compute.switch_disks }}**.
   1. Click ![image](../../../_assets/horizontal-ellipsis.svg) next to the desired disk and select **{{ ui-key.yacloud.compute.disks.button_action-edit }}**.
   1. Change the name and description of the disk.
   1. Click **{{ ui-key.yacloud.compute.disks.edit.button_update }}**.

   {{ compute-name }} will start the operation to update the disk.

- CLI

   {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

   1. View the description of the CLI's disk change command:

      ```bash
      {{ yc-compute }} disk update --help
      ```

   1. Request a list of available disks:

      {% include [compute-disk-list](../../../_includes/compute/disk-list.md) %}

   1. Select the `ID` or `NAME` of the required disk (for example, `first-disk`).
   1. Specify the name and description in the disk change command:

      ```bash
      {{ yc-compute }} disk update first-disk \
        --new-name updated-disk \
        --description "Updated disk via CLI"
      ```

      {{ compute-name }} will start the operation to update the disk.

- API

  To change the disk name or description, use the [update](../../api-ref/Disk/update.md) REST API method for the [Disk](../../api-ref/Disk/index.md) resource or the [DiskService/Update](../../api-ref/grpc/disk_service.md#Update) gRPC API call.

  To request the list of available disks, use the [list](../../api-ref/Disk/list.md) REST API method or the [DiskService/List](../../api-ref/grpc/disk_service.md#List) gRPC API call.

{% endlist %}

## Increasing disk size {#change-disk-size}

{% note info %}

You can only change the size of a disk by increasing it. You cannot reduce the size of a disk.

{% endnote %}

You can increase the disk size even on a [running](../../concepts/vm-statuses.md#list-of-statuses) VM.

{% list tabs %}

- Management console

   1. In the [management console]({{ link-console-main }}), select the folder where the disk is located.
   1. In the list of services, select **{{ ui-key.yacloud.iam.folder.dashboard.label_compute }}**.
   1. In the left-hand panel, select ![image](../../../_assets/compute/disks-pic.svg) **{{ ui-key.yacloud.compute.switch_disks }}**.
   1. Click ![image](../../../_assets/horizontal-ellipsis.svg) next to the required disk and select **{{ ui-key.yacloud.compute.disks.button_action-edit }}**.
   1. Increase the disk size.
   1. Click **{{ ui-key.yacloud.compute.disks.edit.button_update }}**.

      {{ compute-name }} will launch the operation to change the disk size.

- CLI

   {% include [cli-install](../../../_includes/cli-install.md) %}

   {% include [default-catalogue](../../../_includes/default-catalogue.md) %}

   1. View the description of the CLI's disk change command:

      ```bash
      {{ yc-compute }} disk update --help
      ```

   1. Request a list of available disks:

      {% include [compute-disk-list](../../../_includes/compute/disk-list.md) %}

   1. Select the `ID` or `NAME` of the required disk (for example, `first-disk`).
   1. Specify the size (for example, 32 GB) in the disk change command:

      ```bash
      {{ yc-compute }} disk update first-disk \
        --size 32
      ```

      {{ compute-name }} will launch the operation to change the disk size.

- API

  You can increase the disk size by using the [update](../../api-ref/Disk/update.md) REST API method for the [Disk](../../api-ref/Disk/) resource or the [DiskService/Update](../../api-ref/grpc/disk_service.md#Update) gRPC API call.

  To request the list of available disks, use the [list](../../api-ref/Disk/list.md) REST API method or the [DiskService/List](../../api-ref/grpc/disk_service.md#List) gRPC API call.

{% endlist %}

## Increasing the size of a Linux disk partition {#change-part-size-linux}

After increasing the disk size, you also need to increase its partition and file system. For boot disks, this happens automatically after you restart the VM.

If the disk partition has not increased, or if you mean to increase the size of a non-boot disk, do it manually. The procedure depends on the file system:

{% list tabs %}

- ext4

   1. [Connect](../../operations/vm-connect/ssh.md) to the VM over SSH:

      ```bash
      ssh <username>@<VM_public_IP_address>
      ```

   1. See the disks attached to the VM:

      ```bash
      lsblk
      ```

      Result:

      ```text
      NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
      vda    252:0    0  25G  0 disk
      ├─vda1 252:1    0   1M  0 part
      └─vda2 252:2    0  25G  0 part /
      vdb    252:16   0  64G  0 disk
      └─vdb1 252:17   0  32G  0 part /data
      ```

      Disk partitions are listed in the `NAME` column. Partition mount points are shown in the `MOUNTPOINT` column.
   1. If the `MOUNTPOINT` column contains a value for your partition, the partition is mounted. Unmount it:

      ```bash
      sudo umount /data
      ```

      Where `/data` is the partition mount point.
   1. Check and restore the file system:

      ```bash
      sudo e2fsck -f /dev/vdb1
      ```

      Result:

      ```text
      e2fsck 1.44.1 (24-Mar-2018)
      Pass 1: Checking inodes, blocks, and sizes
      Pass 2: Checking directory structure
      Pass 3: Checking directory connectivity
      Pass 4: Checking reference counts
      Pass 5: Checking group summary information
      /dev/vdb1: 11/2097152 files (0.0% non-contiguous), 143890/8388352 blocks
      ```

   1. Change the partition size:

      ```bash
      sudo growpart /dev/vdb 1
      ```

      Where:
      * `/dev/vdb` is the name of the device.
      * `1` is the partition number, so it is separated by a space.

      Result:

      ```text
      CHANGED: partition=1 start=2048 old: size=67106816 end=67108864 new: size=134215647,end=134217695
      ```

   1. Change the file system size:

      ```bash
      sudo resize2fs /dev/vdb1
      ```

      Where `dev/vdb1` is the name of the partition.

      Result:

      ```text
      Resizing the filesystem on /dev/vdb1 to 16776955 (4k) blocks.
      The filesystem on /dev/vdb1 is now 16776955 (4k) blocks long.
      ```

   1. If you unmounted a partition before checking, remount it:

      ```bash
      sudo mount /data
      ```

   1. Make sure that the partition increased:

      ```bash
      lsblk
      ```

      Result:

      ```text
      NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
      vda    252:0    0  25G  0 disk
      ├─vda1 252:1    0   1M  0 part
      └─vda2 252:2    0  25G  0 part /
      vdb    252:16   0  64G  0 disk
      └─vdb1 252:17   0  64G  0 part /data
      ```

- xfs

   1. [Connect](../../operations/vm-connect/ssh.md) to the VM over SSH:

      ```bash
      ssh <username>@<VM_public_IP_address>
      ```

   1. See the disks attached to the VM:

      ```bash
      lsblk
      ```

      Result:

      ```text
      NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
      vda    252:0    0  25G  0 disk
      ├─vda1 252:1    0   1M  0 part
      └─vda2 252:2    0  25G  0 part /
      vdb    252:16   0  64G  0 disk
      └─vdb1 252:17   0  32G  0 part /data
      ```

      Disk partitions are listed in the `NAME` column. Partition mount points are shown in the `MOUNTPOINT` column.

   1. Run this command:

      ```bash
      sudo growpart /dev/vdb 1
      ```

      Where:
      * `/dev/vdb` is the name of the device.
      * `1` is the partition number, so it is separated by a space.

      Result:

      ```text
      CHANGED: partition=1 start=2048 old: size=67106816 end=67108864 new: size=134215647,end=134217695
      ```

   1. Change the file system size:

      ```bash
      sudo xfs_growfs /data -d
      ```

      Where:

      * `/data`: Mount point of the partition you need to increase.
      * `-d`: Partition extension parameter.

      Result:

      ```text
      meta-data=/dev/vdb1              isize=512    agcount=4, agsize=655360 blks
               =                       sectsz=4096  attr=2, projid32bit=1
               =                       crc=1        finobt=1, sparse=1, rmapbt=0
               =                       reflink=1    bigtime=0 inobtcount=0
      data     =                       bsize=4096   blocks=2621440, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0, ftype=1
      log      =internal log           bsize=4096   blocks=2560, version=2
               =                       sectsz=4096  sunit=1 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 2621440 to 11796219
      ```

   1. Make sure that the partition increased:

      ```bash
      lsblk /dev/vdb
      ```

      Result:

      ```text
      NAME   MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
      vdb    252:16   0  64G  0 disk
      └─vdb1 252:17   0  64G  0 part /data
      ```

{% endlist %}

