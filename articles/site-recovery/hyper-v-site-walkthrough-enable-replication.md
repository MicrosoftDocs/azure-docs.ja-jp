---
title: "Azure Site Recovery を使用した Azure への Hyper-V VM のレプリケーション (System Center VMM なし) を有効にする | Microsoft Docs"
description: "Azure Site Recovery サービスを使用した Azure への Hyper-V VM のレプリケーションを有効にするために必要な手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: bd31ef01-69f1-4591-a519-e42510a6e2f4
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: aabe99dbd375b80e4a87ca7a067927008672b4ed
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017

---

# <a name="step-10-enable-replication-for-hyper-v-vms-replicating-to-azure"></a>手順 10: Azure への Hyper-V VM のレプリケーションを有効にする


この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使って (System Center VMM で管理されていない) オンプレミスの Hyper-V 仮想マシンの Azure へのレプリケートを有効にする方法について説明します。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。




## <a name="before-you-start"></a>開始する前に

開始する前に、Azure ユーザー アカウントに、新しい仮想マシンを Azure にレプリケートできるようにするために必要な特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が付与されていることを確認してください。

## <a name="exclude-disks-from-replication"></a>レプリケーションからディスクを除外する

既定では、マシンのすべてのディスクがレプリケートされます。 レプリケーションからディスクを除外できます。 たとえば、一時的なデータや、マシンまたはアプリケーションを再起動するたびに更新されるデータ (pagefile.sys や SQL Server tempdb など) が保存されたディスクをレプリケートから除外できます。 [詳細情報](site-recovery-exclude-disk.md)


## <a name="replicate-vms"></a>VM をレプリケートする

以下のようにして、VM のレプリケーションを有効にします。          

1. **[アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 レプリケーションを初めてセットアップした後、**[+ レプリケート]** をクリックして、追加のマシンのレプリケーションを有効にできます。

    ![Enable replication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication.png)
2. **[ソース]** で、Hyper-V サイトを選択します。 次に、 **[OK]**をクリックします
3. **[ターゲット]** で、コンテナーのサブスクリプションと、フェールオーバー後に Azure で使用するフェールオーバー モデル (クラシックまたは Resource Manager) を選択します。
4. 使用するストレージ アカウントを選択します。 使用するアカウントがない場合は、[作成](#set-up-an-azure-storage-account)できます。 次に、 **[OK]**をクリックします
5. フェールオーバー後に作成された Azure VM が接続する Azure ネットワークとサブネットを選択します。

    - レプリケーションを有効にするすべてのマシンにネットワーク設定を適用するには、**[選択したマシン用に今すぐ構成します。]** を選択します。
    - マシンごとに Azure ネットワークを選択する場合は、**[後で構成する]** を選択します。
    - 使用するネットワークがない場合は、[作成](#set-up-an-azure-network)できます。 該当する場合は、サブネットを選択します。 次に、 **[OK]**をクリックします

   ![Enable replication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication11.png)

6. **[仮想マシン]** > **[仮想マシンの選択]** で、レプリケートする各マシンをクリックして選択します。 選択できるのは、レプリケーションを有効にできるマシンのみです。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication5-for-exclude-disk.png)

7. **[プロパティ]**  >  **[プロパティの構成]** で、選択した VM のオペレーティング システムと OS ディスクを選択します。
8. Azure VM の名前 (ターゲット名) が [Azure 仮想マシンの要件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠していることを確認します。
9. 既定では、VM のすべてのディスクがレプリケーションの対象として選択されています。 除外するディスクをオフにします。
10. **[OK]** をクリックして変更を保存します。 後で追加のプロパティを設定できます。

    ![Enable replication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication6-with-exclude-disk.png)

11. **[レプリケーションの設定]**  >  **[レプリケーション設定の構成]** で、保護対象の VM に適用するレプリケーション ポリシーを選択します。 次に、 **[OK]**をクリックします レプリケーション ポリシーを変更するには、**[レプリケーション ポリシー]**ポリシー名 > **[設定の編集]** の順にクリックします。 適用する変更は、既にレプリケートしているマシンと、新しいマシンに使用されます。


   ![Enable replication](./media/hyper-v-site-walkthrough-enable-replication/enable-replication7.png)

**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**保護の有効化**ジョブの進行状況を追跡できます。 **保護の最終処理** ジョブが実行されると、マシンはフェールオーバーできる状態になります。


## <a name="next-steps"></a>次のステップ


[手順 11: テスト フェールオーバーを実行する](hyper-v-site-walkthrough-test-failover.md)方法に関するページに進む

