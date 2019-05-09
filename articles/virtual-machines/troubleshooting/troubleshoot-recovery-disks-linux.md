---
title: Azure CLI でトラブルシューティング用 Linux VM を使用する | Microsoft Docs
description: Azure CLI で OS ディスクを復旧 VM に接続して、Linux VM の問題のトラブルシューティングを行う方法について説明します。
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: dfb85b0f9f1dda611c613cb296177cf28391adc0
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776888"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Azure CLI で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う
Linux 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合、仮想ハード ディスク自体でトラブルシューティングの手順を実行することが必要な場合があります。 一般的な例として、`/etc/fstab` 内の無効なエントリによって VM の正常な起動が妨げられている場合が挙げられます。 この記事では、Azure CLI で仮想ハード ディスクを別の Linux VM に接続してエラーを修正し、元の VM を再作成する方法について詳しく説明します。 


## <a name="recovery-process-overview"></a>回復プロセスの概要
トラブルシューティングのプロセスは次のとおりです。

1. 仮想ハード ディスクを保持して、問題が発生している VM を削除します。
2. トラブルシューティングのために、仮想ハード ディスクを別の Linux VM に接続してマウントします。
3. トラブルシューティング用 VM に接続します。 元の仮想ハード ディスクで、ファイルを編集するか、任意のツールを実行して問題を解決します。
4. 仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
5. 元の仮想ハード ディスクを使用して VM を作成します。

マネージド ディスクを使用する VM の場合は、「[新しい OS ディスクを接続することでマネージド ディスク VM のトラブルシューティングを行う](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk)」をご覧ください。

上記のトラブルシューティングの手順を行うには、[Azure CLI](/cli/azure/install-az-cli2) の最新版をインストールし、[az login](/cli/azure/reference-index) を使用して Azure アカウントにログインしておく必要があります。

以下の例では、パラメーター名を独自の値に置き換えてください。 `myResourceGroup`、`mystorageaccount`、`myVM` などは、例として使われているパラメーター名です。


## <a name="determine-boot-issues"></a>起動の問題を特定する
シリアル出力を調べて、VM が正常に起動できない理由を特定します。 一般的な例として、`/etc/fstab` に無効なエントリがある場合や、基になる仮想ハード ディスクが削除または移動されている場合が挙げられます。

[az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics) を使用して、ブート ログを取得します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVM` という名前の VM からシリアル出力を取得します。

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

シリアル出力を調べて、VM が起動できない理由を特定します。 シリアル出力に何も示されていない場合は、仮想ハード ディスクをトラブルシューティング用 VM に接続した後に、`/var/log` にあるログ ファイルを確認することが必要な場合があります。


## <a name="view-existing-virtual-hard-disk-details"></a>既存の仮想ハード ディスクの詳細を表示する
仮想ハード ディスク (VHD) を別の VM に接続するには、OS ディスクの URI を特定しておく必要があります。 

[az vm show](/cli/azure/vm) を使用して、VM に関する情報を表示します。 OS ディスクの URI を抽出するには、`--query` フラグを使用します。 次の例では、`myResourceGroup` という名前のリソース グループにある `myVM` という名前の VM のディスク情報を取得します。

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

URI は **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** のようになります。

## <a name="delete-existing-vm"></a>既存の VM を削除する
Azure では、仮想ハード ディスクと VM は 2 つの異なるリソースです。 仮想ハード ディスクには、オペレーティング システム自体、アプリケーション、構成が格納されています。 VM 自体は、サイズや場所を定義し、仮想ハード ディスクや仮想ネットワーク インターフェイス カード (NIC) などのリソースを参照するメタデータにすぎません。 各仮想ハード ディスクには、VM に接続されたときにリースが割り当てられています。 データ ディスクは、VM の実行中でも接続および切断できますが、OS ディスクは、VM リソースを削除しない限り、切断することはできません。 VM が停止され、割り当てが解除された状態であっても、リースによって OS ディスクは引き続きその VM に関連付けられています。

VM を回復するには、まず VM リソース自体を削除します。 VM を削除しても、仮想ハード ディスクはストレージ アカウントに残されます。 VM を削除したら、仮想ハード ディスクを別の VM に接続してトラブルシューティングを行い、エラーを解決します。

[az vm delete](/cli/azure/vm) を使用して VM を削除します。 次の例では、`myResourceGroup` という名前のリソース グループから `myVM` という名前の VM を削除します。

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

VM の削除が完了するまで待ってから、仮想ハード ディスクを別の VM に接続します。 仮想ハード ディスクを別の VM に接続するには、仮想ハード ディスクを VM に関連付けているリースを解放しておく必要があります。


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>既存の仮想ハード ディスクを別の VM に接続する
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクの内容を参照して編集するために、既存の仮想ハード ディスクをこのトラブルシューティング用 VM に接続します。 このプロセスにより、構成エラーの修正や、その他のアプリケーション ログ ファイルまたはシステム ログ ファイルの確認などが可能になります。 トラブルシューティングに使用する別の VM を選択または作成します。

[az vm unmanaged-disk attach](/cli/azure/vm/unmanaged-disk) を使用して、既存の仮想ハード ディスクを接続します。 既存の仮想ハード ディスクを接続する場合は、前述の `az vm show` コマンドで取得したディスクの URI を指定します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVMRecovery` という名前のトラブルシューティング用 VM に既存の仮想ハード ディスクを接続します。

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
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


