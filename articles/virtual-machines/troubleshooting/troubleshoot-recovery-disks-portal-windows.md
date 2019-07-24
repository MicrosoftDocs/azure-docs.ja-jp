---
title: Azure Portal でトラブルシューティング用 Windows VM を使用する | Microsoft Docs
description: Azure Portal で OS ディスクを復旧 VM に接続して、Azure の Windows 仮想マシンの問題のトラブルシューティングを行う方法について説明します
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/13/2018
ms.author: genli
ms.openlocfilehash: ec2da7d9f659f32c40f7a2685ab08be4eec27ed5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780628"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Azure Portal で OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う
Azure の Windows 仮想マシン (VM) で起動エラーまたはディスク エラーが発生した場合、仮想ハード ディスク自体でトラブルシューティングの手順を実行することが必要な場合があります。 一般的な例として、VM の正常な起動を妨げる失敗したアプリケーション更新が挙げられます。 この記事では、Azure Portal で仮想ハード ディスクを別の Windows VM に接続してエラーを修正し、元の VM を再作成する方法について詳しく説明します。

## <a name="recovery-process-overview"></a>回復プロセスの概要
トラブルシューティングのプロセスは次のとおりです。

1. 仮想ハード ディスクを保持して、問題が発生している VM を削除します。
2. トラブルシューティングのために、仮想ハード ディスクを別の Windows VM に接続してマウントします。
3. トラブルシューティング用 VM に接続します。 元の仮想ハード ディスクで、ファイルを編集するか、任意のツールを実行して問題を解決します。
4. 仮想ハード ディスクのマウントを解除し、トラブルシューティング用 VM から切断します。
5. 元の仮想ハード ディスクを使用して VM を作成します。

マネージド ディスクを使用する VM の場合、Azure PowerShell を使用して VM 用の OS ディスクを変更できるようになりました。 VM を削除して再作成する必要はもうありません。 詳細については、「[Azure PowerShell で OS ディスクを復旧 VM に接続して Windows VM のトラブルシューティングを行う](troubleshoot-recovery-disks-windows.md)」を参照してください。

## <a name="determine-boot-issues"></a>起動の問題を特定する
VM が正常に起動できない理由を特定するには、ブート診断と VM のスクリーンショットを調べます。 一般的な例として、失敗したアプリケーション更新や、基になる仮想ハード ディスクが削除または移動されている場合が挙げられます。

ポータルで VM を選択し、下へスクロールして **[サポート + トラブルシューティング]** セクションを表示します。 **[ブート診断]** をクリックしてスクリーンショットを表示します。 VM で問題が発生している理由を判断するのに役立つ特定のエラー メッセージまたはエラー コードに注意してください。 

![VM のブート診断のコンソール ログの表示](./media/troubleshoot-recovery-disks-portal-windows/screenshot-error.png)

**[スクリーン ショットのダウンロード]** をクリックして、VM スクリーン ショットのキャプチャをダウンロードすることもできます。

## <a name="view-existing-virtual-hard-disk-details"></a>既存の仮想ハード ディスクの詳細を表示する
仮想ハード ディスクを別の VM に接続するには、仮想ハード ディスク (VHD) の名前を確認しておく必要があります。 

ポータルでリソース グループを選択し、ストレージ アカウントを選択します。 次の例のように、**[BLOB]** をクリックします。

![ストレージ BLOB を選択する](./media/troubleshoot-recovery-disks-portal-windows/storage-account-overview.png)

通常は、仮想ハード ディスクを格納する **vhds** という名前のコンテナーがあります。 コンテナーを選択して、仮想ハード ディスクの一覧を表示します。 VHD の名前を書き留めます (通常、プレフィックスは VM の名前です)。

![ストレージ コンテナーで VHD を特定する](./media/troubleshoot-recovery-disks-portal-windows/storage-container.png)

一覧から既存の仮想ハード ディスクを選択し、以降の手順で使用できるように URL をコピーします。

