---
title: "Azure リージョン間で Azure VM をレプリケートする | Microsoft Docs"
description: "Azure Portal の Azure Site Recovery サービスを使用して、Azure リージョン間で Azure VM のレプリケートに必要な手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.contentlocale: ja-jp
ms.lasthandoff: 08/02/2017

---

# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Azure Site Recovery を使用したリージョン間での Azure VM のレプリケーション

>この記事では、1 つの Azure リージョンにある Azure 仮想マシン (VM) を別のリージョンにある Azure VM にレプリケートするために必要な手順の概要を示します。 

>[!NOTE]
>
> Azure VM レプリケーションは、現在プレビューの段階です。

コメントや質問は、この記事の末尾または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="step-1-review-architecture"></a>手順 1: アーキテクチャを確認する

デプロイを開始する前に、シナリオのアーキテクチャと、デプロイする必要があるコンポーネントを確認します。

[手順 1: アーキテクチャを確認する](azure-to-azure-walkthrough-architecture.md)方法に関するページに進む


## <a name="step-2-review-prerequisites"></a>手順 2: 前提条件を確認する

サブスクリプション、仮想ネットワーク、ストレージ アカウント、および VM の要件など、要点となる Azure の前提条件を確認してください。

「[手順 2: 前提条件と制限事項を確認する](azure-to-azure-walkthrough-prerequisites.md)」に進みます。


## <a name="step-3-plan-networking"></a>手順3: ネットワークを計画する

Azure VM に送信接続が設定されていること、オンプレミスからの接続が設定されていることを確認します。

[手順 4: ネットワークを計画する](azure-to-azure-walkthrough-network.md)方法に関するページに進む



## <a name="step-4-create-a-vault"></a>手順 4: コンテナーを作成する 

レプリケーションを調整および管理し、ソース リージョンを指定するには、Recovery Services コンテナーを設定する必要があります。

「[手順 4: コンテナーを作成する](azure-to-azure-walkthrough-vault.md)」にページに進む


## <a name="step-5-enable-replication"></a>手順 5: レプリケーションを有効にする


レプリケーションを有効にするには、ターゲットの場所の設定を構成し、レプリケーション ポリシーを設定して、レプリケートする Azure VM を選択します。 有効にすると、VM の初期レプリケーションが行われます。

「[手順 5: レプリケーションを有効にする](azure-to-azure-walkthrough-enable-replication.md)」に進む


## <a name="step-6-run-a-test-failover"></a>手順 6: テスト フェールオーバーを実行する

初期レプリケーションが完了した後で差分レプリケーションが実行されているときに、テスト フェールオーバーを実行して、すべてが予期したとおりに動作することを確認できます。

「[手順 6: テスト フェールオーバーを実行する](azure-to-azure-walkthrough-test-failover.md)」に進む



