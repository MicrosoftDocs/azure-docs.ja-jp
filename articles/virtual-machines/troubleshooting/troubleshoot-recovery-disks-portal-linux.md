---
title: Azure Portal でトラブルシューティング用 Linux VM を使用する | Microsoft Docs
description: Azure Portal で OS ディスクを復旧 VM に接続して、Linux 仮想マシンの問題のトラブルシューティングを行う方法について説明します。
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2016
ms.author: genli
ms.openlocfilehash: 160e45ad5bf83f44bed2314ee5103825e265467c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67709387"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure Portal で OS ディスクを復旧 VM に接続して Linux VM のトラブルシューティングを行う
Linux 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合、仮想ハード ディスク自体でトラブルシューティングの手順を実行することが必要な場合があります。 一般的な例として、`/etc/fstab` 内の無効なエントリによって VM の正常な起動が妨げられている場合が挙げられます。 この記事では、Azure Portal で仮想ハード ディスクを別の Linux VM に接続してエラーを修正し、元の VM を再作成する方法について詳しく説明します。

## <a name="recovery-process-overview"></a>回復プロセスの概要
トラブルシューティングのプロセスは次のとおりです。

1. 仮想ハード ディスクを保持して、問題が発生している VM を削除します。
2. トラブルシューティングのために、仮想ハード ディスクを別の Linux VM に接続してマウントします。
3. トラブルシューティング用 VM に接続します。 元の仮想ハード ディスクで、ファイルを編集するか、任意のツールを実行して問題を解決します。
4. 仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
5. 元の仮想ハード ディスクを使用して VM を作成します。

マネージド ディスクを使用する VM の場合は、「[新しい OS ディスクを接続することでマネージド ディスク VM のトラブルシューティングを行う](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk)」をご覧ください。

## <a name="determine-boot-issues"></a>起動の問題を特定する
ブート診断と VM のスクリーンショットを調べて、VM が正常に起動できない理由を特定します。 一般的な例として、`/etc/fstab` に無効なエントリがある場合や、基になる仮想ハード ディスクが削除または移動されている場合が挙げられます。

ポータルで VM を選択し、下へスクロールして **[サポート + トラブルシューティング]** セクションを表示します。 **[ブート診断]** をクリックして、VM からストリーミングされたコンソール メッセージを表示します。 コンソール ログを調べて、VM で問題が発生している理由を特定できるかどうかを確認します。 次の例は、手動操作を必要とするメンテナンス モードの VM スタックを示しています。

