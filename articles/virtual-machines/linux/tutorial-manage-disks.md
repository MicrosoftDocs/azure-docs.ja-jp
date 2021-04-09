---
title: チュートリアル - Azure CLI を使用した Azure ディスクの管理
description: このチュートリアルでは、Azure CLI を使用して、仮想マシン用の Azure ディスクの作成と管理を行う方法について説明します
author: cynthn
ms.service: virtual-machines
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 08/20/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.subservice: disks
ms.openlocfilehash: bbecaa32f85c42954cea6c8e533f0f658eb2dfee
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802286"
---
# <a name="tutorial---manage-azure-disks-with-the-azure-cli"></a>チュートリアル - Azure CLI を使用した Azure ディスクの管理

Azure Virtual Machines (VM) では、オペレーティング システム、アプリケーション、およびデータを格納するためにディスクを使用します。 VM を作成するときは、予測されるワークロードに適したディスクのサイズと構成を選択する必要があります。 このチュートリアルでは、VM ディスクのデプロイと管理方法を示します。 内容は次のとおりです。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備
> * ディスクのスナップショット


## <a name="default-azure-disks"></a>既定の Azure ディスク

Azure 仮想マシンを作成すると、2 つのディスクが仮想マシンに自動的に接続されます。

**オペレーティング システム ディスク** - オペレーティング システム ディスクは、最大 2 TB までサイズを変更でき、VM のオペレーティング システムをホストします。 OS ディスクには既定で */dev/sda* というラベルが付けられています。 OS ディスクのディスク キャッシュ構成は、OS パフォーマンスの向上のために最適化されています。 この構成のため、アプリケーションまたはデータ用に OS ディスクを **使用しないでください**。 アプリケーションとデータ用には、このチュートリアルの後半で説明するデータ ディスクを使用してください。

**一時ディスク** - 一時ディスクは、VM と同じ Azure ホストに配置されているソリッド ステート ドライブを使用します。 一時ディスクは、パフォーマンスが高く、一時的なデータ処理などの操作に使用される場合があります。 ただし、VM を新しいホストに移動すると、一時ディスクに格納されているデータは削除されます。 一時ディスクのサイズは VM のサイズによって決まります。 一時ディスクには */dev/sdb* のラベルが付けられており、 */mnt* というマウント ポイントがあります。

## <a name="azure-data-disks"></a>Azure データ ディスク

アプリケーションのインストールとデータの格納を行うために、データ ディスクを追加できます。 耐久性と応答性の高いデータ ストレージが望ましい状況では、必ず、データ ディスクを使用する必要があります。 仮想マシンのサイズによって、VM に接続できるデータ ディスクの数が決まります。

## <a name="vm-disk-types"></a>VM ディスクの種類

Azure では、2 種類のディスクを提供しています。

**Standard ディスク** - HDD が使用されており、高パフォーマンスでありながらコスト効率にも優れたストレージを提供します。 Standard ディスクは、コスト効率が重視される、開発およびテストのワークロードに最適です。

**Premium ディスク** - SSD ベースの高性能で待ち時間の短いディスクが使用されています。 実稼働ワークロードを実行する VM に最適です。 [サイズ名](../vm-naming-conventions.md)に **S** を含む VM サイズでは、通常、Premium Storage がサポートされています。 たとえば、DS シリーズ、DSv2 シリーズ、GS シリーズ、FS シリーズの VM は、Premium Storage をサポートしています。 ディスク サイズを選択するときは、値を切り上げて 1 つ上の種類にします。 たとえば、ディスク サイズが 64 GB より大きく、128 GB 未満の場合、ディスクの種類は P10 です。 

<br>


[!INCLUDE [disk-storage-premium-ssd-sizes](../../../includes/disk-storage-premium-ssd-sizes.md)]

Premium Storage ディスクをプロビジョニングすると、Standard Storage とは異なり、対象のディスクの容量、IOPS、スループットが保証されます。 たとえば、P50 ディスクを作成した場合、対象のディスクに 4,095 GB のストレージ容量、7,500 IOPS、および 250 MB/秒のスループットがプロビジョニングされます。 アプリケーションでは、容量とパフォーマンスのすべてまたは一部を使用できます。 Premium SSD ディスクは、1 桁のミリ秒の低遅延と、前出の表に示した目標 IOPS とスループットを 99.9% の時間で提供するように設計されています。

