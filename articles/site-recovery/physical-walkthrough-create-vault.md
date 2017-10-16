---
title: "Azure Site Recovery を使用して Azure に物理サーバーをレプリケートするためのコンテナーを設定する | Microsoft Docs"
description: "Azure Site Recovery を使用して Azure に物理サーバーをレプリケートするためのコンテナーを設定するために必要な手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>手順 6: Azure に物理サーバーをレプリケートするためのコンテナーを設定する


この記事では、コンテナーの設定方法について説明します。 Azure ポータルでコンテナーを作成し、[Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの場所から Azure にレプリケートする対象を指定します。


コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。




## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[インフラストラクチャを準備する]** > **[保護の目標]** をクリックします。
3. **[保護の目標]** で、**[To Azure]\(Azure へ\)** > **[非仮想化/その他]** を選択します。


## <a name="next-steps"></a>次のステップ

「[手順 7: ソースとターゲットを設定する](physical-walkthrough-source-target.md)」に進みます。
