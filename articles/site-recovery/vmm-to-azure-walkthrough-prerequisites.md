---
title: "Azure Site Recovery を使用した Azure への Hyper-V レプリケーション (System Center VMM あり) の前提条件を確認する | Microsoft Docs"
description: "Azure Site Recovery による Azure への VMM クラウド内のオンプレミスの Hyper-V VM のレプリケーション、フェールオーバー、リカバリーのセットアップの前提条件について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>手順 2: Azure への Hyper-V (VMM あり) のレプリケーションの前提条件を確認する

[シナリオのアーキテクチャ](vmm-to-azure-walkthrough-architecture.md)を確認したら、この記事を読んでデプロイの前提条件をご確認ください。 

## <a name="prerequisites-and-limitations"></a>前提条件と制限事項

**要件** | **詳細**
--- | ---
**Azure アカウント** | [Microsoft Azure アカウント](http://azure.microsoft.com/)が必要です。
**Azure Storage** | レプリケートしたデータを格納するには Azure ストレージ アカウントが必要です。<br/><br/> ストレージ アカウントは、Azure Recovery Services コンテナーと同じリージョンに存在する必要があります。<br/><br/>[geo 冗長ストレージ](../storage/common/storage-redundancy.md#geo-redundant-storage)またはローカル冗長ストレージを使うことができます。 geo 冗長ストレージをお勧めします。 geo 冗長ストレージを使用すると、地域的障害が発生した場合やプライマリ リージョンが復旧できない場合にデータの復元性を確保できます。<br/><br/> 標準の Azure ストレージ アカウントを使用するか、Azure [Premium Storage](../storage/common/storage-premium-storage.md) を使用することができます。 Premium Storage では I/O 集中型ワークロードをホストでき、通常、常に高 I/O パフォーマンスと低待機時間を必要とする VM に使用されます。 レプリケートしたデータに Premium Storage を使用する場合は、Standard Storage アカウントも必要です。 Standard Storage アカウントでは、オンプレミス データの継続的な変更をキャプチャするレプリケーション ログを保存します。
**Azure ネットワーク** | フェールオーバー後に Azure VM の接続先となる [Azure ネットワーク](../virtual-network/virtual-network-get-started-vnet-subnet.md)が必要です。 Azure ネットワークは、Recovery Services コンテナーと同じリージョンに存在する必要があります。
**オンプレミスの VMM サーバー** | System Center 2012 R2 以降を実行している、1 つ以上の VMM サーバーが必要です。<br/><br/> 各 VMM サーバーには 1 つ以上のプライベート クラウドが必要です。 各クラウドには 1 つ以上のホスト グループが必要です。<br/><br/> VMM サーバーがインターネットにアクセスできる必要があります。
**オンプレミスの Hyper-V** | Hyper-V ホスト サーバーは、Hyper-V ロールが有効な Windows Server 2012 R2 以降か、Microsoft Hyper-V Server 2012 R2 を実行している必要があります。 最新の更新プログラムがインストールされている必要があります。<br/><br/> Hyper-V ホストは、(VMM クラウド内にある) VMM ホスト グループに属している必要があります。<br/><br/> ホストにはレプリケートする 1 つ以上の VM が必要です。<br/><br/> Hyper-V ホストは、Azure へのレプリケーションのために、直接またはプロキシを経由してインターネットに接続されている必要があります。 Hyper-V サーバーには、記事 [2961977](https://support.microsoft.com/kb/2961977) で説明されている修正プログラムが必要です。
**オンプレミスの Hyper-V VM** | レプリケートする VM では、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)を実行し、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠する必要があります。 レプリケーションが有効になった後、VM の名前を変更できます。 




## <a name="next-steps"></a>次のステップ

[手順 3: 容量を計画する](vmm-to-azure-walkthrough-capacity.md)方法に関するページに進む
