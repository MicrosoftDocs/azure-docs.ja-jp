---
title: チュートリアル - Azure CLI を使用してスケール セットのディスクを作成および使用する
description: Azure CLI を使用して仮想マシン スケール セットの Managed Disks を作成および使用する方法 (ディスクの追加、準備、一覧表示、切断方法など) を説明します。
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: adaa7d1c2cf4a78a680ef4fbbec06975ceda812b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96433488"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>チュートリアル:Azure CLI を使用した仮想マシン スケール セットのディスクの作成および使用
仮想マシン スケール セットでは、VM インスタンスのオペレーティング システム、アプリケーション、およびデータを格納するためにディスクを使用します。 スケール セットを作成および管理するときは、予測されるワークロードに適したディスクのサイズと構成を選択する必要があります。 このチュートリアルでは、VM ディスクの作成方法と管理方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続および準備

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.29 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="default-azure-disks"></a>既定の Azure ディスク
スケール セットが作成またはスケーリングされると、2 つのディスクが各 VM インスタンスに自動的に接続されます。

**オペレーティング システム ディスク** - オペレーティング システム ディスクは、最大 2 TB までサイズを変更でき、VM インスタンスのオペレーティング システムをホストします。 OS ディスクには既定で */dev/sda* というラベルが付けられています。 OS ディスクのディスク キャッシュ構成は、OS パフォーマンスの向上のために最適化されています。 この構成では、OS ディスクでアプリケーションやデータをホスト **しないでください**。 アプリケーションとデータには、この記事の後半で説明するデータ ディスクを使用してください。

**一時ディスク** - 一時ディスクは、VM インスタンスと同じ Azure ホストに配置されているソリッド ステート ドライブを使用します。 これらは高パフォーマンスのディスクであり、一時的なデータ処理などの操作に使用される場合があります。 ただし、VM インスタンスを新しいホストに移動すると、一時ディスクに格納されているデータはすべて削除されます。 一時ディスクのサイズは VM インスタンスのサイズによって決まります。 一時ディスクには */dev/sdb* のラベルが付けられており、 */mnt* というマウント ポイントがあります。

### <a name="temporary-disk-sizes"></a>一時ディスクのサイズ
| 種類 | 一般的なサイズ | 一時ディスクの最大サイズ (GiB) |
|----|----|----|
| [汎用](../virtual-machines/sizes-general.md) | A、B、D シリーズ | 1600 |
| [コンピューティングの最適化](../virtual-machines/sizes-compute.md) | F シリーズ | 576 |
| [メモリの最適化](../virtual-machines/sizes-memory.md) | D、E、G、M シリーズ | 6144 |
| [ストレージの最適化](../virtual-machines/sizes-storage.md) | L シリーズ | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | N シリーズ | 1440 |
| [高性能](../virtual-machines/sizes-hpc.md) | A および H シリーズ | 2000 |


## <a name="azure-data-disks"></a>Azure データ ディスク
アプリケーションをインストールしたりデータを保存したりする必要がある場合は、データ ディスクをさらに追加できます。 耐久性と応答性の高いデータ ストレージが望ましい状況では、必ず、データ ディスクを使用する必要があります。 各データ ディスクの最大容量は 4 TB です。 VM インスタンス サイズによって、接続できるデータ ディスクの数が決まります。 VM vCPU ごとに、2 つのデータ ディスクを仮想マシンあたり絶対最大数の 64 個のディスクまで接続できます。

## <a name="vm-disk-types"></a>VM ディスクの種類
Azure では、2 種類のディスクを提供しています。

### <a name="standard-disk"></a>Standard ディスク
Standard Storage では、HDD が使用されており、コスト効率とパフォーマンスに優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。

### <a name="premium-disk"></a>Premium ディスク
Premium ディスクは、SSD ベースの高性能で待機時間の短いディスクによってサポートされています。 これらのディスクは、運用ワークロードを実行している VM に適しています。 Premium Storage は、DS シリーズ、DSv2 シリーズ、GS シリーズ、FS シリーズの VM をサポートしています。 ディスク サイズを選択するときは、値を切り上げて 1 つ上の種類にします。 たとえば、ディスク サイズが 128 GB 未満の場合、ディスクの種類は P10 です。 ディスク サイズが 129 ～ 512 GB の場合、サイズは P20 です。 512 GB を超えた場合、サイズは P30 です。

### <a name="premium-disk-performance"></a>Premium ディスクのパフォーマンス
|Premium Storage ディスクの種類 | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ディスク サイズ (切り上げ) | 32 GB | 64 GB | 128 GB | 512 GB | 1,024 GB (1 TB) | 2,048 GB (2 TB) | 4,095 GB (4 TB) |
| ディスクあたりの最大 IOPS | 120 | 240 | 500 | 2,300 | 5,000 | 7,500 | 7,500 |
ディスクあたりのスループット | 25 MB/秒 | 50 MB/秒 | 100 MB/秒 | 150 MB/秒 | 200 MB/s | 250 MB/秒 | 250 MB/秒 |

上記の表は、ディスクあたりの最大 IOPS を割り出していますが、複数のデータ ディスクをストライピングすることによって、より高いレベルのパフォーマンスを実現できます。 たとえば、Standard_GS5 VM では、最大 80,000 IOPS を実現できます。 VM あたりの最大 IOPS の詳細については、[Linux VM のサイズ](../virtual-machines/sizes.md)に関するページを参照してください。


## <a name="create-and-attach-disks"></a>ディスクを作成して接続する
ディスクは、スケール セットの作成時に作成および接続できます。また、既存のスケール セットに対してディスクを作成および接続することもできます。

