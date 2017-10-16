---
title: "Azure Site Recovery を使用してリージョン間で Azure VM レプリケーションのコンテナーを設定する |Microsoft ドキュメント"
description: "Azure Site Recovery を使用して Azure リージョン間に Azure レプリケーションのコンテナーを設定するために必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 40472189-3d80-4963-b175-8bddcbc2f61f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: e03d17992ee0b12049636e40188950bcc4a6f31e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-4-set-up-a-vault-for-azure-to-azure-replication"></a>手順 4: Azure レプリケーション用に Azure のコンテナーを設定する

[ネットワークを計画した](azure-to-azure-walkthrough-network.md)後、この記事に従って、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、他の Azure リージョンへレプリケートしている Azure 仮想マシン (VM) に対してコンテナーを設定します。

- 記事の手順を完了すると、Recovery Services のコンテナーが設定されています。
- この記事の末尾にあるコメント欄でご意見をお送りください。または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) に質問を投稿してください。



>[!NOTE]
>
> Azure VM レプリケーションは、現在プレビューの段階です。




## <a name="create-a-vault"></a>コンテナーの作成

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> VM をレプリケートする場所に Recovery Services コンテナーを作成することをお勧めします。 たとえば、ターゲットの場所が米国中部の場合は、**米国中部**にコンテナーを作成します。


## <a name="next-steps"></a>次のステップ

「[手順 5: レプリケーションを有効にする](azure-to-azure-walkthrough-enable-replication.md)」に進む
