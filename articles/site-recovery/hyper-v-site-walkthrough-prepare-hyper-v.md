---
title: "Azure にレプリケートできるように Hyper-V ホスト (System Center VMM なし) を準備する | Microsoft Docs"
description: "Azure Site Recovery を使用して Azure にレプリケートするために Hyper-V ホストを準備する方法について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>手順 6: Azure へのレプリケーションのために Hyper-V ホストを準備する

この記事の手順を使って、Azure Site Recovery と対話するようにオンプレミスの Hyper-V ホストを準備します。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="prepare-hosts"></a>ホストを準備する

- Hyper-V ホストが[前提条件](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm)を満たしていることを確認します。
- ホストが、必要な URL にアクセスできることを確認します。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。
- [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
- ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。

Site Recovery のデプロイ中に、Hyper-V サイトにレプリケートする VM が含まれる Hyper-V ホストを追加します。 Site Recovery Provider と Recovery Services エージェントが各ホストにインストールされます。 Hyper-V サイトが Recovery Services コンテナーに登録されます。

## <a name="next-steps"></a>次のステップ

[手順 7: コンテナーを作成する](hyper-v-site-walkthrough-create-vault.md)方法に関するページに進む

