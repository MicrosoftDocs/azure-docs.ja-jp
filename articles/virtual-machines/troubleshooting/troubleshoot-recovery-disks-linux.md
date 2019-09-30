---
title: Azure CLI でトラブルシューティング用 Linux VM を使用する | Microsoft Docs
description: Azure CLI で OS ディスクを復旧 VM に接続して、Linux VM の問題のトラブルシューティングを行う方法について説明します。
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: faa15e9cf6288bcd4014cbc03dcf9d82a2047bde
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088367"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Azure CLI で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う
Linux 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合、仮想ハード ディスク自体でトラブルシューティングの手順を実行することが必要な場合があります。 一般的な例として、`/etc/fstab` 内の無効なエントリによって VM の正常な起動が妨げられている場合が挙げられます。 この記事では、Azure CLI で仮想ハード ディスクを別の Linux VM に接続してエラーを修正し、元の VM を再作成する方法について詳しく説明します。 

## <a name="recovery-process-overview"></a>回復プロセスの概要
トラブルシューティングのプロセスは次のとおりです。

1. 影響を受けている VM を停止します。
1. VM の OS ディスクのスナップショットを作成します。
1. OS ディスクのスナップショットからディスクを作成します。
1. トラブルシューティングのために、新しい OS ディスクを別の Linux VM に接続してマウントします。
1. トラブルシューティング用 VM に接続します。 ファイルを編集するか任意のツールを実行して、新しい OS ディスクの問題を解決します。
1. 新しい OS ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
1. 影響を受けている VM の OS ディスクを変更します。

