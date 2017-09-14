---
title: "Azure Site Recovery を使用してセカンダリ サイトへの Hyper-V レプリケーション用のコンテナーを作成する | Microsoft Docs"
description: "Azure Site Recovery を使用して Hyper-V VM をセカンダリ System Center VMM サイトにレプリケートする場合のコンテナーの作成方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>手順 5: セカンダリ サイトへの Hyper-V のレプリケーション用のコンテナーを作成する

[Azure Site Recovery](site-recovery-overview.md) を使用し、セカンダリ サイトへの HYPER-V レプリケーション用のオンプレミスの [System Center Virtual Machine Manager (VMM) サーバーと HYPER-V ホスト/クラスター](vmm-to-vmm-walkthrough-vmm-hyper-v.md)を準備した後で、リカバリ サービス コンテナーを作成し、レプリケーションのシナリオを選択することができます。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Site Recovery]** > **[手順 1: インフラストラクチャを準備する]** > **[保護の目標]** の順にクリックします。
2. **[復旧サイトへ]** を選択し、**[Yes, with Hyper-V (はい、Hyper-V を使用します)]** を選択します。
3. **[はい]** を選択し、VMM を使用して Hyper-V ホストを管理することを指定します。
4. セカンダリ VMM サーバーがある場合は、**[はい]** を選択します。 単一の VMM サーバー上にあるクラウド間でレプリケーションをデプロイする場合は、**[いいえ]** をクリックします。 次に、 **[OK]**をクリックします

    ![Choose goals](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>次のステップ

「[手順 6: レプリケーションのソースとターゲットをセットアップする](vmm-to-vmm-walkthrough-source-target.md)」に進みます。