上記の表は、ディスクあたりの最大 IOPS を割り出していますが、複数のデータ ディスクをストライピングすることによって、より高いレベルのパフォーマンスを実現できます。 たとえば、64 のデータ ディスクを Standard_GS5 VM に接続することができます。 これらの各ディスクのサイズが P30 である場合、最大 80,000 IOPS を実現できます。 VM あたりの最大 IOPS について詳しくは、「[VM の種類とサイズ](../sizes.md)」をご覧ください。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

## <a name="create-and-attach-disks"></a>ディスクを作成して接続する

データ ディスクの作成および接続は、VM の作成時や、既存の VM に対して実行することができます。

### <a name="attach-disk-at-vm-creation"></a>VM の作成時にディスクを接続する

[az group create](/cli/azure/group#az-group-create) コマンドを使用して、リソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroupDisk --location eastus
```

[az vm create](/cli/azure/vm#az-vm-create) コマンドを使用して VM を作成します。 次の例では、*myVM* という名前の VM を作成し、*azureuser* という名前のユーザー アカウントを追加します。さらに、まだ SSH キーが存在しない場合は SSH キーを生成します。 `--datadisk-sizes-gb` 引数は、追加のディスクを作成してこの仮想マシンに接続するように指定するために使用します。 複数のディスクを作成して接続するには、ディスク サイズ値をスペースで区切ったリストを使用します。 次の例では、どちらも 128 GB のデータ ディスクを 2 つ備えた VM が作成されます。 ディスク サイズが 128 GB であるため、両方のディスクが P10 として構成され、ディスクあたり最大 500 IOPS を実現します。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupDisk \
  --name myVM \
  --image UbuntuLTS \
  --size Standard_DS2_v2 \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 128 128
```

### <a name="attach-disk-to-existing-vm"></a>既存の VM にディスクを接続する

新しいディスクを作成して既存の仮想マシンに接続するには、[az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) コマンドを使用します。 次の例では、サイズが 128 ギガバイトの Premium ディスクが作成され、最後の手順で作成した VM に接続されます。

```azurecli-interactive
az vm disk attach \
    --resource-group myResourceGroupDisk \
    --vm-name myVM \
    --name myDataDisk \
    --size-gb 128 \
    --sku Premium_LRS \
    --new
```

## <a name="prepare-data-disks"></a>データ ディスクを準備する

ディスクが仮想マシンに接続されたら、そのディスクを使用するようにオペレーティング システムを構成する必要があります。 次の例は、ディスクを手動で構成する方法を示しています。 この処理は、cloud-init を使用して自動化することもできます。詳細については、[後のチュートリアル](./tutorial-automate-vm-deployment.md)で説明します。


仮想マシンとの SSH 接続を作成します。 この例の IP アドレスは、仮想マシンのパブリック IP で置き換えてください。

```console
ssh 10.101.10.10
```

`parted` を使用してディスクをパーティション分割します。

```bash
sudo parted /dev/sdc --script mklabel gpt mkpart xfspart xfs 0% 100%
```

`mkfs` コマンドを使用してパーティションにファイル システムを書き込みます。 `partprobe` を使用して、変更を OS に認識させます。

```bash
sudo mkfs.xfs /dev/sdc1
sudo partprobe /dev/sdc1
```

オペレーティング システムでアクセスできるように、新しいディスクをマウントします。

```bash
sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
```

これで `/datadrive` マウント ポイントを通じてディスクにアクセスできるようになりました。これは `df -h` コマンドを実行して確認できます。

```bash
df -h | grep -i "sd"
```

出力には、`/datadrive` に新しいドライブがマウントされていることが示されます。

```bash
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        29G  2.0G   27G   7% /
/dev/sda15      105M  3.6M  101M   4% /boot/efi
/dev/sdb1        14G   41M   13G   1% /mnt
/dev/sdc1        50G   52M   47G   1% /datadrive
```

再起動後にドライブが確実に再マウントされるように、そのドライブを */etc/fstab* ファイルに追加する必要があります。 これを行うには、`blkid` ユーティリティを使用してディスクの UUID を取得します。

```bash
sudo -i blkid
```

出力には、ドライブ (この例では `/dev/sdc1`) の UUID が表示されます。

```bash
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="xfs"
```

> [!NOTE]
> **/etc/fstab** ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。

テキスト エディターで次のように `/etc/fstab` ファイルを開きます。

```bash
sudo nano /etc/fstab
```

次のような行を */etc/fstab* ファイルに追加します。 UUID 値は実際のものに置き換えてください。

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive  xfs    defaults,nofail   1  2
```

ファイルの編集が完了したら、`Ctrl+O` を使用してファイルを書き込み、 `Ctrl+X` を使用してエディターを終了します。

ディスクが構成されたので、SSH セッションを閉じます。

```bash
exit
```

## <a name="take-a-disk-snapshot"></a>ディスクのスナップショットを作成する

ディスクのスナップショットを作成すると、特定の時点のディスクに対する読み取り専用のコピーが作成されます。 Azure VM のスナップショットは、構成に変更を加える前に、VM の状態を簡単に保存するときに役立ちます。 問題やエラーが発生した場合は、スナップショットを使用して VM を復元できます。 VM に複数のディスクがある場合は、各ディスクのスナップショットが個別に作成されます。 アプリケーション整合性のあるバックアップを取得するには、ディスクのスナップショットを作成する前に、VM を停止することを検討してください。 または、[Azure Backup サービス](../../backup/index.yml)を使用して、VM の実行中に自動的にバックアップを実行できます。

### <a name="create-snapshot"></a>スナップショットの作成

スナップショットを作成する前に、ディスクの ID または名前が必要です。 [az vm show](/cli/azure/vm#az-vm-show) を使用して、ディスク ID を取得します。 この例では、ディスク ID を変数に格納して、後の手順で使用できるようにしています。

```azurecli-interactive
osdiskid=$(az vm show \
   -g myResourceGroupDisk \
   -n myVM \
   --query "storageProfile.osDisk.managedDisk.id" \
   -o tsv)
```

ID を取得したので、 [az snapshot create](/cli/azure/snapshot#az-snapshot-create) を使用してディスクのスナップショットを作成します。

```azurecli-interactive
az snapshot create \
    --resource-group myResourceGroupDisk \
    --source "$osdiskid" \
    --name osDisk-backup
```

### <a name="create-disk-from-snapshot"></a>スナップショットからのディスクの作成

その後、[az disk create](/cli/azure/disk#az-disk-create) を使用してこのスナップショットをディスクに変換できます。これを使用して、仮想マシンを再作成することができます。

```azurecli-interactive
az disk create \
   --resource-group myResourceGroupDisk \
   --name mySnapshotDisk \
   --source osDisk-backup
```

### <a name="restore-virtual-machine-from-snapshot"></a>スナップショットからの仮想マシンの復元

実際に仮想マシンの復旧を試すために、[az vm delete](/cli/azure/vm#az-vm-delete) を使用して既存の仮想マシンを削除します。

```azurecli-interactive
az vm delete \
--resource-group myResourceGroupDisk \
--name myVM
```

スナップショット ディスクから新しい仮想マシンを作成します。

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupDisk \
    --name myVM \
    --attach-os-disk mySnapshotDisk \
    --os-type linux
```

### <a name="reattach-data-disk"></a>データ ディスクの再接続

すべてのデータ ディスクを仮想マシンに再度接続する必要があります。

[az disk list](/cli/azure/disk#az-disk-list) コマンドを使用して、データ ディスク名を見つけます。 この例では、このディスク名を `datadisk` という変数に格納しています。次の手順でこの変数を使用します。

```azurecli-interactive
datadisk=$(az disk list \
   -g myResourceGroupDisk \
   --query "[?contains(name,'myVM')].[id]" \
   -o tsv)
```

ディスクを接続するには、[az vm disk attach](/cli/azure/vm/disk#az-vm-disk-attach) コマンドを使用します。

```azurecli-interactive
az vm disk attach \
   –g myResourceGroupDisk \
   --vm-name myVM \
   --name $datadisk
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、VM ディスクについて、次のようなトピックを学習しました。

> [!div class="checklist"]
> * OS ディスクと一時ディスク
> * データ ディスク
> * Standard ディスクと Premium ディスク
> * ディスクのパフォーマンス
> * データ ディスクの接続と準備
> * ディスクのスナップショット

次のチュートリアルに進み、VM 構成を自動化する方法について学習してください。

> [!div class="nextstepaction"]
> [VM 構成の自動化](./tutorial-automate-vm-deployment.md)
