---
title: "Azure Site Recovery を使用して Hyper-V VM を Azure にレプリケートする | Microsoft Docs"
description: "オンプレミス Hyper-V VM の Azure へのレプリケーション、フェールオーバー、および復旧を調整する方法を説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>Hyper-V 仮想マシン (VMM なし) を Azure にレプリケートする 

> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-hyper-v-site-to-azure.md)
> * [Azure クラシック](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell - Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

この記事では、オンプレミスの Hyper-V 仮想マシンを、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用して Azure にレプリケートするために必要な手順の概要を説明します。 このデプロイでは、Hyper-V VM は System Center Virtual Machine Manager (VMM) によって管理されません。


コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="step-1-review-architecture-and-prerequisites"></a>手順 1: アーキテクチャと前提条件を確認する

デプロイを開始する前に、シナリオのアーキテクチャを確認し、デプロイする必要があるすべてのコンポーネントを理解していることを確認します。

[手順 1: アーキテクチャを確認する](hyper-v-site-walkthrough-architecture.md)方法に関するページに進む


## <a name="step-2-review-prerequisites"></a>手順 2: 前提条件を確認する

各デプロイ コンポーネントの前提条件が満たされていることを確認します。

- **Azure の前提条件**: Microsoft Azure アカウント、Azure ネットワーク、およびストレージ アカウントが必要です。
- **オンプレミスの Hyper-V の前提条件**: Hyper-V ホストが Site Recovery デプロイ用に準備されていることを確認します。
- **レプリケートされた VM**: レプリケートする VM は Azure 要件に準拠する必要があります。

[手順 2: 前提条件と制限事項を確認する](hyper-v-site-walkthrough-prerequisites.md)方法に関するページに進む

## <a name="step-3-plan-capacity"></a>手順3: 容量を計画する

完全なデプロイを行う場合は、必要なレプリケーション リソースを把握する必要があります。 これに役立つツールがいくつかあります。 手順 2. に進みます。 環境をテストするための簡単なセットアップを行っている場合は、この手順をスキップできます。

[手順 3: 容量を計画する](hyper-v-site-walkthrough-capacity.md)方法に関するページに進む

## <a name="step-4-plan-networking"></a>手順 4: ネットワークを計画する

フェールオーバーの発生後に Azure VM がネットワークに接続され、正しい IP アドレスが割り当てられるようにするには、ネットワークの計画を行う必要があります。

[手順 4: ネットワークを計画する](hyper-v-site-walkthrough-network.md)方法に関するページに進む

##  <a name="step-5-prepare-azure-resources"></a>手順 5: Azure リソースを準備する

開始する前に、Azure ネットワークとストレージを設定します。 この操作はデプロイ中に実行できますが、開始する前に行うことをお勧めします。

[手順 5: Azure を準備する](hyper-v-site-walkthrough-prepare-azure.md)方法に関するページに進む


## <a name="step-6-prepare-hyper-v"></a>手順 6: Hyper-V を準備する

Hyper-V サーバーが Site Recovery のデプロイ要件を満たしていることを確認します。

[手順 6: Hyper-V を準備する](hyper-v-site-walkthrough-prepare-hyper-v.md)方法に関するページに進む

## <a name="step-7-set-up-a-vault"></a>手順 7: コンテナーを設定する

レプリケーションを調整および管理するには、Recovery Services コンテナーを設定する必要があります。 コンテナーを設定するときは、レプリケートする対象とレプリケート先を選択します。

[手順 7: コンテナーを作成する](hyper-v-site-walkthrough-create-vault.md)方法に関するページに進む

## <a name="step-8-configure-source-and-target-settings"></a>手順 8: ソースとターゲットの設定を構成する

レプリケーションに使用するソースとターゲットを設定します。 ソース設定のセットアップには、Hyper-V サイトへの Hyper-V ホストの追加、各 Hyper-V ホストへのSite Recovery Provider と Recovery Services エージェントのインストール、Recovery Services コンテナーへのサイトの登録が含まれます。

[手順 8: ソースとターゲットを設定する](hyper-v-site-walkthrough-source-target.md)方法に関するページに進む

## <a name="step-9-set-up-a-replication-policy"></a>手順 9: レプリケーション ポリシーを設定する

コンテナー内の Hyper-V VM のレプリケーション設定を指定するポリシーを設定します。

[手順 9: レプリケーション ポリシーを設定する](hyper-v-site-walkthrough-replication.md)方法に関するページに進む


## <a name="step-10-enable-replication"></a>手順 10: レプリケーションを有効にする

レプリケーション ポリシーを設定して有効にした後、VM の初期レプリケーションが発生します。

[手順 10: レプリケーションを有効にする](hyper-v-site-walkthrough-enable-replication.md)方法に関するページに進む

## <a name="step-11-run-a-test-failover"></a>手順 11: テスト フェールオーバーを実行する

初期レプリケーションが完了した後で差分レプリケーションが実行されているときに、テスト フェールオーバーを実行して、すべてが予定どおりに動作することを確認できます。

[手順 11: テスト フェールオーバーを実行する](hyper-v-site-walkthrough-test-failover.md)方法に関するページに進む
