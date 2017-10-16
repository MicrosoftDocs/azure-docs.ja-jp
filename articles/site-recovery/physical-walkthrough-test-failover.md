---
title: "Azure Site Recovery を使用して Azure に物理サーバーをレプリケートするためのテスト フェールオーバーを実行する | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して Azure に物理サーバーをレプリケートするためのテスト フェールオーバーを実行するために必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94aa3bfc700cad3de9fc5516c0c9a4d86ade3fed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-11-run-a-test-failover-of-physical-servers-to-azure"></a>手順 11: Azure への物理サーバーのテスト フェールオーバーを実行する

この記事では、Azure ポータルで [Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの物理サーバーから Azure へのテスト フェールオーバーを実行する方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="before-you-start"></a>開始する前に

テスト フェールオーバーを実行する前に、サーバーのプロパティを確認し、必要な変更を加えることをお勧めします。 **[レプリケートされたアイテム]** で VM のプロパティにアクセスできます。 **[要点]** ブレードにマシンの設定と状態に関する情報が表示されます。

## <a name="managed-disk-considerations"></a>管理ディスクに関する考慮事項

[管理ディスク](../virtual-machines/windows/managed-disks-overview.md)を使用すると、VM ディスクに関連付けられているストレージ アカウントを管理できるため、Azure VM のディスク管理が簡素化されます。 

- サーバーの保護を有効にすると、VM データがストレージ アカウントにレプリケートされます。 管理ディスクは、フェールオーバーが発生した場合にのみ作成されて VM に接続されます。
- 管理ディスクは、Resource Manager モデルを使用してデプロイされた Azure VM に対してのみ作成することができます。  
- この設定が有効な場合、**[管理ディスクを使用]** が有効になっているリソース グループの可用性セットのみを選択できます。 管理ディスクを持つ VM は、**[管理ディスクを使用]** が **[はい]** に設定されている可用性セットに含まれている必要があります。 VM に対してこの設定が有効でない場合は、有効な管理ディスクを持たないリソース グループの可用性セットのみを選択できます。
- 管理ディスクと可用性セットの詳細については、[こちら](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set)を参照してください。
- レプリケーションに使用するストレージ アカウントが Storage Service Encryption によって暗号化されている場合、フェールオーバー時に管理ディスクを作成することはできません。 この場合は、管理ディスクの使用を有効にしないか、VM の保護を無効にした後、暗号化が有効でないストレージ アカウントを使用して再度有効にします。 [詳細情報](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption)


## <a name="network-considerations"></a>ネットワークに関する考慮事項

フェールオーバー後に作成された Azure VM のターゲット IP アドレスを設定できます。

- アドレスを指定しなかった場合、フェールオーバーされたマシンで DHCP が使用されます。
- フェールオーバーで使用できないアドレスを設定した場合、フェールオーバーは機能しません。
- テスト フェールオーバー ネットワークのアドレスを利用できる場合、テスト フェールオーバーに同じターゲット IP アドレスを使用できます。
- ネットワーク アダプターの数は、ターゲット仮想マシンに指定したサイズによって異なります。

     - ソース マシン上のネットワーク アダプターの数が、ターゲット マシンのサイズに許可されているアダプターの数以下の場合、ターゲットのアダプターの数は、ソースと同じになります。
     - ソース仮想マシン用のアダプターの数が、ターゲットのサイズに許可されている数を超える場合は、ターゲットの最大サイズが使用されます。
     - たとえば、ソース マシンに 2 つのネットワーク アダプターがあり、ターゲット マシンのサイズが 4 つをサポートしている場合は、ターゲット マシンのアダプターの数は、2 つになります。 ソース マシンに 2 つのアダプターがあるが、サポートされているターゲット サイズで 1 つしかサポートしていない場合、ターゲット マシンのアダプターの数は 1 つだけになります。     
   - 仮想マシンにネットワーク アダプターが複数ある場合、これらのアダプターはすべて同じネットワークに接続されます。
   - 仮想マシンにネットワーク アダプターが複数ある場合は、一覧で最初に表示されるアダプターが、Azure 仮想マシンの*既定*のネットワーク アダプターとなります。
 - IP アドレスの詳細については、[こちら](vmware-walkthrough-network.md)を参照してください。



## <a name="view-and-modify-vm-settings"></a>VM 設定を表示および変更する

フェールオーバーを実行する前に、ソース サーバーのプロパティを確認することをお勧めします。

1. **[保護されているアイテム]** で、**[レプリケートされたアイテム]** をクリックし、マシンをクリックします。
2. **[レプリケートされたアイテム]** ウィンドウには、マシン情報、正常性状態、および最新の使用可能な復旧ポイントの概要が表示されます。 **[プロパティ]** をクリックすると、詳細が表示されます。
3. **[コンピューティングとネットワーク]** では、次の操作を行うことができます。
    - Azure VM 名を変更する。 この名前は [Azure 要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)を満たす必要があります。
    - フェールオーバー後の [リソース グループ] を指定する。
    - Azure VM のターゲット サイズを指定する。
    - [可用性セット](../virtual-machines/windows/tutorial-availability-sets.md)を選択する。
    - [管理ディスク](#managed-disk-considerations)を使用するかどうかを指定する。 Azure への移行時に管理ディスクをマシンに接続する場合は、**[はい]** を選択します。
    - ネットワーク設定 (フェールオーバー後に Azure VM が配置されるネットワークやサブネット、割り当てられる IP アドレスなど) を表示または変更する。
4. **[ディスク]** で、VM のオペレーティング システム ディスクとデータ ディスクに関する情報を確認できます。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

すべてのセットアップが完了したら、テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。

- フェールオーバー後に RDP を使用して Azure VM に接続する場合は、[接続の準備](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)をします。
 - すべてをテストするには、テスト環境に Active Directory と DNS をコピーする必要があります。 [詳細情報](site-recovery-active-directory.md#test-failover-considerations)
 - テスト フェールオーバーの詳細については、[こちらの記事](site-recovery-test-failover-to-azure.md)を参照してください。
- 始める前に、簡単なビデオ概要を見ます。

     
     >[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]

次に、フェールオーバーを実行します。

1. 1 台のマシンをフェールオーバーする場合は、**[設定]** > **[レプリケートされたアイテム]** で該当するマシンをクリックし、**[+ テスト フェールオーバー]** アイコンをクリックします。

    ![[テスト フェールオーバー]](./media/physical-walkthrough-test-failover/test-failover.png)

2. 復旧計画をフェールオーバーする場合は、**[設定]** > **[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。  

3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続する Azure ネットワークを選択します。

4. **[OK]** をクリックすると、フェールオーバーが開始されます。 進行状況を追跡するには、マシンをクリックしてそのプロパティを開くか、コンテナー名 > **[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で **[テスト フェールオーバー]** ジョブをクリックします。

5. フェールオーバーの完了後は、Azure ポータルの **[仮想マシン]** にレプリカの Azure も表示されるようになります。 VM が適切なサイズであること、適切なネットワークに接続していること、実行されていることを確認する必要があります。

6. フェールオーバー後の接続の準備が完了したら、Azure VM に接続できるようになります。

### <a name="delete-test-failover-vms"></a>テスト フェールオーバー VM を削除する

1. 完了したら、復旧計画またはマシンの **[Cleanup test failover]\(テスト フェールオーバーのクリーンアップ\)** をクリックします。
2. **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。
3. クリーンアップ アクションによって、テスト フェールオーバー中に作成された Azure VM が削除されます。

## <a name="summary"></a>概要

テスト フェールオーバーの正常な完了は、物理サーバーがレプリケートされ、Azure にフェールオーバーできることを意味します。 これで、組織の要件に従ってフェールオーバーを実行できます。 

現時点では、Azure から物理サーバーにはフェールオーバーできないことを忘れないでください。 VMware VM にフェールバックする必要があります。 つまり、フェールバックするには、オンプレミスの VMware インフラストラクチャが必要です。 Azure VM の VMware へのフェールバックの詳細については、[こちら](site-recovery-failback-azure-to-vmware.md)を参照してください。


## <a name="next-steps"></a>次のステップ

- 必要に応じて[フェールオーバーを実行](site-recovery-failover.md)します。
