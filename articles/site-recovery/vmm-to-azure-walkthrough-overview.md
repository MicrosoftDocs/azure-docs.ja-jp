---
title: "Azure Site Recovery を使用して VMM クラウド内の Hyper-V VM を Azure にレプリケートする | Microsoft Docs"
description: "Azure Site Recovery サービスを使用して VMM クラウド内の Hyper-V VM を Azure にレプリケートする方法の概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Azure Portal の Site Recovery を使用して VMM クラウド内の Hyper-V 仮想マシンを Azure にレプリケートする
> [!div class="op_single_selector"]
> * [Azure ポータル](site-recovery-vmm-to-azure.md)
> * [Azure クラシック](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [PowerShell クラシック](site-recovery-deploy-with-powershell.md)


この記事では、System Center Virtual Machine Manager (VMM) クラウドで管理されているオンプレミスの Hyper-V 仮想マシン (VM) を、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure にレプリケートする場合に必要な手順の概要を示します。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## <a name="step-1-review-the-scenario-architecture"></a>手順 1: シナリオのアーキテクチャを確認する

デプロイを開始する前に、シナリオのアーキテクチャを確認し、デプロイする必要があるすべてのコンポーネントを理解していることを確認します。

[手順 1: アーキテクチャを確認する](vmm-to-azure-walkthrough-architecture.md)方法に関するページに進む

## <a name="step-2-review-prerequisites-and-limitations"></a>手順 2: 前提条件と制限事項を確認する

デプロイの前提条件と制限事項を理解していることを確認します。

**Azure の前提条件**: Microsoft Azure アカウント、Azure ネットワーク、ストレージ アカウントが必要です。
**オンプレミスの VMM サーバーおよび Hyper-V ホスト**: VMM サーバーと Hyper-V ホストが Site Recovery のデプロイ用に対応しており、準備されていることを確認します。
**レプリケートされた VM**: レプリケートする VM が Azure の要件に準拠していることを確認します。

「[手順 2: 前提条件と制限事項を確認する](vmm-to-azure-walkthrough-prerequisites.md)」に進みます。

## <a name="step-3-plan-capacity"></a>手順 3: 容量を計画する

完全なデプロイを行う場合は、必要なレプリケーション リソースを把握する必要があります。 これに役立つツールがいくつかあります。 環境をテストするためのクイック セットアップを行っている場合は、この手順をスキップできます。

[手順 3: 容量を計画する](vmm-to-azure-walkthrough-capacity.md)方法に関するページに進む

## <a name="step-4-plan-networking"></a>手順 4: ネットワークを計画する

ネットワークの計画を行って、シナリオをデプロイするときにネットワーク マッピングを構成できるようにし、フェールオーバーが発生したときに Azure VM が Azure 仮想ネットワークに接続して、これらが適切な IP アドレスに割り当てられるようにする必要があります。

[手順 4: ネットワークを計画する](vmm-to-azure-walkthrough-network.md)方法に関するページに進む


## <a name="step-5-prepare-azure-resources"></a>手順 5: Azure リソースを準備する

Azure アカウント、ネットワーク、ストレージを設定します。 この操作はデプロイ中に実行できますが、開始する前に行うことをお勧めします。

[手順 5: Azure を準備する](vmm-to-azure-walkthrough-prepare-azure.md)方法に関するページに進む

## <a name="step-6-prepare-vmm-and-hyper-v"></a>手順 6: VMM と Hyper-V を準備する

Site Recovery のデプロイ用にオンプレミスの VMM サーバーと Hyper-V ホストを準備します。

[手順 6: オンプレミスのサーバーを準備する](vmm-to-azure-walkthrough-vmm-hyper-v.md)方法に関するページに進む

## <a name="step-7-set-up-a-vault"></a>手順 7: コンテナーを設定する

Recovery Services コンテナーを設定します。 このコンテナーには構成設定が含まれ、レプリケーションを調整します。

[手順 7: コンテナーを設定する](vmm-to-azure-walkthrough-create-vault.md)方法に関するページに進む

## <a name="step-8-configure-source-and-target-settings"></a>手順 8: ソースとターゲットの設定を構成する

レプリケーションに使用するソースとターゲットの場所を設定します。 VMM サーバーをコンテナーに追加し、Site Recovery コンポーネントのインストール ファイルをダウンロードします。 VMM サーバーで Azure Site Recovery プロバイダーのセットアップを実行します。 プロバイダーが VMM サーバーにインストールされ、サーバーがコンテナーに登録されます。 各 Hyper-V ホストには、Azure Recovery Services エージェントをインストールします。

[手順 8: ソースとターゲットの設定を構成する](vmm-to-azure-walkthrough-source-target.md)方法に関するページに進む

## <a name="step-9-configure-network-mapping"></a>手順 9: ネットワーク マッピングを構成する

オンプレミスの VMM VM ネットワークを Azure 仮想ネットワークにマップします。 フェールオーバー後、Azure VM は Azure ネットワークに作成され、このネットワークは、ソース Hyper-V が配置されるオンプレミスの VM ネットワークにマップされます。

[手順 9: ネットワーク マッピングを構成する](vmm-to-azure-walkthrough-network-mapping.md)方法に関するページに進む


## <a name="step-10-set-up-a-replication-policy"></a>手順 10: レプリケーション ポリシーを設定する

オンプレミスの VM を Azure にレプリケートする方法を指定します。

[手順 10: レプリケーション ポリシーを設定する](vmm-to-azure-walkthrough-replication.md)方法に関するページに進む


## <a name="step-11-enable-replication-for-vms"></a>手順 11: VM のレプリケーションを有効にする

レプリケーションする VM を選択します。 VM をレプリケーションできるようにすると、Azure への初期レプリケーションがトリガーされ、その後に実行中の差分レプリケーションが続きます。

[手順 11: レプリケーションを有効にする](vmm-to-azure-walkthrough-enable-replication.md)方法に関するページに進む


## <a name="step-12-run-a-test-failover"></a>手順 12: テスト フェールオーバーを実行する

テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。

[手順 12: テスト フェールオーバーを実行する](vmm-to-azure-walkthrough-test-failover.md)方法に関するページに進む


