---
title: SAP ソリューション向け Azure Monitor の概要とアーキテクチャ | Microsoft Docs
description: この記事では、SAP ソリューション向け Azure Monitor に関してよく寄せられる質問に対する回答を示します
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: a88ad3930e114bdf9f3c3c340f92f164215d59c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671997"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>SAP ソリューション向け Azure Monitor (プレビュー)

## <a name="overview"></a>概要

SAP ソリューション向け Azure Monitor は、Azure で SAP ランドスケープを実行しているお客様向けの Azure ネイティブの監視製品です。 製品は、[SAP on Azure Virtual Machines](./hana-get-started.md) と [SAP on Azure Large Instances](./hana-overview-architecture.md) の両方で動作します。
SAP ソリューション向け Azure Monitor を使用するお客様は、Azure インフラストラクチャとデータベースからの利用統計情報を 1 か所で収集し、迅速なトラブルシューティングのために利用統計情報を視覚的に関連付けることができます。

SAP ソリューション向け Azure Monitor は、Azure Marketplace を通じて提供されます。 シンプルで直感的なセットアップ エクスペリエンスが提供されており、SAP ソリューション向け Azure Monitor 用のリソース (**SAP Monitor リソース** と呼ばれます) をわずか数回のクリックでデプロイできます。

お客様は、コンポーネントに対応する **プロバイダー** を追加することにより、Azure Virtual Machines、高可用性クラスター、SAP HANA データベースなど、SAP ランドスケープのさまざまなコンポーネントを監視できます。

サポートされているインフラストラクチャ:

- Azure Virtual Machine
- Azure Large Instances

サポートされるデータベース:
- SAP HANA データベース
- Microsoft SQL Server

