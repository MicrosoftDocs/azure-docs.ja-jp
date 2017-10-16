---
title: "ディザスター リカバリーのニーズに応じた Azure リージョン間での Azure VM のレプリケーション: Azure 間 | Microsoft Docs"
description: "ディザスター リカバリーのニーズに応じて、Azure Site Recovery サービスを使用して、Azure リージョン間 (Azure 間) で Azure VM をレプリケートする手順をまとめます。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Azure Site Recovery を使用したリージョン間での Azure VM のレプリケーション

>[!NOTE]
>
> Azure 仮想マシン (VM) の Azure Site Recovery レプリケーションは現在プレビューの段階です。

この記事では、Azure Portal の [Site Recovery](site-recovery-overview.md) サービスを使用して、Azure リージョン間で Azure VM をレプリケートする方法について説明します。

コメントや質問は、この記事の末尾または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="disaster-recovery-in-azure"></a>Azure でのディザスター リカバリー

組み込みの Azure インフラストラクチャ機能は、Azure VM で実行されるワークロードに対する、堅牢で耐障害性のある可用性戦略に役立ちます。 ただし、さまざまな理由で、Azure リージョン間でのディザスター リカバリーを、自身で計画する必要があります。

- ビジネス継続性とディザスター リカバリー (BCDR) 戦略を必要とする特定のアプリおよびワークロードについて、コンプライアンス ガイドラインを満たす必要がある。
- 組み込みの Azure 機能だけでなく、ビジネスの意思決定に基づいて、Azure VM を保護および復旧する機能が必要である。
- ビジネスおよびコンプライアンスのニーズに応じて、運用環境に影響を及ぼさずに、フェールオーバーと回復をテストする必要がある。
- 災害発生時の回復リージョンへのフェールオーバーと、元のソース リージョンへのフェールバックをシームレスに行う必要がある。

こうしたタスクはすべて、Azure 間 VM レプリケーションの Site Recovery を使用して行うことができます。


## <a name="why-use-site-recovery"></a>Site Recovery を使用する理由      

Site Recovery により、リージョン間で Azure VM を簡単にレプリケートできます。

- **自動デプロイ**。 アクティブ/アクティブ レプリケーション モデルとは異なり、セカンダリ リージョンに複雑でコストのかかるインフラストラクチャが不要です。 レプリケーションを有効にすると、ソース リージョンの設定に基づいて、ターゲット リージョンに必要なリソースが自動的に作成されます。
- **リージョンのコントロール**。 Site Recovery を使用すると、大陸内の任意のリージョン間でのレプリケーションが可能です。 これを、標準の[ペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)間でのみ非同期的にレプリケーションが行われる、読み取りアクセス geo 冗長ストレージと比較してみてください。 読み取りアクセス geo 冗長ストレージで可能なのは、ターゲット リージョンのデータへの読み取りアクセスだけです。
- **自動レプリケーション**。 継続的な自動レプリケーションがサポートされ、 シングル クリックで、フェールオーバーとフェールバックをトリガーできます。
- **RTO および RPO**。 リージョンに接続されている Azure ネットワーク インフラストラクチャにより、RTO と RPO を非常に低く抑えられます。
- **テスト**。 オンデマンドのテスト フェールオーバーによって、ディザスター リカバリーのテストを、必要に応じて、運用ワークロードや進行中のレプリケーションに影響を与えずに実行できます。
- **復旧計画**。 復旧計画を使用して、複数の VM で実行されているアプリケーション全体のフェールオーバーおよびフェールバックを調整できます。 復旧計画の機能は、Azure Automation Runbook と高度に統合されています。


## <a name="deployment-summary"></a>デプロイの概要

Azure リージョン間での VM レプリケーションのセットアップに必要な操作の概要を次に示します。

1. Recovery Services コンテナーを作成します。 このコンテナーには構成設定が含まれ、レプリケーションを調整します。
2. Azure VM のレプリケーションを有効にします。
3. テスト フェールオーバーを実行して、すべてが想定どおりに動作していることを確認します。

>[!IMPORTANT]
>
> [Azure VM レプリケーションのサポート マトリックス](./site-recovery-support-matrix-azure-to-azure.md)を確認できます。

>[!IMPORTANT]
>
> Site Recovery レプリケーションで Azure VM に必要なネットワーク送信接続を構成する方法については、[ネットワーク ガイダンスのドキュメント](./site-recovery-azure-to-azure-networking-guidance.md)を参照してください。

### <a name="before-you-start"></a>開始する前に

