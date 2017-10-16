---
title: "Azure Site Recovery を使用して VMware VM を Azure にレプリケートする | Microsoft Docs"
description: "VMware VM 上で実行されているワークロードを Azure にレプリケートするための手順の概要を示します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>Site Recovery を使用して VMware VM を Azure にレプリケートする

この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して、オンプレミスの VMware 仮想マシンを Azure にレプリケートするために必要な手順の概要を示します。


![デプロイ プロセス](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**図 1: デプロイ プロセスの概要**

## <a name="step-1-review-architecture-and-prerequisites"></a>手順 1: アーキテクチャと前提条件を確認する

デプロイを開始する前に、シナリオのアーキテクチャを確認し、デプロイする必要があるすべてのコンポーネントを理解していることを確認します。

[手順 1: アーキテクチャを確認する](vmware-walkthrough-architecture.md)方法に関するページに進む


## <a name="step-2-review-prerequisites"></a>手順 2: 前提条件を確認する

各デプロイ コンポーネントの前提条件が満たされていることを確認します。

- **Azure の前提条件**: Microsoft Azure アカウント、Azure ネットワーク、ストレージ アカウントが必要です。
- **オンプレミスの Site Recovery コンポーネント**: オンプレミスの Site Recovery コンポーネントを実行しているマシンが必要です。
- **オンプレミスの VMware の前提条件**: Site Recovery が VMware サーバーおよび VM にアクセスできるようにアカウントを設定する必要があります。
- **レプリケーション対象の VM**: レプリケートする VM は、Azure の要件に準拠しており、モビリティ サービス コンポーネントがインストールされている必要があります。

[手順 2: 前提条件と制限事項を確認する](vmware-walkthrough-prerequisites.md)方法に関するページに進む

## <a name="step-3-plan-capacity"></a>手順 3: 容量を計画する

完全なデプロイを行う場合は、必要なレプリケーション リソースを把握する必要があります。 これに役立つツールがいくつかあります。 手順 2. に進みます。 環境をテストするための簡単なセットアップを行っている場合は、この手順をスキップできます。

[手順 3: 容量を計画する](vmware-walkthrough-capacity.md)方法に関するページに進む

## <a name="step-4-plan-networking"></a>手順 4: ネットワークを計画する

フェールオーバーの発生後に Azure VM がネットワークに接続され、正しい IP アドレスが割り当てられるようにするには、ネットワークの計画を行う必要があります。

[手順 4: ネットワークを計画する](vmware-walkthrough-network.md)方法に関するページに進む

##  <a name="step-5-prepare-azure-resources"></a>手順 5: Azure リソースを準備する

開始する前に、Azure ネットワークとストレージを設定します。 この操作はデプロイ中に実行できますが、開始する前に行うことをお勧めします。

[手順 5: Azure を準備する](vmware-walkthrough-prepare-azure.md)方法に関するページに進む


## <a name="step-6-prepare-vmware"></a>手順 6: VMware を準備する

Site Recovery で次の操作に使用するアカウントを設定する必要があります。

- VMware 仮想化サーバーにアクセスして、VM を自動的に検出する。
- VM にアクセスして、モビリティ サービスをインストールする。 レプリケートする各 VM でモビリティ サービス エージェントをインストールしないと、VM のレプリケーションを有効にすることはできません。

[手順 6: VMware を準備する](vmware-walkthrough-prepare-vmware.md)方法に関するページに進む

## <a name="step-7-set-up-a-vault"></a>手順 7: コンテナーを設定する

レプリケーションを調整および管理するには、Recovery Services コンテナーを設定する必要があります。 コンテナーを設定するときは、レプリケートする対象とレプリケート先を選択します。

[手順 7: コンテナーを設定する](vmware-walkthrough-create-vault.md)方法に関するページに進む

## <a name="step-8-configure-source-and-target-settings"></a>手順 8: ソースとターゲットの設定を構成する

レプリケーションに使用するソースとターゲットを設定します。 ソースの設定には、統合セットアップを実行してオンプレミスの Site Recovery コンポーネントをインストールする操作が含まれます。

[手順 8: ソースとターゲットを設定する](vmware-walkthrough-source-target.md)方法に関するページに進む

## <a name="step-9-set-up-a-replication-policy"></a>手順 9: レプリケーション ポリシーを設定する

コンテナー内の VMware VM のレプリケーション設定を指定するポリシーを設定します。

[手順 9: レプリケーション ポリシーを設定する](vmware-walkthrough-replication.md)方法に関するページに進む

## <a name="step-10-install-the-mobility-service"></a>手順 10: モビリティ サービスをインストールする

モビリティ サービスは、レプリケートする各 VM にインストールする必要があります。 プッシュ インストールまたはプル インストールを使用してサービスを設定する方法がいくつかあります。

「[手順 10: モビリティ サービスをインストールする](vmware-walkthrough-install-mobility.md)」に進む

## <a name="step-11-enable-replication"></a>手順 11: レプリケーションを有効にする

VM 上でモビリティ サービスが実行されるようになったら、その VM のレプリケーションを有効にすることができます。 有効にすると、VM の初期レプリケーションが行われます。

[手順 11: レプリケーションを有効にする](vmware-walkthrough-enable-replication.md)方法に関するページに進む

## <a name="step-12-run-a-test-failover"></a>手順 12: テスト フェールオーバーを実行する

初期レプリケーションが完了した後で差分レプリケーションが実行されているときに、テスト フェールオーバーを実行して、すべてが予期したとおりに動作することを確認できます。

[手順 12: テスト フェールオーバーを実行する](vmware-walkthrough-test-failover.md)方法に関するページに進む