上記のトラブルシューティングの手順を行うには、[Azure CLI](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしておく必要があります。

> [!Important]
> この記事のスクリプトは、[マネージド ディスク](../linux/managed-disks-overview.md)を使用している VM にのみ適用されます。 

以下の例では、パラメーター名を `myResourceGroup` や `myVM` などの独自の値に置き換えてください。

## <a name="determine-boot-issues"></a>起動の問題を特定する
シリアル出力を調べて、VM が正常に起動できない理由を特定します。 一般的な例として、`/etc/fstab` に無効なエントリがある場合や、基になる仮想ハード ディスクが削除または移動されている場合が挙げられます。

[az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics) を使用して、ブート ログを取得します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM からシリアル出力を取得します。

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

シリアル出力を調べて、VM が起動できない理由を特定します。 シリアル出力に何も示されていない場合は、仮想ハード ディスクをトラブルシューティング用 VM に接続した後に、`/var/log` にあるログ ファイルを確認することが必要な場合があります。

## <a name="stop-the-vm"></a>VM を停止する

次の例では、`myResourceGroup` という名前のリソース グループから `myVM` という名前の VM を停止します。

```azurecli
az vm stop --resource-group MyResourceGroup --name MyVm
```
## <a name="take-a-snapshot-from-the-os-disk-of-the-affected-vm"></a>影響を受けている VM の OS ディスクのスナップショットを作成する

スナップショットは、VHD の完全な読み取り専用コピーです。 これを VM にアタッチすることはできません。 次の手順で、このスナップショットからディスクを作成します。 次の例は、'myVM' という名前の VM の OS ディスクから `mySnapshot` という 名前のスナップショットを作成します。 

```azurecli
#Get the OS disk Id 
$osdiskid=(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)

#creates a snapshot of the disk
az snapshot create --resource-group myResourceGroupDisk --source "$osdiskid" --name mySnapshot
```
## <a name="create-a-disk-from-the-snapshot"></a>スナップショットからディスクを作成する

このスクリプトは、`mySnapshot` という名前のスナップショットから `myOSDisk` という名前のマネージド ディスクを作成します。  

```azurecli
#Provide the name of your resource group
$resourceGroup=myResourceGroup

#Provide the name of the snapshot that will be used to create Managed Disks
$snapshot=mySnapshot

#Provide the name of the Managed Disk
$osDisk=myNewOSDisk

#Provide the size of the disks in GB. It should be greater than the VHD file size.
$diskSize=128

#Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
$storageType=Premium_LRS

#Provide the OS type
$osType=linux

#Provide the name of the virtual machine
$virtualMachine=myVM

#Get the snapshot Id 
$snapshotId=(az snapshot show --name $snapshot --resource-group $resourceGroup --query [id] -o tsv)

# Create a new Managed Disks using the snapshot Id.

az disk create --resource-group $resourceGroup --name $osDisk --sku $storageType --size-gb $diskSize --source $snapshotId

```

リソース グループとソース スナップショットが同じリージョンにない場合、`az disk create` を実行すると "リソースが見つかりません" というエラーが表示されます。 この場合は、`--location <region>` を指定して、ソース スナップショットと同じリージョンにディスクを作成する必要があります。

これで、元の OS ディスクのコピーが用意できました。 トラブルシューティングのために、この新しいディスクを別の Windows VM にマウントできます。

## <a name="attach-the-new-virtual-hard-disk-to-another-vm"></a>新しい仮想ハード ディスクを別の VM に接続する
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクの内容を参照して編集するために、ディスクをこのトラブルシューティング用 VM に接続します。 このプロセスにより、すべての構成エラーを修正したり、追加のアプリケーションまたはシステム ログ ファイルを確認したりできるようになります。

このスクリプトでは、ディスク `myNewOSDisk` を VM `MyTroubleshootVM` にアタッチしています。

```azurecli
# Get ID of the OS disk that you just created.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Attach the disk to the troubleshooting VM
az vm disk attach --disk $diskId --resource-group MyResourceGroup --size-gb 128 --sku Standard_LRS --vm-name MyTroubleshootVM
```
## <a name="mount-the-attached-data-disk"></a>接続されたデータ ディスクをマウントする

> [!NOTE]
> 以下の例では、Ubuntu VM で必要な手順の詳細を示しています。 別の Linux ディストリビューション (Red Hat Enterprise Linux や SUSE など) を使用している場合、ログ ファイルの場所と `mount` コマンドが少し異なることがあります。 ご使用のディストリビューションのドキュメントを参照して、コマンドを適切に変更してください。

1. 適切な資格情報を使用して、トラブルシューティング用 VM に SSH 接続します。 このディスクがトラブルシューティング用 VM に接続された最初のデータ ディスクの場合、ディスクは `/dev/sdc` に接続される可能性があります。 `dmseg` を使用して、接続されているディスクを表示します。

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

    前の例では、OS ディスクは `/dev/sda` にあり、各 VM 用に提供される一時ディスクは `/dev/sdb` にあります。 複数のデータ ディスクがある場合、それらのディスクの場所は、`/dev/sdd`、`/dev/sde` (以降も同様) になります。

2. 既存の仮想ハード ディスクをマウントするディレクトリを作成します。 次の例では、`troubleshootingdisk` という名前のディレクトリを作成します。

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. 既存の仮想ハード ディスクに複数のパーティションがある場合は、必要なパーティションをマウントします。 次の例では、`/dev/sdc1` にある最初のプライマリ パーティションをマウントします。

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > 仮想ハード ディスクの汎用一意識別子 (UUID) を使用して、Azure の VM にデータ ディスクをマウントするのがベスト プラクティスです。 この短いトラブルシューティング シナリオでは、UUID を使用して仮想ハード ディスクをマウントする必要はありません。 ただし、通常の使用時に、`/etc/fstab` を編集し、UUID ではなくデバイス名を使用して仮想ハード ディスクをマウントすると、VM の起動に失敗する場合があります。


## <a name="fix-issues-on-the-new-os-disk"></a>新しい OS ディスクの問題を修正する
既存の仮想ハード ディスクをマウントすることで、必要に応じてメンテナンスやトラブルシューティングの手順を実行できるようになります。 問題に対処したら、次の手順に進みます。


## <a name="unmount-and-detach-the-new-os-disk"></a>新しい OS ディスクのマウントを解除して切断する
エラーが解決したら、既存の仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。 仮想ハード ディスクをトラブルシューティング用 VM に接続しているリースを解放するまで、仮想ハード ディスクを他の VM で使用することはできません。

1. トラブルシューティング用 VM の SSH セッションから、既存の仮想ハード ディスクのマウントを解除します。 まず、マウント ポイントの親ディレクトリを変更します。

    ```bash
    cd /
    ```

    次に、既存の仮想ハード ディスクのマウントを解除します。 次の例では、`/dev/sdc1` にあるデバイスのマウントを解除します。

    ```bash
    sudo umount /dev/sdc1
    ```

2. 仮想ハード ディスクを VM から切断します。 トラブルシューティング用 VM の SSH セッションを終了します。

    ```azurecli
    az vm disk detach -g MyResourceGroup --vm-name MyTroubleShootVm --name myNewOSDisk
    ```

## <a name="change-the-os-disk-for-the-affected-vm"></a>影響を受けている VM の OS ディスクを変更する

Azure CLI を使用して、OS ディスクを交換できます。 VM を削除して再作成する必要はありません。

この例では、`myVM` という名前の VM を停止し、`myNewOSDisk` という名前のディスクを新しい OS ディスクとして割り当てます。

```azurecli
# Stop the affected VM
az vm stop -n myVM -g myResourceGroup

# Get ID of the OS disk that is repaired.
$myNewOSDiskid=(az vm show -g myResourceGroupDisk -n myNewOSDisk --query "storageProfile.osDisk.managedDisk.id" -o tsv)

# Change the OS disk of the affected VM to "myNewOSDisk"
az vm update -g myResourceGroup -n myVM --os-disk $myNewOSDiskid

# Start the VM
az vm start -n myVM -g myResourceGroup
```

## <a name="next-steps"></a>次の手順
VM への接続の問題が発生した場合は、[Azure VM への SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Linux VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。