## <a name="fix-issues-on-original-virtual-hard-disk"></a>元の仮想ハード ディスクで問題を修正する
既存の仮想ハード ディスクをマウントすることで、必要に応じてメンテナンスやトラブルシューティングの手順を実行できるようになります。 問題に対処したら、次の手順に進みます。


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>元の仮想ハード ディスクのマウントを解除して切断する
エラーが解決したら、既存の仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。 仮想ハード ディスクをトラブルシューティング用 VM に接続しているリースを解放するまで、仮想ハード ディスクを他の VM で使用することはできません。

1. トラブルシューティング用 VM の SSH セッションから、既存の仮想ハード ディスクのマウントを解除します。 まず、マウント ポイントの親ディレクトリを変更します。

    ```bash
    cd /
    ```

    次に、既存の仮想ハード ディスクのマウントを解除します。 次の例では、`/dev/sdc1` にあるデバイスのマウントを解除します。

    ```bash
    sudo umount /dev/sdc1
    ```

2. 仮想ハード ディスクを VM から切断します。 トラブルシューティング用 VM の SSH セッションを終了します。 [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk) を使用して、トラブルシューティング用 VM に接続されているデータ ディスクの一覧を表示します。 次の例では、`myResourceGroup` という名前のリソース グループの `myVMRecovery` という名前の VM に接続されているデータ ディスクを表示します。

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    既存の仮想ハード ディスクの名前を書き留めます。 たとえば、URI が **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** のディスクの名前は **myVHD** です。 

    [az vm unmanaged-disk detach](/cli/azure/vm/unmanaged-disk) を使用して、VM からデータ ディスクを切断します。 次の例では、`myResourceGroup` リソース グループ内の `myVMRecovery` という名前の VM から `myVHD` という名前のディスクを切断します。

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>元のハード ディスクから VM を作成する
元の仮想ハード ディスクから VM を作成するには、[この Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd)を使用します。 実際の JSON テンプレートは次のリンクにあります。

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

このテンプレートでは、前述のコマンドの VHD URI を使用して VM をデプロイします。 [az group deployment create](/cli/azure/group/deployment) を使用して、テンプレートをデプロイします。 元の VHD の URI を指定した後、次のように OS の種類、VM サイズ、VM 名を指定します。

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>ブート診断を再度有効にする
既存の仮想ハード ディスクから VM を作成したときに、ブート診断が自動的に有効にならない場合があります。 [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics) を使用して、ブート診断を有効にします。 次の例では、`myResourceGroup` という名前のリソース グループの `myDeployedVM` という名前の VM で診断拡張機能を有効にします。

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>新しい OS ディスクを接続することでマネージド ディスク VM のトラブルシューティングを行う
1. 影響を受けている VM を停止します。
2. マネージド ディスク VM の OS ディスクの[マネージド ディスク スナップショットを作成](../linux/snapshot-copy-managed-disk.md)します。
3. [スナップショットから新しいマネージド ディスクを作成](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md)します。
4. [マネージド ディスクを VM のデータ ディスクとして接続](../windows/attach-disk-ps.md)します。
5. [手順 4 のデータ ディスクを OS ディスクに変更](../windows/os-disk-swap.md)します。

## <a name="next-steps"></a>次の手順
VM への接続の問題が発生した場合は、[Azure VM への SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Linux VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。

