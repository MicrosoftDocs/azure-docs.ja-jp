---
title: "Azure Site Recovery を使用してオンプレミスの物理サーバーを Azure にレプリケートする | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して、オンプレミスの Windows/Linux 物理サーバー上で実行されているワークロードを Azure にレプリケートする手順の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017


---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Site Recovery を使用して物理サーバーを Azure にレプリケートする

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの Windows/Linux 物理サーバーを Azure にレプリケートするために必要な手順の概要を示します。


## <a name="step-1-review-architecture-and-prerequisites"></a>手順 1: アーキテクチャと前提条件を確認する

デプロイを開始する前に、シナリオのアーキテクチャを確認し、デプロイを設定するために必要なすべてのコンポーネントを理解していることを確認します。

[手順 1: アーキテクチャを確認する](physical-walkthrough-architecture.md)方法に関するページに進む


## <a name="step-2-review-prerequisites"></a>手順 2: 前提条件を確認する

各デプロイ コンポーネントの前提条件が満たされていることを確認します。

- **Azure の前提条件**: Microsoft Azure アカウント、Azure ネットワーク、ストレージ アカウントが必要です。
- **オンプレミスの Site Recovery コンポーネント**: オンプレミスの Site Recovery コンポーネントを実行しているマシンが必要です。
- **レプリケート対象のマシン**: レプリケートするサーバーは、オンプレミスと Azure の要件を準拠している必要があります。

[手順 2: 前提条件と制限事項を確認する](physical-walkthrough-prerequisites.md)方法に関するページに進む

## <a name="step-3-plan-capacity"></a>手順 3: 容量を計画する

完全なデプロイを行う場合は、必要なレプリケーション リソースを把握する必要があります。 環境をテストするためのクイック セットアップを行っている場合は、この手順をスキップできます。

[手順 3: 容量を計画する](physical-walkthrough-capacity.md)方法に関するページに進む

## <a name="step-4-plan-networking"></a>手順 4: ネットワークを計画する

フェールオーバーの発生後に Azure VM がネットワークに接続され、正しい IP アドレスが割り当てられるようにするには、ネットワークの計画を行う必要があります。

[手順 4: ネットワークを計画する](physical-walkthrough-network.md)方法に関するページに進む

##  <a name="step-5-prepare-azure-resources"></a>手順 5: Azure リソースを準備する

開始する前に、Azure ネットワークとストレージを設定します。 

[手順 5: Azure を準備する](physical-walkthrough-prepare-azure.md)方法に関するページに進む


## <a name="step-6-set-up-a-vault"></a>手順 6: コンテナーを設定する

レプリケーションを調整および管理するには、Recovery Services コンテナーを設定します。 コンテナーを設定するときは、レプリケートする対象とレプリケート先を選択します。

[手順 6: コンテナーを設定する](physical-walkthrough-create-vault.md)方法に関するページに進む

## <a name="step-7-configure-source-and-target-settings"></a>手順 7: ソースとターゲットの設定を構成する

ソースとターゲット (Azure) サイトの設定を構成します。 ソースの設定には、統合セットアップを実行してオンプレミス Site Recovery コンポーネントをインストールする操作が含まれます。

[手順 7: ソースとターゲットを設定する](physical-walkthrough-source-target.md)方法に関するページに進む

## <a name="step-8-set-up-a-replication-policy"></a>手順 8: レプリケーション ポリシーを設定する

物理サーバーのレプリケート方法を指定するポリシーを設定します。

[手順 8: レプリケーション ポリシーを設定する](physical-walkthrough-replication.md)方法に関するページに進む

## <a name="step-9-install-the-mobility-service"></a>手順 9: モビリティ サービスをインストールする

モビリティ サービスは、レプリケートする各サーバーにインストールする必要があります。 プッシュ インストールまたはプル インストールを使用して、いくつかの方法でサービスを設定できます。

[手順 9: モビリティ サービスをインストールする](physical-walkthrough-install-mobility.md)方法に関するページに進む

## <a name="step-10-enable-replication"></a>手順 10: レプリケーションを有効にする

サーバー上でモビリティ サービスを実行した後、そのサーバーのレプリケーションを有効にすることができます。 有効にすると、VM の初期レプリケーションが行われます。

[手順 10: レプリケーションを有効にする](physical-walkthrough-enable-replication.md)方法に関するページに進む

## <a name="step-11-run-a-test-failover"></a>手順 11: テスト フェールオーバーを実行する

初期レプリケーションが完了した後で差分レプリケーションが実行されているときに、テスト フェールオーバーを実行して、すべてが予期したとおりに動作することを確認できます。

[手順 11: テスト フェールオーバーを実行する](physical-walkthrough-test-failover.md)方法に関するページに進む