![VM のブート診断のコンソール ログの表示](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

ブート診断ログの上にある **[スクリーンショット]** をクリックして、VM のスクリーンショットのキャプチャをダウンロードすることもできます。


## <a name="view-existing-virtual-hard-disk-details"></a>既存の仮想ハード ディスクの詳細を表示する
仮想ハード ディスクを別の VM に接続するには、仮想ハード ディスク (VHD) の名前を確認しておく必要があります。 

ポータルでリソース グループを選択し、ストレージ アカウントを選択します。 次の例のように、 **[BLOB]** をクリックします。

![ストレージ BLOB を選択する](./media/troubleshoot-recovery-disks-portal-linux/storage-account-overview.png)

通常は、仮想ハード ディスクを格納する **vhds** という名前のコンテナーがあります。 コンテナーを選択して、仮想ハード ディスクの一覧を表示します。 VHD の名前を書き留めます (通常、プレフィックスは VM の名前です)。

![ストレージ コンテナーで VHD を特定する](./media/troubleshoot-recovery-disks-portal-linux/storage-container.png)

一覧から既存の仮想ハード ディスクを選択し、以降の手順で使用できるように URL をコピーします。

![既存の仮想ハード ディスクの URL をコピーする](./media/troubleshoot-recovery-disks-portal-linux/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>既存の VM を削除する
Azure では、仮想ハード ディスクと VM は 2 つの異なるリソースです。 仮想ハード ディスクには、オペレーティング システム自体、アプリケーション、構成が格納されています。 VM 自体は、サイズや場所を定義し、仮想ハード ディスクや仮想ネットワーク インターフェイス カード (NIC) などのリソースを参照するメタデータにすぎません。 各仮想ハード ディスクには、VM に接続されたときにリースが割り当てられています。 データ ディスクは、VM の実行中でも接続および切断できますが、OS ディスクは、VM リソースを削除しない限り、切断することはできません。 VM が停止され、割り当てが解除された状態であっても、リースによって OS ディスクは引き続きその VM に関連付けられています。

VM を回復するには、まず VM リソース自体を削除します。 VM を削除しても、仮想ハード ディスクはストレージ アカウントに残されます。 VM を削除したら、仮想ハード ディスクを別の VM に接続してトラブルシューティングを行い、エラーを解決します。

ポータルで VM を選択し、 **[削除]** をクリックします。

![起動エラーを示す VM のブート診断のスクリーンショット](./media/troubleshoot-recovery-disks-portal-linux/stop-delete-vm.png)

VM の削除が完了するまで待ってから、仮想ハード ディスクを別の VM に接続します。 仮想ハード ディスクを別の VM に接続するには、仮想ハード ディスクを VM に関連付けているリースを解放しておく必要があります。


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>既存の仮想ハード ディスクを別の VM に接続する
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクの内容を参照して編集できるように、既存の仮想ハード ディスクをこのトラブルシューティング用 VM に接続します。 このプロセスにより、構成エラーの修正や、その他のアプリケーション ログ ファイルまたはシステム ログ ファイルの確認などが可能になります。 トラブルシューティングに使用する別の VM を選択または作成します。

1. ポータルでリソース グループを選択し、トラブルシューティング用 VM を選択します。 **[ディスク]** を選択し、 **[既存のディスクの接続]** をクリックします。

    ![ポータルで既存のディスクを接続する](./media/troubleshoot-recovery-disks-portal-linux/attach-existing-disk.png)

2. 既存の仮想ハード ディスクを選択するには、 **[VHD ファイル]** をクリックします。

    ![既存の VHD を参照する](./media/troubleshoot-recovery-disks-portal-linux/select-vhd-location.png)

3. ストレージ アカウントとコンテナーを選択し、既存の VHD をクリックします。 **[選択]** をクリックして選択を確定します。

    ![既存の VHD を選択する](./media/troubleshoot-recovery-disks-portal-linux/select-vhd.png)

4. VHD を選択したら、 **[OK]** をクリックして既存の仮想ハード ディスクを接続します。

    ![既存の仮想ハード ディスクの接続を確定する](./media/troubleshoot-recovery-disks-portal-linux/attach-disk-confirm.png)

5. 数秒後、VM の**ディスク** ウィンドウに、データ ディスクとして接続された既存の仮想ハード ディスクが表示されます。

    ![データ ディスクとして接続された既存の仮想ハード ディスク](./media/troubleshoot-recovery-disks-portal-linux/attached-disk.png)


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

    ![既存の仮想ハード ディスクを切断する](./media/troubleshoot-recovery-disks-portal-linux/detach-disk.png)

    VM からデータ ディスクが正常に切断されるまで待ってから、次に進みます。

## <a name="create-vm-from-original-hard-disk"></a>元のハード ディスクから VM を作成する
元の仮想ハード ディスクから VM を作成するには、[この Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-existing-vnet)を使用します。 このテンプレートでは、以前のコマンドで取得した VHD の URL を使用して、VM を既存の仮想ネットワークにデプロイします。 次のように、 **[Deploy to Azure (Azure にデプロイ)]** をクリックします。

![GitHub のテンプレートを使用して VM をデプロイする](./media/troubleshoot-recovery-disks-portal-linux/deploy-template-from-github.png)

デプロイするために、テンプレートが Azure Portal に読み込まれます。 新しい VM と既存の Azure リソースの名前を入力し、既存の仮想ハード ディスクの URL を貼り付けます。 デプロイを開始するには、 **[購入]** をクリックします。

![テンプレートを使用して VM をデプロイする](./media/troubleshoot-recovery-disks-portal-linux/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>ブート診断を再度有効にする
既存の仮想ハード ディスクから VM を作成したときに、ブート診断が自動的に有効にならない場合があります。 ブート診断の状態を確認し、必要に応じて有効にするには、ポータルで VM を選択します。 **[監視]** の **[診断設定]** をクリックします。 状態が **[オン]** になっており、 **[ブート診断]** の横のチェック ボックスがオンになっていることを確認します。 設定を変更した場合は、 **[保存]** をクリックします。

![ブート診断設定を更新する](./media/troubleshoot-recovery-disks-portal-linux/reenable-boot-diagnostics.png)

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>新しい OS ディスクを接続することでマネージド ディスク VM のトラブルシューティングを行う
1. 影響を受けている VM を停止します。
2. マネージド ディスク VM の OS ディスクの[マネージド ディスク スナップショットを作成](../windows/snapshot-copy-managed-disk.md)します。
3. [スナップショットから新しいマネージド ディスクを作成](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md)します。
4. [マネージド ディスクを VM のデータ ディスクとして接続](../windows/attach-disk-ps.md)します。
5. [手順 4 のデータ ディスクを OS ディスクに変更](../windows/os-disk-swap.md)します。

## <a name="next-steps"></a>次の手順
VM への接続の問題が発生した場合は、[Azure VM への SSH 接続のトラブルシューティング](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Linux VM でのアプリケーションの接続の問題のトラブルシューティング](../windows/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」をご覧ください。
