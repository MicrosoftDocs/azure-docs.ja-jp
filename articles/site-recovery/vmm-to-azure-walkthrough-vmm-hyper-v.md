---
title: "Azure に Hyper-V をレプリケートできるように System Center VMM を準備する | Microsoft Docs"
description: "Azure Site Recovery を使用して Azure に Hyper-V をレプリケートするために System Center VMM サーバーを準備する方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---

# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>手順 6: Azure に Hyper-V をレプリケートできるように VMM サーバーと Hyper-V ホストを準備する

デプロイの [Azure コンポーネント](vmm-to-azure-walkthrough-prepare-azure.md)を設定したら、この記事の手順に従って、Azure Site Recovery と対話するようオンプレミスの VMM サーバーと Hyper-V ホストを準備します。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="prepare-vmm-servers"></a>VMM サーバーを準備する

- Site Recovery レプリケーションのサポート要件 (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers) を満たす VMM サーバーが少なくとも 1 つ必要です。
- VMM サーバーで[ネットワーク マッピング](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure)の準備ができていることを確認します。
- VMM サーバーが次の URL にアクセスできることを確認します。

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- IP アドレスベースのファイアウォール規則を使用している場合、その規則で Azure との通信が許可されていることを確認します。
- [Azure データセンターの IP の範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)と HTTPS (443) ポートを許可します。
- ご利用のサブスクリプションの Azure リージョンと米国西部の IP アドレス範囲を許可します (Access Control と ID 管理に使用されます)。

Site Recovery をデプロイする際に、Site Recovery プロバイダーをダウンロードして各 VMM サーバーにインストールします。 VMM サーバーが Recovery Services コンテナーに登録されます。




## <a name="next-steps"></a>次のステップ

[手順 7: コンテナーを作成する](vmm-to-azure-walkthrough-create-vault.md)方法に関するページに進む