![既存の仮想ハード ディスクの URL をコピーする](./media/troubleshoot-recovery-disks-portal-windows/copy-vhd-url.png)


## <a name="delete-existing-vm"></a>既存の VM を削除する
Azure では、仮想ハード ディスクと VM は 2 つの異なるリソースです。 仮想ハード ディスクには、オペレーティング システム自体、アプリケーション、構成が格納されています。 VM 自体は、サイズや場所を定義し、仮想ハード ディスクや仮想ネットワーク インターフェイス カード (NIC) などのリソースを参照するメタデータにすぎません。 各仮想ハード ディスクには、VM に接続されたときにリースが割り当てられています。 データ ディスクは、VM の実行中でも接続および切断できますが、OS ディスクは、VM リソースを削除しない限り、切断することはできません。 VM が停止され、割り当てが解除された状態であっても、リースによって OS ディスクは引き続きその VM に関連付けられています。

VM を回復するには、まず VM リソース自体を削除します。 VM を削除しても、仮想ハード ディスクはストレージ アカウントに残されます。 VM を削除したら、仮想ハード ディスクを別の VM に接続してトラブルシューティングを行い、エラーを解決します。

ポータルで VM を選択し、**[削除]** をクリックします。

![起動エラーを示す VM のブート診断のスクリーンショット](./media/troubleshoot-recovery-disks-portal-windows/stop-delete-vm.png)

VM の削除が完了するまで待ってから、仮想ハード ディスクを別の VM に接続します。 仮想ハード ディスクを別の VM に接続するには、仮想ハード ディスクを VM に関連付けているリースを解放しておく必要があります。


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>既存の仮想ハード ディスクを別の VM に接続する
次のいくつかの手順では、トラブルシューティングのために別の VM を使用します。 ディスクの内容を参照して編集できるように、既存の仮想ハード ディスクをこのトラブルシューティング用 VM に接続します。 このプロセスにより、構成エラーの修正や、その他のアプリケーション ログ ファイルまたはシステム ログ ファイルの確認などが可能になります。 トラブルシューティングに使用する別の VM を選択または作成します。