SAP ソリューション向け Azure Monitor では、Log Analytics や[ブック](../../../azure-monitor/visualize/workbooks-overview.md)などの [Azure Monitor](../../../azure-monitor/overview.md) の既存機能を使用して、追加の監視機能が提供されます。 お客様は、SAP ソリューション向け Azure Monitor によって提供される既定のブックを編集することによって[カスタム視覚化](../../../azure-monitor/visualize/workbooks-overview.md#getting-started)を作成し、Log Analytics ワークスペースを使用して[カスタム クエリ](../../../azure-monitor/logs/log-analytics-tutorial.md)や[カスタム アラート](../../../azure-monitor/alerts/tutorial-response.md)を作成し、[柔軟な保有期間](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period)を利用し、監視データをチケット システムに接続することができます。

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>SAP ソリューション向け Azure Monitor ではどのようなデータが収集されますか?

SAP ソリューション向け Azure Monitor で収集されるデータは、お客様によって構成されるプロバイダーによって異なります。 パブリック プレビューの間は、次のデータが収集されます。

高可用性 Pacemaker クラスター利用統計情報:
- ノード、リソース、SBD デバイスの状態
- Pacemaker の場所の制約
- クォーラム投票とリングの状態
- [Others](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA 利用統計情報:
- CPU、メモリ、ディスク、ネットワークの使用率
- HANA システム レプリケーション (HSR)
- HANA バックアップ
- HANA ホストの状態
- インデックス サーバー ロールとネーム サーバー ロール

Microsoft SQL Server 利用統計情報:
- CPU、メモリ、ディスクの使用率
- ホスト名、SQL インスタンス名、SAP システム ID
- 経時的なバッチ要求、コンパイル、ページの予測保持期間
- 経時的な最もコストの高い SQL ステートメントの上位 10 個
- SAP システムで最大のテーブルの上位 12 個
- SQL Server エラー ログに記録された問題
- 経時的なブロック プロセスと SQL 待機統計

オペレーティング システムのテレメトリ (Linux) 
- CPU 使用率、フォークの数、実行中およびブロックされたプロセス。 
- メモリ使用率と分布 (使用済み、キャッシュ、バッファー)。 
- スワップ使用率、ページング、およびスワップ率。 
- ファイルシステムの使用率、ブロック デバイスごとの読み取りおよび書き込みバイト数。 
- ブロック デバイスあたりの読み取り/書き込み待機時間。 
- 実行中の I/O 数、永続メモリの読み取り/書き込みバイト数。 
- ネットワーク パケットの入出力、ネットワークの入出力バイト数 

## <a name="data-sharing-with-microsoft"></a>Microsoft とのデータ共有

SAP ソリューション向け Azure Monitor では、SAP on Azure のお客様のサポートを強化するために、システム メタデータが収集されます。 PII/EUII は収集されません。
お客様は、SAP ソリューション向け Azure Monitor リソースを作成するときにドロップダウンから *[共有]* を選択することで、Microsoft とのデータ共有を有効にすることができます。
お客様にはデータ共有を有効にすることを強くお勧めします。これにより、Microsoft サポートとエンジニアリング チームにお客様の環境に関するより多くの情報が提供され、ミッション クリティカルな SAP on Azure のお客様へのサポートが向上します。

## <a name="architecture-overview"></a>アーキテクチャの概要

次の図は、SAP ソリューション向け Azure Monitor で SAP HANA データベースから利用統計情報が収集される方法の概要を示しています。 アーキテクチャは、SAP HANA が Azure Virtual Machines または Azure Large Instances のどちらにデプロイされているかに依存しません。

![SAP ソリューション向け Azure Monitor のアーキテクチャ](./media/azure-monitor-sap/azure-monitor-architecture.png)

アーキテクチャの主要なコンポーネントは次のとおりです。
- Azure portal – お客様の出発点となります。 お客様は Azure portal 内のマーケットプレースに移動して、SAP ソリューション向け Azure Monitor を見つけることができます
- SAP ソリューション向け Azure Monitor リソース – お客様が監視利用統計情報を表示するためのランディング プレースです
- 管理対象リソース グループ – SAP ソリューション向け Azure Monitor リソースのデプロイの一部として自動的にデプロイされます。 管理対象リソース グループにデプロイされたリソースは、利用統計情報の収集に役立ちます。 デプロイされる主要なリソースとその用途は次のとおりです。
   - Azure 仮想マシン: "*コレクター VM*" とも呼ばれます。 これは Standard_B2ms VM です。 この VM の主な目的は、"*監視ペイロード*" をホストすることです。 監視ペイロードとは、ソース システムから利用統計情報を収集し、収集したデータを監視フレームワークに転送するロジックのことです。 上の図では、監視ペイロードには、SQL ポート経由で SAP HANA データベースに接続するためのロジックが含まれています。
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md):このリソースは、SAP HANA データベースの資格情報を安全に保持し、[プロバイダー](./azure-monitor-providers.md)に関する情報を格納するためにデプロイされます。
   - Log Analytics ワークスペース: 利用統計情報の格納先です。
      - 視覚化は、[Azure ブック](../../../azure-monitor/visualize/workbooks-overview.md)を使用して Log Analytics の利用統計情報を基に構築されます。 お客様は視覚化をカスタマイズできます。 また、お客様は、ブックやブック内の特定の視覚エフェクトを Azure ダッシュボードにピン留めし、最低 30 分の間隔で自動的に更新することもできます。
      - お客様は、デプロイ時にオプションを選択することで、SAP モニター リソースと同じサブスクリプション内の既存のワークスペースを使用できます。
      - お客様は、Kusto クエリ言語 (KQL) を使用して、Log Analytics ワークスペース内の生テーブルに対する[クエリ](../../../azure-monitor/logs/log-query-overview.md)を実行できます。 "*カスタム ログ*" を調べます。

> [!Note]
> 管理対象リソース グループにデプロイされている VM への修正プログラムの適用とメンテナンスは、お客様が行う必要があります。

> [!Tip]
> お客様は、SAP ソリューション向け Azure Monitor 用のリソースと同じ Azure サブスクリプション内にデプロイされた既存の Log Analytics ワークスペースがある場合、それを利用統計情報の収集に使用することを選択できます。

### <a name="architecture-highlights"></a>アーキテクチャのハイライト

アーキテクチャの主な特徴を次に示します。
 - **複数インスタンス** - お客様は、SAP ソリューション向け Azure Monitor の単一のリソースを使用して、VNET 内の複数の SAP SID を対象に、特定のコンポーネントの種類 (HANA DB、HA クラスター、Microsoft SQL Server など) の複数のインスタンスに対するモニターを作成できます。
 - **複数プロバイダー** - 上のアーキテクチャの図では、例として SAP HANA プロバイダーが示されています。 同様に、お客様は、対応するコンポーネント (HANA DB、HA クラスター、Microsoft SQL Server など) 用に追加のプロバイダーを構成して、それらのコンポーネントからデータを収集できます。
 - **オープンソース** - SAP ソリューション向け Azure Monitor のソース コードは、[GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) で入手できます。 お客様は、プロバイダーのコードを参照し、製品の詳細を確認したり、フィードバックを投稿または共有したりできます。
 - **拡張可能なクエリ フレームワーク** - 利用統計情報を収集するための SQL クエリは、[JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) で記述されています。 さらに多くの利用統計情報を収集するための SQL クエリを簡単に追加できます。 お客様は、このドキュメントの最後にあるリンクを通じてフィードバックを残すか、アカウント チームに連絡することで、SAP ソリューション向け Azure Monitor に追加する特定の利用統計情報を要求できます。

## <a name="pricing"></a>価格
SAP ソリューション向け Azure Monitor は無料の製品です (ライセンス料金はありません)。 お客様は、管理対象リソース グループ内の基になるコンポーネントに対するコストを支払う必要があります。

## <a name="next-steps"></a>次のステップ

プロバイダーについて学習し、最初の SAP ソリューション向け Azure Monitor リソースを作成します。
 - [プロバイダー](./azure-monitor-providers.md)について詳しく学習します
 - [Azure PowerShell を使用して SAP ソリューション向け Azure Monitor をデプロイする](azure-monitor-sap-quickstart-powershell.md)
 - SAP ソリューション向け Azure Monitor に関する質問がありますか。 [FAQ](./azure-monitor-faq.md) のセクションを確認してください
