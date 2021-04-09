---
title: Linux VM からデータ ディスクを切断する - Azure
description: Azure CLI または Azure portal を使用して、Azure の仮想マシンからデータ ディスクをデタッチする方法について説明します。
author: roygara
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: 29a2cbbf2c390b81aa62b064a7cf93decbaa7457
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565990"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>データ ディスクを Linux 仮想マシンから切断する方法

仮想マシンに接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 そうすれば、ディスクは仮想マシンから削除されますが、ストレージからは削除されません。 この記事では、Ubuntu LTS 16.04 ディストリビューションを使用します。 別のディストリビューションを使用している場合は、ディスクのマウントを解除する手順が異なることがあります。

> [!WARNING]
> ディスクを切断した場合、自動的には削除されません。 Premium Storage のサブスクリプションにお申込みいただいている場合は、ディスクのストレージ料金が引き続き発生します。 詳細については、[Premium Storage 利用時の料金と課金](https://azure.microsoft.com/pricing/details/storage/page-blobs/)に関する記事を参照してください。

再びディスク上の既存のデータを使用する場合は、同じ仮想マシンや別の仮想マシンに再接続できます。  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>VM に接続してディスクのマウントを解除する

CLI またはポータルを使用してディスクをデタッチするには、事前にディスクのマウントを解除し、fstab ファイルからディスクへの参照を削除する必要があります。

VM に接続します この例では、VM のパブリック IP アドレスは *10.0.1.4*、ユーザー名は *azureuser* です。 

```bash
ssh azureuser@10.0.1.4
```

最初に、デタッチするデータ ディスクを見つけます。 次の例では、SCSI ディスクでのフィルター処理に dmesg を使用します。

```bash
dmesg | grep SCSI
```

出力は次の例のようになります。

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

ここでは、*sdc* がデタッチするディスクです。 また、ディスクの UUID も取得する必要があります。

```bash
sudo -i blkid
```

出力は次の例のようになります。

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


*/etc/fstab* ファイルを編集して、ディスクへの参照を削除します。 

> [!NOTE]
> **/etc/fstab** ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

テキスト エディターで次のように */etc/fstab* ファイルを開きます。

```bash
sudo vi /etc/fstab
```

この例では、次の行を */etc/fstab* ファイルから削除する必要があります。

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

`umount` を使用してディスクのマウントを解除します。 次の例では、 */datadrive* マウント ポイントから */dev/sdc1* パーティションのマウントを解除します。

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Azure CLI を使用してデータ ディスクを切断する 

この例では、*myResourceGroup* の *myVM* という VM から *myDataDisk* ディスクをデタッチします。

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

ディスクはストレージに残りますが、仮想マシンには接続されていません。


## <a name="detach-a-data-disk-using-the-portal"></a>ポータルを使用してデータ ディスクを切断する方法

1. 左側のメニューで **[Virtual Machines]** を選択します。
1. 仮想マシンのブレードで、 **[ディスク]** を選択します。
1. **[ディスク]** ブレードで、デタッチしたいデータ ディスクの右端にある **[X]** ボタンを選択して、ディスクをデタッチします。
1. ディスクが削除されたら、ブレードの上部にある **[保存]** を選択します。

ディスクはストレージに残りますが、仮想マシンには接続されていません。 このディスクは削除されません。

## <a name="next-steps"></a>次のステップ
データ ディスクを再利用する場合は、[別の VM にそのデータ ディスクをアタッチ](add-disk.md)します。

ディスクを削除して、ストレージ コストが発生しなくなるようにするには、「[接続されていない Azure マネージド ディスクとアンマネージド ディスクを見つけて削除する - Azure portal](../disks-find-unattached-portal.md)」を参照してください。