---
title: "Azure Site Recovery を使用した Azure への Hyper-V レプリケーション (System Center VMM なし) 用のコンテナーを設定する | Microsoft Docs"
description: "Azure Site Recovery を使用した Azure への Hyper-V レプリケーション用のコンテナーを設定するために必要な手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>手順 7: Hyper-V レプリケーション用のコンテナーを設定する

この記事では、コンテナーを設定する方法と、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの場所から Azure にレプリケートする対象を指定する方法について説明します。


コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[インフラストラクチャの準備]** > **[保護の目標]** の順にクリックします。
3. **[保護の目標]** で、**[Azure へ]** > **[Yes, with Hyper-V]\(はい、Hyper-V を使用する\)** を選択します。 VMM を使用していないことを確認するために、 **[いいえ]** を選択します。 

    ![Choose goals](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>次のステップ

[手順 8: ソースとターゲットを設定する](hyper-v-site-walkthrough-source-target.md)方法に関するページに進む
