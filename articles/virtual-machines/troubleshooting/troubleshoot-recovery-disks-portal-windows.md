---
title: Azure Portal でトラブルシューティング用 Windows VM を使用する | Microsoft Docs
description: Azure Portal で OS ディスクを復旧 VM に接続して、Azure の Windows 仮想マシンの問題のトラブルシューティングを行う方法について説明します
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 18bd531e122ed72aa1cc481d6cf76590412c73c6
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71088297"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure Portal で OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う
Azure の Windows 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合、仮想ハード ディスク自体でトラブルシューティングの手順を実行することが必要な場合があります。 一般的な例として、VM の正常な起動を妨げる失敗したアプリケーション更新が挙げられます。 この記事では、Azure Portal で仮想ハード ディスクを別の Windows VM に接続してエラーを修正し、元の VM を再作成する方法について詳しく説明します。 

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

## <a name="take-a-snapshot-of-the-os-disk"></a>OS ディスクのスナップショットを取得する
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
    
    #Provide the storage type for Managed Disk.  Premium_LRS or Standard_LRS.
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

## <a name="attach-the-disk-to-another-vm"></a>ディスクを別の VM に接続する
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクをトラブルシューティング用 VM に接続した後、そのディスクの内容を参照して編集することができます。 このプロセスにより、すべての構成エラーを修正したり、追加のアプリケーションまたはシステム ログ ファイルを確認したりできるようになります。 ディスクを別の VM に接続するには、次の手順を実行します。

1. ポータルでリソース グループを選択し、トラブルシューティング用 VM を選択します。 **[ディスク]** を選択し、 **[編集]** を選択して、 **[Add data disk]\(データ ディスクの追加\)** をクリックします。

    ![ポータルで既存のディスクを接続する](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. **[データディスク]** の一覧で、特定した VM の OS ディスクを選択します。 OS ディスクが表示されない場合は、トラブルシューティング用の VM と OS ディスクが同じリージョン (場所) にあることを確認します。 
3. **[保存]** をクリックして変更を適用します。

## <a name="mount-the-attached-data-disk-to-the-vm"></a>接続されたデータ ディスクを VM にマウントする

1. トラブルシューティング用 VM へのリモート デスクトップ接続を開きます。 
2. トラブルシューティング用 VM で、 **[サーバー マネージャー]** を開き、 **[ファイル サービスと記憶域サービス]** を選択します。 

    ![[サーバー マネージャー] で [ファイル サービスと記憶域サービス] を選択](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. データ ディスクが自動的に検出され、接続されます。 接続されているディスクの一覧を表示するには、 **[ディスク]** を選択します。 データ ディスクを選び、ドライブ文字を含むボリューム情報を表示できます。 次の例は、接続されて **F:** を使っているデータ ディスクを示しています。

    ![[サーバー マネージャー] 内の接続されているディスクとボリュームの情報](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-issues-on-original-virtual-hard-disk"></a>元の仮想ハード ディスクで問題を修正する
既存の仮想ハード ディスクをマウントすることで、必要に応じてメンテナンスやトラブルシューティングの手順を実行できるようになります。 問題に対処したら、次の手順に進みます。

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>元の仮想ハード ディスクのマウントを解除して切断する
エラーが解決したら、トラブルシューティング用 VM から既存の仮想ハード ディスクを切断します。 仮想ハード ディスクをトラブルシューティング用 VM に接続しているリースを解放するまで、仮想ハード ディスクを他の VM で使用することはできません。

1. VM への RDP セッションから、 **[サーバー マネージャー]** を開き、 **[ファイル サービスと記憶域サービス]** を選択します。

    ![[サーバー マネージャー] で [ファイル サービスと記憶域サービス] を選択](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. **[ディスク]** を選択してから、データ ディスクを選びます。 データ ディスクを右クリックし、 **[オフラインにする]** を選択します。

    ![データ ディスクをサーバー マネージャーでオフラインに設定](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 仮想ハード ディスクを VM から切断します。 Azure Portal で VM を選択し、 **[ディスク]** をクリックします。 
4. **[編集]** を選択し、アタッチした OS ディスクを選択して、 **[デタッチ]** をクリックします。

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
VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](troubleshoot-rdp-connection.md)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。

Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」をご覧ください。


