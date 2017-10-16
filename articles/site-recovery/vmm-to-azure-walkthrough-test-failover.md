---
title: "Azure への Hyper-V のレプリケーション (System Center VMM あり) のテスト フェールオーバーを実行する | Microsoft Docs"
description: "Azure Site Recovery サービスを使用した Azure への VMM クラウド内の Hyper-V VM のレプリケーションのテスト フェールオーバーを実行するために必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7b562a23-7ba7-48ee-905d-c22b4b5d6466
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/25/2017
ms.author: raynew
ms.openlocfilehash: 4688fc4bc74a9e0e04487cfbe965006070fd9a7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-run-a-test-failover-for-hyper-v-replication-with-vmm-to-azure"></a>手順 11: Azure への Hyper-V のレプリケーション (VMM あり) のテスト フェールオーバーを実行する

[Hyper-V VM のレプリケーションを有効に](vmm-to-azure-walkthrough-enable-replication.md)した後で、この記事に従って、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用し、System Center Virtual Machine Manager (VMM) クラウドで管理されているオンプレミスの Hyper-V 仮想マシンから Azure へのテスト フェールオーバーを実行します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="before-you-start"></a>開始する前に

テスト フェールオーバーを実行する前に、VM のプロパティを確認し、必要な変更を加えることをお勧めします。 **[レプリケートされたアイテム]** で VM のプロパティにアクセスできます。 **[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。

## <a name="managed-disk-considerations"></a>管理ディスクに関する考慮事項

[管理ディスク](../virtual-machines/windows/managed-disks-overview.md)を使用すると、VM ディスクに関連付けられているストレージ アカウントを管理できるため、Azure VM のディスク管理が簡素化されます。 

- 管理ディスクは、Azure へのフェールオーバーが発生した場合にのみ作成され、VM に接続されます。 保護を有効にすると、オンプレミスの VM からのデータがストレージ アカウントにレプリケートされます。
- 管理ディスクは、Resource Manager デプロイメント モデルでデプロイされた VM に対してのみ作成することができます。
- Azure からオンプレミス Hyper-V 環境へのフェールバックは、現時点では、管理ディスクを使用するマシンではサポートされていません。 移行だけ (フェールバックのない Azure へのフェールオーバー) を行っている場合にのみ、**[管理ディスクを使用]** を **[はい]** に設定する必要があります。
- この設定が有効な場合、**[管理ディスクを使用]** が有効になっているリソース グループの可用性セットのみを選択できます。 管理ディスクを持つ VM は、**[管理ディスクを使用]** が **[はい]** に設定されている可用性セットに含まれている必要があります。 VM に対してこの設定が有効でない場合は、有効な管理ディスクを持たないリソース グループの可用性セットのみを選択できます。 [詳細情報](../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)
- - レプリケーションに使用するストレージ アカウントが Storage サービスの暗号化で暗号化されている場合、フェールオーバー時に管理ディスクを作成することはできません。 この場合は、管理ディスクの使用を有効にしないか、VM の保護を無効にした後、暗号化が有効でないストレージ アカウントを使用して再度有効にします。 [詳細情報](../virtual-machines/windows/managed-disks-overview.md#managed-disks-and-encryption)

 
## <a name="network-considerations"></a>ネットワークに関する考慮事項
    
- フェールオーバー後に Azure VM に対して使われるターゲット IP アドレスを設定できます。 アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。 フェールオーバーで使用できないアドレスが設定された場合、フェールオーバーは失敗します。 テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。
- ネットワーク アダプターの数は、次に示すように、ターゲット仮想マシンに指定したサイズによって異なります。
    - ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
    - ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
    - たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。     
- VM に複数のネットワーク アダプターがある場合は、すべて同じネットワークに接続されます。
- 仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure 仮想マシンの*既定*のネットワーク アダプターとなります。


## <a name="view-and-manage-vm-settings"></a>VM の設定を表示して管理する

フェールオーバーを実行する前に、ソース マシンのプロパティを確認することをお勧めします。

1. **[Protected Items]\(保護されたアイテム\)** で、**[レプリケートされたアイテム]** をクリックし、VM をクリックします。

    ![Enable replication](./media/vmm-to-azure-walkthrough-test-failover/test-failover1.png)
2. **[レプリケートされたアイテム]** ウィンドウには、VM 情報、正常性ステータス、および最新の使用可能な復旧ポイントの概要が表示されます。 **[プロパティ]** をクリックすると、詳細が表示されます。

    ![Enable replication](./media/vmm-to-azure-walkthrough-test-failover/test-failover2.png)
3. **[コンピューティングとネットワーク]** では、次の操作を行うことができます。
    - Azure VM 名を変更する。 この名前は [Azure 要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)を満たす必要があります。
    - フェールオーバー後の [リソース グループ] を指定する。
    - Azure VM のターゲット サイズを指定する。
    - [可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)を選択する。
    - [管理ディスク](#managed-disk-considerations)を使用するかどうかを指定する。 Azure への移行時に管理ディスクをマシンに接続する場合は、**[はい]** を選択します。
    - ネットワーク設定 (フェールオーバー後に Azure VM が配置されるネットワークやサブネット、割り当てられる IP アドレスなど) を表示または変更する。

    ![Enable replication](./media/vmm-to-azure-walkthrough-test-failover/test-failover4.png)
4. **[ディスク]** で、VM のオペレーティング システム ディスクとデータ ディスクに関する情報を確認できます。


## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。

- フェールオーバー後に RDP を使用して Azure VM に接続する場合は、[接続の準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)をします。
 - すべてをテストするには、テスト環境に Active Directory と DNS をコピーする必要があります。 [詳細情報](site-recovery-active-directory.md#test-failover-considerations)
 - テスト フェールオーバーの詳細については、[この記事](site-recovery-test-failover-to-azure.md)を参照してください。
 
 次に、フェールオーバーを実行します。

1. 1 つの仮想マシンをフェールオーバーする場合は、**[レプリケートされたアイテム]** で、その VM をクリックし、**[+ テスト フェールオーバー]** をクリックします。
2. 復旧計画をフェールオーバーする場合は、**[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。
3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。
4. **[OK]** をクリックすると、フェールオーバーが開始されます。 進行状況を追跡するには、VM をクリックしてそのプロパティを開くか、コンテナー名をクリックし、**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[テスト フェールオーバー]** ジョブをクリックします。
5. フェールオーバーの完了後は、Azure Portal の **[仮想マシン]** にレプリカの Azure マシンも表示されるようになります。 VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。
6. フェールオーバー後の接続の準備が完了したら、Azure VM に接続できるようになります。
7. 完了したら、復旧計画の **[Cleanup test failover]\(テスト フェールオーバーのクリーンアップ\)** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 これで、テスト フェールオーバー中に作成された仮想マシンが削除されます。



## <a name="next-steps"></a>次のステップ

- さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。
- [フェールバックの詳細を確認して](site-recovery-failback-from-azure-to-hyper-v.md)、Azure VM をオンプレミスのプライマリ VMM クラウドにフェールバックし、再度レプリケートします。

