---
title: "Azure Site Recovery を使用した Azure への VMware レプリケーション用のコンテナーを設定する | Microsoft Docs"
description: "Azure Site Recovery を使用した Azure への VMware レプリケーション用のコンテナーを設定するために必要な手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8bce940e-f19f-4418-8360-aee7b073519a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: dca95ad46b8de587140c3573ba6ed5702a122032
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="step-7-set-up-a-vault-for-vmware-replication-to-azure"></a>手順 7: Azure への VMware レプリケーション用のコンテナーを設定する


この記事では、コンテナーを設定し、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの場所から Azure にレプリケートする対象を指定する方法について説明します。


コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。




## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>保護の目標を選択する

レプリケートの対象とレプリケート先を選択します。

1. **[Recovery Services コンテナー]** > [コンテナー] の順にクリックします。
2. リソース メニューで、**[Site Recovery]** > **[インフラストラクチャの準備]** > **[保護の目標]** の順にクリックします。
3. **[保護の目標]** で、**[To Azure (Azure へ)]** > **[Yes, with VMware vSphere Hypervisor (はい、VMware vSphere ハイパーバイザーを使用する)]** の順に選択します。



## <a name="next-steps"></a>次のステップ

[手順 8: ソースとターゲットを設定する](vmware-walkthrough-source-target.md)方法に関するページに進む

