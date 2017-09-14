---
title: "Azure Site Recovery を使用して VMM クラウド内の HYPER-V Vm の Azure へのレプリケーションを有効にする | Microsoft Docs"
description: "Azure Site Recovery を使用して VMM クラウド内の HYPER-V Vm の Azure へのレプリケーションを有効にする方法について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 89a2c4fc-7e03-4a86-a2c0-52831ccebc1a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 96a817e43a830e836f2faa4603fc88ed9c0b1828
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="step-11-enable-replication-to-azure-for-hyper-v-vms-in-vmm-clouds"></a>手順 11: VMM クラウド内の Hyper-V VM の Azure へのレプリケーションを有効にする

レプリケーション ポリシーを設定した後で、この記事に従って、System Center Virtual Machine Manager (VMM) で管理されているオンプレミスの Hyper-V 仮想マシン (VM) を、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure にレプリケートする方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="before-you-start"></a>開始する前に

Azure アカウントに Azure VM を作成するための正しい[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)があることを確認します。 Azure のロールベースのアクセス制御の[詳細について学習します](../active-directory/role-based-access-built-in-roles.md)。

## <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

既定では、マシンのすべてのディスクがレプリケートされます。 レプリケーションからディスクを除外できます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。 [詳細情報](site-recovery-exclude-disk.md)

## <a name="replicate-vms"></a>VM をレプリケートする

以下のようにして、VM のレプリケーションを有効にします。  

1. **[手順 2: アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 レプリケーションを初めて有効にした後は、コンテナーで **[+ レプリケート]** をクリックして、追加のマシンのレプリケーションを有効にします。

    ![Enable replication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication1.png)
2. **[ソース]** ブレードで、VMM サーバーと、Hyper-V ホストが配置されているクラウドを選択します。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-source.png)
3. **[ターゲット]** で、サブスクリプション、フェールオーバー後のデプロイメント モデル、レプリケートされたデータに使用するストレージ アカウントを選択します。

    ![Enable replication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication-target.png)
4. 使用するストレージ アカウントを選択します。 現在所有しているのと異なるストレージ アカウントを使用する場合は、[ストレージ アカウントを作成](#set-up-an-azure-storage-account)できます。 レプリケートされたデータに Premium Storage アカウントを使用している場合は、オンプレミスのデータの継続的な変更をキャプチャするレプリケーション ログを格納するために、追加の Standard ストレージ アカウントを選択する必要があります。Resource Manager モデルを使用してストレージ アカウントを作成するには、**[新規作成]** をクリックします。 クラシック モデルを使用してストレージ アカウントを作成する場合は、[Azure Portal](../storage/common/storage-create-storage-account.md) で作成できます。 次に、 **[OK]**をクリックします
5. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。 保護の対象として選択したすべてのマシンにネットワーク設定を適用する場合は、**[選択したマシン用に今すぐ構成します。]** を選択します。 マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。 現在所有しているのと異なるネットワークを使用する場合は、[ネットワークを作成](#set-up-an-azure-network)できます。 Resource Manager モデルを使用してネットワークを作成するには、**[新規作成]** をクリックします。 クラシック モデルを使用してネットワークを作成する場合は、[Azure Portal](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) で作成できます。 該当する場合は、サブネットを選択します。 次に、 **[OK]**をクリックします
6. **[仮想マシン]** > **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication5.png)

7. **[プロパティ]**  >  **[プロパティの構成]** で、選択した VM のオペレーティング システムと OS ディスクを選択します。

    - Azure VM の名前 (ターゲット名) が [Azure 仮想マシンの要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認します。   
    - 既定では、VM のすべてのディスクがレプリケーションの対象として選択されていますが、ディスクを消去して除外することもできます。

        - レプリケーションの帯域幅を減らすために、ディスクを除外したほうがよい場合があります。 たとえば、一時的なデータや、マシンまたはアプリを再起動するたびに更新されるデータ (pagefile.sys や Microsoft SQL Server tempdb など) が保存されたディスクをレプリケーションから除外できます。 ディスクをレプリケーションから除外するには、ディスクの選択を解除します。
        - 除外できるのは、ベーシック ディスクだけです。 OS ディスクを除外することはできません。
        - ダイナミック ディスクは除外しないことをお勧めします。 Site Recovery は、ゲスト VM 内の仮想ハード ディスクがベーシック ディスクであるかダイナミック ディスクであるかを識別できません。 依存するダイナミック ボリューム ディスクすべてが除外されていない場合に VM がフェールオーバーし、そのディスクのデータにアクセスできなくなると、保護されたダイナミック ディスクは障害が発生したディスクとして表示されます。
        - レプリケーションが有効になった後で、レプリケーション用のディスクを追加または削除することはできません。 ディスクを追加または除外する場合は、VM の保護を無効にし、再度有効にする必要があります。
        - Azure で手動で作成したディスクはフェールバックされません。 たとえば、3 つのディスクをフェールオーバーし、Azure VM に直接 2 つのディスクを作成した場合、フェールオーバーされた 3 つのディスクだけが Azure から Hyper-V にフェールバックされます。 Hyper-V から Azure へのフェールバックまたはレプリケーションの反転に、手動で作成されたディスクを含めることはできません。
        - アプリケーションが動作するために必要なディスクを除外した場合、Azure へのフェールオーバー後、レプリケートされたアプリケーションを実行できるように、Azure でディスクを手動で作成する必要があります。 別の方法として、Azure Automation を復旧計画に組み込んで、マシンのフェールオーバー時にディスクを作成することもできます。

    **[OK]** をクリックして変更を保存します。 後で追加のプロパティを設定できます。

    ![Enable replication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

8. **[レプリケーションの設定]**  >  **[レプリケーション設定の構成]** で、保護対象の VM に適用するレプリケーション ポリシーを選択します。 次に、 **[OK]**をクリックします レプリケーション ポリシーを変更するには、**[レプリケーション ポリシー]**ポリシー名 > **[設定の編集]** の順にクリックします。 適用する変更は、既にレプリケートしているマシンと、新しいマシンに使用されます。

   ![Enable replication](./media/vmm-to-azure-walkthrough-enable-replication/enable-replication7.png)

**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理**ジョブが実行されると、マシンはフェールオーバーできる状態になります。



## <a name="next-steps"></a>次のステップ

[手順 12: テスト フェールオーバーを実行する](vmm-to-azure-walkthrough-test-failover.md)方法に関するページに進む