* Azure ユーザー アカウントには、Azure 仮想マシンのレプリケーションを有効にするための特定の[アクセス許可](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)が必要です。
* ディザスター リカバリー リージョンとして使用するターゲットの場所で VM を作成するには、Azure サブスクリプションを有効にする必要があります。 サポートに連絡して、必要なクォータを有効にしてください。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> VM をレプリケートする場所に Recovery Services コンテナーを作成することをお勧めします。 たとえば、ターゲットの場所が米国中部の場合は、**米国中部**にコンテナーを作成します。

## <a name="enable-replication"></a>Enable replication

**[Recovery Services コンテナー]** で、コンテナー名をクリックします。 コンテナーで、**[+ レプリケート]** ボタンをクリックします。

### <a name="step-1-configure-the-source"></a>手順 1. ソースを構成する
1. **[ソース]** で **[Azure - プレビュー]** を選択します。
2. **[ソースの場所]** で、現在 VM が実行されているソースの Azure リージョンを選択します。
3. VM のデプロイ モデル (**[Resource Manager]** または **[クラシック]**) を選択します。
4. Resource Manager VM の場合は**ソース リソース グループ**を、クラシック VM の場合は**クラウド サービス**を選択します。

    ![ソースを構成する](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>手順 2. 仮想マシンの選択

* レプリケートする VM を選択し、**[OK]** をクリックします。

    ![VM を選択する](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>手順 3. 設定を構成する

1. 既定のターゲット設定を上書きして、任意の設定を指定するには、**[カスタマイズ]** をクリックします。 詳細については、[ターゲット リソースのカスタマイズ](site-recovery-replicate-azure-to-azure.md##customize-target-resources)に関するページをご覧ください。

    ![設定を構成する](./media/site-recovery-azure-to-azure/settings.png)


2. 既定では、4 時間ごとにアプリ整合性スナップショットを作成し、復旧ポイントを 24 時間保持するレプリケーション ポリシーが作成されます。 別の設定でポリシーを作成するには、**[レプリケーション ポリシー]** の横にある **[カスタマイズ]** をクリックします。

    ![ポリシーをカスタマイズする](./media/site-recovery-azure-to-azure/customize-policy.png)

3. ターゲット リソースのプロビジョニングを開始するには、**[Create target resources]\(ターゲット リソースを作成\)** をクリックします。 プロビジョニングには 1 分程度かかります。 プロビジョニング中はブレードを閉じないでください。閉じた場合、最初からやり直す必要があります。

4. 選択した VM のレプリケーションをトリガーするには、**[レプリケーションを有効にする]** をクリックします。

5. **保護の有効化**ジョブの進行状況は、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で追跡できます。

6. **[設定]** > **[レプリケートされたアイテム]** では、VM の状態と初期レプリケーションの進行状況を確認できます。 VM の設定をドリルダウンするには、その VM をクリックします。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

すべてのセットアップが完了したら、テスト フェールオーバーを実行して、すべてが想定どおりに動作していることを確認します。

1. 1 つの仮想マシンをフェールオーバーするには、**[設定]** > **[レプリケートされたアイテム]** で、その VM の **[+ テスト フェールオーバー]** アイコンをクリックします。

2. 復旧計画をフェールオーバーする場合は、**[設定]** > **[復旧計画]** で、計画を右クリックし、**[テスト フェールオーバー]** をクリックします。 復旧計画を作成する場合は、[こちらの手順に従ってください](site-recovery-create-recovery-plans.md)。 

3. **[テスト フェールオーバー]** で、フェールオーバー後に Azure VM が接続するターゲットの Azure 仮想ネットワークを選択します。

4. フェールオーバーを開始するには、**[OK]** をクリックします。 進行状況を追跡するには、VM をクリックして、そのプロパティを開きます。 また、**[設定]** > **[ジョブ]** > **[Site Recovery ジョブ]** で、**テスト フェールオーバー** ジョブをクリックすることもできます。

5. フェールオーバーの完了後、レプリカの Azure マシンは、Azure Portal の **[仮想マシン]** に表示されます。 VM が適切なサイズであること、適切なネットワークに接続されていること、実行されていることを確認してください。

6. テスト フェールオーバー中に作成された VM を削除するには、レプリケートされたアイテムまたは復旧計画で **[Cleanup test failover]\(テスト フェールオーバーのクリーンアップ\)** をクリックします。 **[メモ]** を使用して、テスト フェールオーバーに関連する観察結果をすべて記録し、保存します。 

テスト フェールオーバーの詳細については、[こちら](site-recovery-test-failover-to-azure.md)をご覧ください。


## <a name="next-steps"></a>次のステップ

デプロイのテストが完了したら、次の作業を行います。

- さまざまな種類のフェールオーバーとそれらを実行する方法の[詳細を確認](site-recovery-failover.md)します。
- [復旧計画を使用](site-recovery-create-recovery-plans.md)して RTO を削減する方法の詳細を確認します。
