---
title: "Azure Site Recovery を使用したセカンダリ サイトへの Hyper-V レプリケーションを有効にする | Microsoft Docs"
description: "Azure Site Recovery を使用したセカンダリ System Center VMM サイトへの Hyper-V VM レプリケーションを有効にする方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>手順 9: セカンダリ サイトへの Hyper-V VM レプリケーションを有効にする


レプリケーション ポリシーを設定したら、この記事の手順に従って、オンプレミスの HYPER-V 仮想マシン (VM) を、[Azure Site Recovery](site-recovery-overview.md) を使用してセカンダリ System Center Virtual Machine Manager (VMM) サイトにレプリケートできるようにします。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。



## <a name="select-vms-to-replicate"></a>レプリケートする VM の選択

1. **[手順 2: アプリケーションをレプリケートする]** > **[ソース]** の順にクリックします。 

    ![Enable replication](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. **[ソース]** で、VMM サーバーと、レプリケートする Hyper-V ホストが配置されているクラウドを選択します。 次に、 **[OK]**をクリックします

    ![Enable replication](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. **[ターゲット]** で、セカンダリ VMM サーバーとクラウドを確認します。
4. **[仮想マシン]** で、保護する VM を一覧から選択します。

    ![[仮想マシンの保護の有効化]](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

**[ジョブ]** > **[Site Recovery ジョブ]** の順にクリックして、**[保護を有効にする]** アクションの進行状況を追跡できます。 **[保護の最終処理]** ジョブが完了すると、初期レプリケーションが完了し、仮想マシンがフェールオーバーを実行できる状態になります。

以下の点に注意してください。

- VMM コンソールでも仮想マシンの保護を有効にできます。 仮想マシンのプロパティの **[Azure Site Recovery]** タブで、ツール バーにある **[保護を有効にする]** をクリックします。
- レプリケーションを有効にした後、**[レプリケートされたアイテム]** で VM のプロパティを確認できます。 **[要点]** ダッシュボードでは、VM とその状態に関するレプリケーション ポリシーの情報を確認できます。 **[プロパティ]** をクリックすると、詳細が表示されます。

## <a name="onboard-existing-vms"></a>既存の VM のオンボード

Hyper-V レプリカを使用してレプリケートされる VMM 内に既存の仮想マシンがある場合は、次の手順で Azure Site Recovery のレプリケーションにそれらの仮想マシンを追加できます。

1. 既存の VM をホストしている Hyper-V サーバーがプライマリ クラウドに存在し、レプリカ仮想マシンをホストしている Hyper-V サーバーがセカンダリ クラウドに存在することを確認します。
2. プライマリ VMM クラウドのレプリケーション ポリシーが構成されていることを確認します。
3. プライマリ仮想マシンのレプリケーションを有効にします。 Azure Site Recovery と VMM で同じレプリカ ホストと仮想マシンが検出され、Azure Site Recovery によって、指定の設定を使用してレプリケーションの再使用と再確立が行われます。


## <a name="next-steps"></a>次のステップ

「[手順 10: テスト フェールオーバーを実行する](vmm-to-vmm-walkthrough-test-failover.md)」に進みます。

