---
title: "Azure Site Recovery を使用した Azure への Hyper-V レプリケーション (System Center VMM なし) の前提条件を確認する | Microsoft Docs"
description: "Azure Site Recovery による Azure へのオンプレミスの Hyper-V VM のレプリケーション、フェールオーバー、リカバリーのセットアップの前提条件について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: cbb5d3598ef91512991d7d1e9f854eb12980752b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>手順 2: Azure への Hyper-V (VMM なし) のレプリケーションの前提条件を確認する

前提条件を表にまとめます。


**前提条件** | **詳細** 
--- | --- 
**Azure** | [Azure の要件](site-recovery-prereq.md#azure-requirements)を確認します。
**オンプレミスのサーバー** | オンプレミスの Hyper-V ホストの要件の[詳細を確認](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm)します。
**オンプレミスの Hyper-V VM** | レプリケートする VM では、[サポートされるオペレーティング システム](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions)を実行し、[Azure の前提条件](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements)に準拠する必要があります。
**Azure の URL** | Hyper-V ホストには、これらの URL へのアクセス権が必要です。<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。<br/></br> [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。<br/></br> ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。



## <a name="next-steps"></a>次のステップ

- 完全なデプロイを行う場合は、「[手順 3: 容量を計画する](hyper-v-site-walkthrough-capacity.md)」に進みます。
- 簡単なテスト配置を行う場合は、「[手順 4: ネットワークを計画する](hyper-v-site-walkthrough-network.md)」に進みます。
