---
title: Azure Portal でトラブルシューティング用 Linux VM を使用する | Microsoft Docs
description: Azure Portal で OS ディスクを復旧 VM に接続して、Linux 仮想マシンの問題のトラブルシューティングを行う方法について説明します。
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: 87b4d761eb7bd1c4a16998e44e8160cda24a05b4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088243"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure Portal で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う
Linux 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合、仮想ハード ディスク自体でトラブルシューティングの手順を実行することが必要な場合があります。 一般的な例として、`/etc/fstab` 内の無効なエントリによって VM の正常な起動が妨げられている場合が挙げられます。 この記事では、Azure Portal で仮想ハード ディスクを別の Linux VM に接続してエラーを修正し、元の VM を再作成する方法について詳しく説明します。

## <a name="recovery-process-overview"></a>回復プロセスの概要
トラブルシューティングのプロセスは次のとおりです。

1. 影響を受けている VM を停止します。
1. VM の OS ディスクのスナップショットを作成します。
1. スナップショットから仮想ハード ディスクを作成します。
1. トラブルシューティングのために、仮想ハード ディスクを別の Windows VM に接続してマウントします。
1. トラブルシューティング用 VM に接続します。 元の仮想ハード ディスクで、ファイルを編集するか、任意のツールを実行して問題を解決します。
1. 仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
1. VM の OS ディスクをスワップします。

> [!NOTE]
> この記事は、アンマネージド ディスクを使用する VM には適用されません。

## <a name="determine-boot-issues"></a>起動の問題を特定する
ブート診断と VM のスクリーンショットを調べて、VM が正常に起動できない理由を特定します。 一般的な例として、`/etc/fstab` に無効なエントリがある場合や、基になる仮想ハード ディスクが削除または移動されている場合が挙げられます。

ポータルで VM を選択し、下へスクロールして **[サポート + トラブルシューティング]** セクションを表示します。 **[ブート診断]** をクリックして、VM からストリーミングされたコンソール メッセージを表示します。 コンソール ログを調べて、VM で問題が発生している理由を特定できるかどうかを確認します。 次の例は、手動操作を必要とするメンテナンス モードの VM スタックを示しています。

![VM のブート診断のコンソール ログの表示](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

ブート診断ログの上にある **[スクリーンショット]** をクリックして、VM のスクリーンショットのキャプチャをダウンロードすることもできます。

## <a name="take-a-snapshot-of-the-os-disk"></a>OS ディスクのスナップショットを作成する
スナップショットは、仮想ハード ドライブ (VHD) の完全な読み取り専用コピーです。 進行中のプロセスをすべてクリアするために、スナップショットを取得する前に VM をクリーン シャットダウンすることをお勧めします。 OS ディスクのスナップショットを作成するには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com)に移動します。 サイドバーから **[仮想マシン]** を選択してから、問題が発生している VM を選択します。
1. 左側のウィンドウで **[ディスク]** を選択してから、OS ディスクの名前を選択します。
    ![OS ディスクの名前に関する画像](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. OS ディスクの **[概要]** ページで、 **[スナップショットの作成]** を選択します。
1. OS ディスクと同じ場所にスナップショットを作成します。

## <a name="create-a-disk-from-the-snapshot"></a>スナップショットからディスクを作成する
スナップショットからディスクを作成するには、次の手順を実行します。

1. Azure portal から **Cloud Shell** を選択します。

    ![Cloud Shell を開く場合の画像](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. 次の PowerShell コマンドを実行して、スナップショットからマネージド ディスクを作成します。 これらのサンプル名を適切な名前に置き換えてください。

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. コマンドが正常に実行された場合は、指定したリソース グループ内に新しいディスクが表示されます。

## <a name="attach-disk-to-another-vm"></a>ディスクを別の VM にアタッチする
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクをトラブルシューティング用 VM に接続した後、そのディスクの内容を参照して編集することができます。 このプロセスにより、すべての構成エラーを修正したり、追加のアプリケーションまたはシステム ログ ファイルを確認したりできるようになります。 ディスクを別の VM に接続するには、次の手順を実行します。

1. ポータルでリソース グループを選択し、トラブルシューティング用 VM を選択します。 **[ディスク]** を選択し、 **[編集]** を選択して、 **[Add data disk]\(データ ディスクの追加\)** をクリックします。

    ![ポータルで既存のディスクを接続する](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. **[データディスク]** の一覧で、特定した VM の OS ディスクを選択します。 OS ディスクが表示されない場合は、トラブルシューティング用の VM と OS ディスクが同じリージョン (場所) にあることを確認します。 
3. **[保存]** をクリックして変更を適用します。

## <a name="mount-the-attached-data-disk"></a>接続されたデータ ディスクをマウントする

> [!NOTE]
> 以下の例では、Ubuntu VM で必要な手順の詳細を示しています。 別の Linux ディストリビューション (Red Hat Enterprise Linux や SUSE など) を使用している場合、ログ ファイルの場所と `mount` コマンドが少し異なることがあります。 ご使用のディストリビューションのドキュメントを参照して、コマンドを適切に変更してください。

1. 適切な資格情報を使用して、トラブルシューティング用 VM に SSH 接続します。 このディスクがトラブルシューティング用 VM に接続された最初のデータ ディスクの場合、`/dev/sdc` に接続される可能性があります。 `dmseg` を使用して、接続されているディスクのリストを表示します。

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
エラーが解決したら、トラブルシューティング用 VM から既存の仮想ハード ディスクを切断します。 仮想ハード ディスクをトラブルシューティング用 VM に接続しているリースを解放するまで、仮想ハード ディスクを他の VM で使用することはできません。

1. トラブルシューティング用 VM の SSH セッションから、既存の仮想ハード ディスクのマウントを解除します。 まず、マウント ポイントの親ディレクトリを変更します。

    ```bash
    cd /
    ```

    次に、既存の仮想ハード ディスクのマウントを解除します。 次の例では、`/dev/sdc1` にあるデバイスのマウントを解除します。

    ```bash
    sudo umount /dev/sdc1
    ```

2. 仮想ハード ディスクを VM から切断します。 ポータルで VM を選択し、 **[ディスク]** をクリックします。 既存の仮想ハード ディスクを選択し、 **[切断]** をクリックします。

    ![既存の仮想ハード ディスクを切断する](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    VM からデータ ディスクが正常に切断されるまで待ってから、次に進みます。

## <a name="swap-the-os-disk-for-the-vm"></a>VM の OS ディスクをスワップする

Azure portal では、VM の OS ディスクの変更がサポートされるようになりました。 そのためには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)に移動します。 サイドバーから **[仮想マシン]** を選択してから、問題が発生している VM を選択します。
1. 左側のウィンドウで **[ディスク]** を選択してから、 **[OS ディスクのスワップ]** を選択します。
        ![Azure portal での [OS ディスクのスワップ] に関する画像](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. 修復した新しいディスクを選択してから、VM の名前を入力して変更を確認します。 そのディスクが一覧に表示されない場合は、トラブルシューティング用 VM からディスクを切断した後 10 から 15 分待ちます。 また、ディスクが VM と同じ場所にあることも確認してください。
1. [OK] を選択します。

## <a name="next-steps"></a>次の手順
VM への接続の問題が発生した場合は、[Azure VM への SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Linux VM でのアプリケーションの接続の問題のトラブルシューティング](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。