API バージョン `2019-07-01` 時点では、[storageProfile.osDisk.diskSizeGb](/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk) プロパティを使用して、仮想マシン スケール セットの OS ディスクのサイズを設定できます。 プロビジョニング後、領域全体を活用するために、ディスクの拡張またはパーティション再分割を行う必要がある場合があります。 ディスクの拡張の詳細については、[こちら](../virtual-machines/windows/expand-os-disk.md#expand-the-volume-within-the-os)を参照してください。

### <a name="attach-disks-at-scale-set-creation"></a>スケール セットの作成時にディスクを接続する
まず、[az group create](/cli/azure/group) コマンドでリソース グループを作成します。 この例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az vmss create](/cli/azure/vmss) コマンドを使用して、仮想マシン スケール セットを作成します。 次の例では、*myScaleSet* という名前のスケール セットを作成し、SSH キーが存在しない場合は SSH キーを生成します。 `--data-disk-sizes-gb` パラメーターを使用して 2 つのディスクを作成します。 1 つ目のディスクのサイズは *64* GB、2 つ目のディスクは *128* GB です。

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

すべてのスケール セット リソースと VM インスタンスを作成して構成するには数分かかります。

### <a name="attach-a-disk-to-existing-scale-set"></a>既存のスケール セットにディスクを接続する
既存のスケール セットにディスクを接続することもできます。 前の手順で作成したスケール セットを使用して別のディスクを追加するには、[az vmss disk attach](/cli/azure/vmss/disk) を使用します。 次の例では、*128* GB の追加ディスクを接続します。

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>データ ディスクの準備
作成されてスケール セット VM インスタンスに接続されたディスクは、未フォーマット ディスクです。 これらのディスクをデータおよびアプリケーション用に使用するには、ディスクを準備する必要があります。 ディスクを準備するには、パーティションを作成し、ファイルシステムを作成した後、マウントします。

スケール セット内の複数の VM インスタンスにわたってこのプロセスを自動化するには、Azure カスタム スクリプト拡張機能を使用できます。 この拡張機能を使用すると、各 VM インスタンス上でローカルにスクリプトを実行して、接続されたデータ ディスクの準備などの操作を実行できます。 詳細については、「[Windows のカスタム スクリプト拡張機能](../virtual-machines/extensions/custom-script-linux.md)」を参照してください。

次の例では、すべての未フォーマットの接続されたデータ ディスクを準備する [az vmss extension set](/cli/azure/vmss/extension) を使用して、GitHub サンプル リポジトリにあるスクリプトを各 VM インスタンス上で実行します。

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

ディスクが正常に準備されたことを確認するには、VM インスタンスの 1 つに SSH 接続します。 [az vmss list-instance-connection-info](/cli/azure/vmss) を使用して、スケール セットの接続情報を一覧表示します。

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

次の例に示すように、独自のパブリック IP アドレスとポート番号を使用して最初の VM インスタンスに接続します。

```console
ssh azureuser@52.226.67.166 -p 50001
```

次のように、VM インスタンスのパーティションを確認します。

```bash
sudo fdisk -l
```

次の出力例は、 */dev/sdc*、 */dev/sdd*、および */dev/sde* の 3 つのディスクが VM インスタンスに接続されていることを示しています。 これらの各ディスクにはパーティションが 1 つあり、このパーティションが使用可能なすべての領域を使用しています。

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

次のように、VM インスタンス上のファイルシステムとマウント ポイントを確認します。

```bash
sudo df -h
```

次の出力例は、3 つのディスクのファイルシステムが */datadisks* に正常にマウントされていることを示しています。

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

スケール内の各 VM インスタンスのディスクは、同じように自動的に準備されます。 スケール セットがスケールアップされると、必要なデータ ディスクが新しい VM インスタンスに接続されます。 さらに、ディスクを準備するためにカスタム スクリプト拡張機能も自動的に実行されます。

VM インスタンスへの SSH 接続を閉じます。

```bash
exit
```


## <a name="list-attached-disks"></a>接続されているディスクの一覧表示
スケール セットに接続されているディスクに関する情報を表示するには、[az vmss show](/cli/azure/vmss) と *virtualMachineProfile.storageProfile.dataDisks* に対するクエリを使います。

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

ディスク サイズ、ストレージ層、および LUN (論理ユニット番号) に関する情報が表示されます。 次の出力例では、スケール セットに接続されている 3 つのデータ ディスクの詳細を出力します。

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>ディスクを取り外す
特定のディスクが不要になったら、スケール セットから切断することができます。 ディスクは、スケール セット内のすべての VM インスタンスから削除されます。 スケール セットからディスクを切断するには、[az vmss disk detach](/cli/azure/vmss/disk) を使用し、ディスクの LUN を指定します。 LUN は、前のセクションの [az vmss show](/cli/azure/vmss) の出力に表示されています。 次の例では、スケール セットから LUN *2* を切断します。

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする
スケール セットとディスクを削除するには、[az group delete](/cli/azure/group) を使用して、リソース グループとそのすべてのリソースを削除します。 `--no-wait` パラメーターは、操作の完了を待たずにプロンプトに制御を戻します。 `--yes` パラメーターは、追加のプロンプトを表示せずにリソースの削除を確定します。

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure CLI を使用してスケール セットのディスクを作成して使用する方法について学習しました。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続および準備

次のチュートリアルに進み、スケール セットの VM インスタンスにカスタム イメージを使用する方法を学習してください。

> [!div class="nextstepaction"]
> [スケール セットの VM インスタンスにカスタム イメージを使用する](tutorial-use-custom-image-cli.md)