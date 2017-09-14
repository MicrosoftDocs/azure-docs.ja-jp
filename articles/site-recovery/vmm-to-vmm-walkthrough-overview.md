---
title: "Azure Site Recovery で Hyper-V VM をセカンダリ VMM サイトにレプリケートする | Microsoft Docs"
description: "Azure Portal を使用したセカンダリ VMM サイトへの Hyper-V VM のレプリケーションの概要を示します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>VMM クラウド内の Hyper-V 仮想マシンをセカンダリ VMM サイトにレプリケートする

> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-vmm.md)
> * [クラシック ポータル](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell - Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

この記事では、System Center Virtual Machine Manager (VMM) クラウドで管理されているオンプレミスの Hyper-V 仮想マシン (VM) を、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) を使用して、セカンダリ VMM の場所にレプリケートする場合に必要な手順の概要を示します。

この記事に関するコメントは、この記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)で投稿してください。


## <a name="step-1-review-the-scenario-architecture"></a>手順 1: シナリオのアーキテクチャを確認する

デプロイを開始する前に、シナリオのアーキテクチャを確認し、デプロイする必要があるすべてのコンポーネントを理解していることを確認します。

[手順 1: アーキテクチャを確認する](vmm-to-vmm-walkthrough-architecture.md)方法に関するページに進みます。

## <a name="step-2-review-prerequisites-and-limitations"></a>手順 2: 前提条件と制限事項を確認する

デプロイの前提条件と制限事項を理解していることを確認します。

**Azure の前提条件**: レプリケーションを調整して管理するには、Microsoft Azure サブスクリプション、および Azure Recovery Services コンテナーが必要です。
**オンプレミスの VMM サーバーおよび Hyper-V ホスト**: VMM サーバーと Hyper-V ホストが Site Recovery のデプロイ用に対応しており、準備されていることを確認します。

[手順 2: 前提条件と制限事項を確認する](vmm-to-vmm-walkthrough-prerequisites.md)方法に関するページに進みます。

## <a name="step-3-plan-networking"></a>手順 3: ネットワークを計画する

シナリオのデプロイ時に VMM VM ネットワーク間のネットワーク マッピングを構成できることを確認するために、いくつかのネットワーク計画が必要になります。

[手順 3: ネットワークを計画する](vmm-to-vmm-walkthrough-network.md)方法に関するページに進みます。


## <a name="step-4-prepare-vmm-and-hyper-v"></a>手順 4: VMM と Hyper-V を準備する

Site Recovery のデプロイ用に VMM サーバーと Hyper-V ホストを準備します。

[手順 4: オンプレミス サーバーを準備する](vmm-to-vmm-walkthrough-vmm-hyper-v.md)方法に関するページに進みます。

## <a name="step-5-set-up-a-vault"></a>手順 5: コンテナーをセットアップする

Recovery Services コンテナーをセットアップします。 このコンテナーには構成設定が含まれ、レプリケーションを調整します。

[手順 5: コンテナーをセットアップする](vmm-to-vmm-walkthrough-create-vault.md)方法に関するページに進みます。

## <a name="step-6-set-up-source-and-target-settings"></a>手順 6: ソースとターゲットの設定をセットアップする

ソースとターゲットのレプリケーション VMM の場所をセットアップします。 VMM サーバーをコンテナーに追加し、Site Recovery コンポーネントのインストール ファイルをダウンロードします。 VMM サーバーで Azure Site Recovery Provider のセットアップを実行します。 セットアップで Provider が VMM サーバーにインストールされ、サーバーがコンテナーに登録されます。 各 Hyper-V ホストには、Microsoft Azure Recovery Services エージェントをインストールします。

[手順 6: ソースとターゲットの設定をセットアップする](vmm-to-vmm-walkthrough-source-target.md)方法に関するページに進みます。

## <a name="step-7-configure-network-mapping"></a>ステップ 7: ネットワーク マッピングを構成する

ソースとターゲットの場所に VMM VM ネットワークをマップします。 フェールオーバー後、VM はターゲット ネットワークに作成され、このネットワークは、ソース Hyper-V VM が配置されるソース VM ネットワークにマップされます。

[手順 7: ネットワーク マッピングを構成する](vmm-to-vmm-walkthrough-network-mapping.md)方法に関するページに進みます。


## <a name="step-8-set-up-a-replication-policy"></a>手順 8: レプリケーション ポリシーを設定する

VMM の場所の間で VM をレプリケートする方法を指定します。

[手順 8: レプリケーション ポリシーをセットアップする](vmm-to-vmm-walkthrough-replication.md)方法に関するページに進みます。


## <a name="step-9-enable-replication-for-vms"></a>手順 9: VM のレプリケーションを有効にする

レプリケートする VM を選択します。 VM をレプリケーションできるようにすると、セカンダリ サイトへの最初のレプリケーションがトリガーされ、その後に実行中の差分レプリケーションが続きます。

[手順 9: レプリケーションを有効にする](vmm-to-vmm-walkthrough-enable-replication.md)方法に関するページに進みます。


## <a name="step-10-run-a-test-failover"></a>手順 10: テスト フェールオーバーを実行する

テスト フェールオーバーを実行して、すべて想定どおりに動作していることを確認します。

[手順 10: テスト フェールオーバーを実行する](vmm-to-vmm-walkthrough-test-failover.md)方法に関するページに進みます。