1. ポータルでリソース グループを選択し、トラブルシューティング用 VM を選択します。 **[ディスク]** を選択し、**[既存のディスクの接続]** をクリックします。

    ![ポータルで既存のディスクを接続する](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. 既存の仮想ハード ディスクを選択するには、**[VHD ファイル]** をクリックします。

    ![既存の VHD を参照する](./media/troubleshoot-recovery-disks-portal-windows/select-vhd-location.png)

3. ストレージ アカウントとコンテナーを選択し、既存の VHD をクリックします。 **[選択]** をクリックして選択を確定します。

    ![既存の VHD を選択する](./media/troubleshoot-recovery-disks-portal-windows/select-vhd.png)

4. VHD を選択したら、**[OK]** をクリックして既存の仮想ハード ディスクを接続します。

    ![既存の仮想ハード ディスクの接続を確定する](./media/troubleshoot-recovery-disks-portal-windows/attach-disk-confirm.png)

5. 数秒後、VM の**ディスク** ウィンドウに、データ ディスクとして接続された既存の仮想ハード ディスクが表示されます。

    ![データ ディスクとして接続された既存の仮想ハード ディスク](./media/troubleshoot-recovery-disks-portal-windows/attached-disk.png)


## <a name="mount-the-attached-data-disk"></a>接続されたデータ ディスクをマウントする

1. VM へのリモート デスクトップ接続を開きます。 ポータルで VM を選び、**[接続]** をクリックします。 RDP 接続ファイルをダウンロードして開きます。 次のように、VM にサインインするための資格情報を入力します。

    ![リモート デスクトップを使って VM にサインインします](./media/troubleshoot-recovery-disks-portal-windows/open-remote-desktop.png)

2. **[サーバー マネージャー]** を開き、**[ファイル サービスと記憶域サービス]** を選択します。 

    ![[サーバー マネージャー] で [ファイル サービスと記憶域サービス] を選択](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. データ ディスクが自動的に検出され、接続されます。 接続されているディスクの一覧を表示するには、**[ディスク]** を選択します。 データ ディスクを選び、ドライブ文字を含むボリューム情報を表示できます。 次の例は、接続されて **F:** を使っているデータ ディスクを示しています。

    ![[サーバー マネージャー] 内の接続されているディスクとボリュームの情報](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)


## <a name="fix-issues-on-original-virtual-hard-disk"></a>元の仮想ハード ディスクで問題を修正する
既存の仮想ハード ディスクをマウントすることで、必要に応じてメンテナンスやトラブルシューティングの手順を実行できるようになります。 問題に対処したら、次の手順に進みます。


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>元の仮想ハード ディスクのマウントを解除して切断する
エラーが解決したら、トラブルシューティング用 VM から既存の仮想ハード ディスクを切断します。 仮想ハード ディスクをトラブルシューティング用 VM に接続しているリースを解放するまで、仮想ハード ディスクを他の VM で使用することはできません。

1. VM への RDP セッションから、**[サーバー マネージャー]** を開き、**[ファイル サービスと記憶域サービス]** を選択します。

    ![[サーバー マネージャー] で [ファイル サービスと記憶域サービス] を選択](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. **[ディスク]** を選択してから、データ ディスクを選びます。 データ ディスクを右クリックし、**[オフラインにする]** を選択します。

    ![データ ディスクをサーバー マネージャーでオフラインに設定](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. 仮想ハード ディスクを VM から切断します。 Azure Portal で VM を選択し、**[ディスク]** をクリックします。 既存の仮想ハード ディスクを選択し、**[切断]** をクリックします。

    ![既存の仮想ハード ディスクを切断する](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    VM からデータ ディスクが正常に切断されるまで待ってから、次に進みます。

## <a name="create-vm-from-original-hard-disk"></a>元のハード ディスクから VM を作成する
元の仮想ハード ディスクから VM を作成するには、[この Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd-new-or-existing-vnet)を使用します。 このテンプレートでは、以前のコマンドで取得した VHD の URL を使用して、VM を既存の、または新しい仮想ネットワークにデプロイします。 次のように、**[Deploy to Azure (Azure にデプロイ)]** をクリックします。

![GitHub のテンプレートを使用して VM をデプロイする](./media/troubleshoot-recovery-disks-portal-windows/deploy-template-from-github.png)

デプロイするために、テンプレートが Azure Portal に読み込まれます。 新しい VM と既存の Azure リソースの名前を入力し、既存の仮想ハード ディスクの URL を貼り付けます。 デプロイを開始するには、**[購入]** をクリックします。

![テンプレートを使用して VM をデプロイする](./media/troubleshoot-recovery-disks-portal-windows/deploy-from-image.png)


## <a name="re-enable-boot-diagnostics"></a>ブート診断を再度有効にする
既存の仮想ハード ディスクから VM を作成したときに、ブート診断が自動的に有効にならない場合があります。 ブート診断の状態を確認し、必要に応じて有効にするには、ポータルで VM を選択します。 **[監視]** の **[診断設定]** をクリックします。 状態が **[オン]** になっており、**[ブート診断]** の横のチェック ボックスがオンになっていることを確認します。 設定を変更した場合は、**[保存]** をクリックします。

![ブート診断設定を更新する](./media/troubleshoot-recovery-disks-portal-windows/reenable-boot-diagnostics.png)

## <a name="next-steps"></a>次の手順
VM への接続の問題が発生した場合は、[Azure VM への RDP 接続のトラブルシューティング](troubleshoot-rdp-connection.md)に関する記事をご覧ください。 VM で実行されているアプリケーションへのアクセスに関する問題については、[Windows VM でのアプリケーションの接続の問題のトラブルシューティング](troubleshoot-app-connection.md)に関する記事をご覧ください。

Resource Manager の使用方法の詳細については、「[Azure Resource Manager の概要](../../azure-resource-manager/resource-group-overview.md)」をご覧ください。

