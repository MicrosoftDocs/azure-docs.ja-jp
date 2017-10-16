---
title: "Azure Site Recovery を使用したセカンダリ VMM サイトへの Hyper-V レプリケーションの前提条件を確認する | Microsoft Docs"
description: "Azure Site Recovery を使用してセカンダリ VMM サイトに Hyper-V VM をレプリケートする場合の前提条件について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>手順 2: セカンダリ VMM サイトへの Hyper-V VM レプリケーションの前提条件と制限事項を確認する


[シナリオのアーキテクチャ](vmm-to-vmm-walkthrough-architecture.md)を確認したら、この記事を参照し、System Center Virtual Machine Manager (VMM) クラウドで管理されているオンプレミスの Hyper-V 仮想マシン (VM) を、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用してセカンダリ サイトにレプリケートするときに、デプロイの前提条件を理解していることを確認します。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## <a name="prerequisites-and-limitations"></a>前提条件と制限事項

**要件** | **詳細**
--- | ---
**Azure** | [Microsoft Azure](http://azure.microsoft.com/) サブスクリプション。<br/><br/> アカウントがなくても、 [無料試用版](https://azure.microsoft.com/pricing/free-trial/)を使用できます。<br/><br/> [こちら](https://azure.microsoft.com/pricing/details/site-recovery/) をご覧ください。<br/><br/> Site Recovery でサポートされるリージョンについては、[Azure Site Recovery の価格の詳細](https://azure.microsoft.com/pricing/details/site-recovery/)に関するページの利用可能なリージョンに関するセクションをご確認ください。
**VMM サーバー** | 2 つの VMM サーバーを用意して、プライマリ サイトに 1 つをデプロイし、セカンダリ サイトにもう 1 つをデプロイすることをお勧めします。<br/><br/> 単一の VMM サーバー上のクラウド間のレプリケーションはサポートされています。<br/><br/> VMM サーバーは、最新の更新プログラムがインストールされている System Center 2012 SP1 以降を実行している必要があります。<br/><br/> VMM サーバーは、インターネットにアクセスできる必要があります。
**VMM クラウド** | 各 VMM サーバーには 1 つ以上のクラウドがあり、すべてのクラウドには Hyper-V キャパシティ プロファイルが設定されている必要があります。 <br/><br/>クラウドには、1 つ以上の VMM ホスト グループが含まれている必要があります。<br/><br/> VMM サーバーが 1 つしかない場合は、プライマリとセカンダリとして作動するために少なくとも 2 つのクラウドが必要です。
**Hyper-V** | Hyper-V サーバーでは、Hyper-V ロールを設定済みの Windows Server 2012 以降が実行され、最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V サーバーに 1 つ以上の VM が含まれている必要があります。<br/><br/>  Hyper-V ホスト サーバーが、プライマリおよびセカンダリの VMM クラウドに配置されている必要があります。<br/><br/> Windows Server 2012 R2 のクラスターで Hyper-V を実行する場合は、[更新プログラム 2961977](https://support.microsoft.com/kb/2961977) をインストールします。<br/><br/> Windows Server 2012 上のクラスターで Hyper-V を実行する場合、静的 IP アドレス ベースのクラスターが存在すると、クラスター ブローカーは自動では作成されません。 クラスター ブローカーは手動で構成してください。 詳細については、[こちら](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx)を参照してください。<br/><br/> Hyper-V サーバーは、インターネットにアクセスできる必要があります。




## <a name="next-steps"></a>次のステップ

[手順 3: ネットワークを計画する](vmm-to-vmm-walkthrough-network.md)方法に関するページに進みます。
