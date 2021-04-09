---
title: オンプレミスの Netezza サーバーから Azure へのデータの移行
description: Azure Data Factory を使用してオンプレミスの Netezza サーバーから Azure にデータを移行する。
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: 36ca7b709e0ec945f1fb4a9a8b745d20e6a58fe1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367777"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Azure Data Factory を使用してオンプレミスの Netezza サーバーから Azure にデータを移行する 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory には、オンプレミスの Netezza サーバーから Azure のストレージ アカウントまたは Azure Synapse Analytics データベースに大量のデータを移行することができる、パフォーマンスと信頼性が高くコスト効率に優れたメカニズムが用意されています。 

この記事では、データ エンジニアと開発者向けに次の情報を提供します。

> [!div class="checklist"]
> * パフォーマンス 
> * 回復性のコピー
> * ネットワークのセキュリティ
> * 概要ソリューション アーキテクチャ 
> * 実装のベスト プラクティス  

## <a name="performance"></a>パフォーマンス

Azure Data Factory は、さまざまなレベルで並列処理を可能にするサーバーレス アーキテクチャを提供します。 開発者は、パイプラインを構築することでネットワークとデータベースの両方の帯域幅を余すことなく使用し、環境内でのデータ移動のスループットを最大化することができます。

![パフォーマンスの図](media/data-migration-guidance-netezza-azure-sqldw/performance.png)

上の図は、次のように解釈できます。

- 1 回のコピー アクティビティで、スケーラブルなコンピューティング リソースを利用できます。 Azure Integration Runtime を使用する場合は、各コピー アクティビティに対して[最大 256 DIU](./copy-activity-performance.md#data-integration-units) をサーバーレス方式で指定できます。 セルフホステッド統合ランタイム (セルフホステッド IR) では、マシンを手動でスケールアップしたり、複数のマシン ([最大 4 台のノード](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)) にスケールアウトしたりすることができます。また、1 回のコピー アクティビティにより、すべてのノードでアクティビティがパーティションを配布されます。 

- 1 回のコピー アクティビティで、複数のスレッドを使用したデータ ストアの読み取りと書き込みが行われます。 

- Azure Data Factory 制御フローでは、複数のコピー アクティビティを並列して開始できます。 たとえば、[For Each ループ](./control-flow-for-each-activity.md)を使用して開始できます。 

詳細については、[コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド](./copy-activity-performance.md)を参照してください。

## <a name="resilience"></a>回復力

Azure Data Factory には組み込みの再試行メカニズムがあるため、1 回のコピー アクティビティの実行で、データ ストアまたは基になるネットワークの特定のレベルの一時的なエラーを処理できます。

Azure Data Factory のコピー アクティビティでは、ソースとシンク データ ストアの間でデータをコピーする場合、2 つの方法で互換性のない行を取り扱うことができます。 コピー アクティビティを中止して失敗とするか、互換性のないデータ行をスキップして残りのデータのコピーを続行できます。 さらに、エラーの原因を把握するために互換性のない行を Azure BLOB ストレージまたは Azure Data Lake Store のログに記録し、データ ソースのデータを修正してから、コピーアクティビティを再試行できます。

## <a name="network-security"></a>ネットワークのセキュリティ 

既定では、Azure Data Factory は、ハイパーテキスト転送プロトコル セキュア (HTTPS) プロトコル経由の暗号化された接続を使用して、オンプレミスの Netezza サーバーから Azure ストレージ アカウントまたは Azure Synapse Analytics データベースにデータを転送します。 HTTPS によって転送中のデータが暗号化され、盗聴や中間者攻撃が防止されます。

また、パブリック インターネット経由でデータを転送しない場合は、Azure ExpressRoute を介してプライベート ピアリング リンク経由でデータを転送することで、より高いセキュリティを実現できます。 

次のセクションでは、より強固なセキュリティを実現する方法について説明します。

## <a name="solution-architecture"></a>ソリューションのアーキテクチャ

このセクションでは、データを移行する 2 つの方法について説明します。

### <a name="migrate-data-over-the-public-internet"></a>パブリック インターネット経由でデータを移行する

![パブリック インターネット経由でデータを移行する](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png)

上の図は、次のように解釈できます。

- このアーキテクチャでは、パブリック インターネット経由で HTTPS を使用してデータを安全に転送します。

- このアーキテクチャを実現するには、企業ファイアウォールの内側にある Windows マシンに、Azure Data Factory 統合ランタイム (セルフホステッド) をインストールする必要があります。 この統合ランタイムから Netezza サーバーに直接アクセスできることを確認します。 ネットワークとデータ ストアの帯域幅を完全に活用してデータをコピーするには、マシンを手動でスケールアップするか、複数のマシンにスケールアウトします。

- このアーキテクチャを使用すると、初期スナップショット データと差分データの両方を移行できます。

### <a name="migrate-data-over-a-private-network"></a>プライベート ネットワーク経由でデータを移行する 

![プライベート ネットワーク経由でデータを移行する](media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png)

上の図は、次のように解釈できます。

- このアーキテクチャでは、データの移行は Azure ExpressRoute を介してプライベート ピアリング リンク経由で行われ、データがパブリック インターネット経由で転送されることはありません。 

- このアーキテクチャを実現するには、Azure 仮想ネットワーク内の Windows 仮想マシン (VM) に Azure Data Factory 統合ランタイム (セルフホステッド) をインストールする必要があります。 ネットワークとデータ ストアの帯域幅を完全に活用してデータをコピーするには、VM を手動でスケールアップするか、複数の VM にスケールアウトします。

- このアーキテクチャを使用すると、初期スナップショット データと差分データの両方を移行できます。

## <a name="implement-best-practices"></a>ベスト プラクティスを実装する 

### <a name="manage-authentication-and-credentials"></a>認証情報と資格情報の管理 

- Netezza に対して認証を行うために、[接続文字列を介した ODBC 認証](./connector-netezza.md#linked-service-properties)を使用できます。 

- Azure BLOB ストレージに対して認証するには: 

   - [Azure リソースのマネージド ID](./connector-azure-blob-storage.md#managed-identity) を使用することを強くお勧めします。 マネージド ID は Azure Active Directory (Azure AD) で自動的に管理される Azure Data Factory ID をベースに構築されており、リンクされたサービス定義で資格情報を指定せずにパイプラインを構成できます。  

   - また、[サービス プリンシパル](./connector-azure-blob-storage.md#service-principal-authentication)、[共有アクセス署名](./connector-azure-blob-storage.md#shared-access-signature-authentication)、または[ストレージ アカウント キー](./connector-azure-blob-storage.md#account-key-authentication)を使用して Azure BLOB ストレージに対する認証を行うこともできます。 

- Data Lake Storage Gen2 に対して認証するには: 

   - [Azure リソースのマネージド ID](./connector-azure-data-lake-storage.md#managed-identity) を使用することを強くお勧めします。
   
   - また、[サービス プリンシパル](./connector-azure-data-lake-storage.md#service-principal-authentication)または[ストレージ アカウント キー](./connector-azure-data-lake-storage.md#account-key-authentication)を使用することもできます。 

- Azure Synapse Analytics に対して認証するには:

   - [Azure リソースのマネージド ID](./connector-azure-sql-data-warehouse.md#managed-identity) を使用することを強くお勧めします。
   
   - また、[サービス プリンシパル](./connector-azure-sql-data-warehouse.md#service-principal-authentication)または [SQL 認証](./connector-azure-sql-data-warehouse.md#sql-authentication)を使用することもできます。

- Azure リソースのマネージド ID を使用しない場合は、簡単にするために、[Azure Key Vault に資格情報を格納](./store-credentials-in-key-vault.md)して、Azure Data Factory のリンクされたサービスを変更せずに、キーを一元的に管理およびローテーションすることを強くお勧めします。 これは、[CI/CD のベスト プラクティス](./continuous-integration-deployment.md#best-practices-for-cicd)の1 つでもあります。 

### <a name="migrate-initial-snapshot-data"></a>初回のスナップショット データ移行 

小さいテーブル (ボリューム サイズが 100 GB より小さい、または 2 時間以内に Azure に移行できる) の場合は、各コピー ジョブでテーブルごとにデータを読み込むよう設定できます。 スループットを向上させるには、複数の Azure Data Factory コピー ジョブを実行して、異なるテーブルを同時に読み込むことができます。 

各コピー ジョブ内では、並列クエリを実行してデータをパーティションでコピーするには、以下のいずれかのデータ パーティションのオプションで[`parallelCopies`プロパティ設定](./copy-activity-performance.md#parallel-copy)を使用することで、ある程度の水準の並列処理を達成することもできます。

- 効率を高めるために、データ スライスから始めることをお勧めします。  `parallelCopies` 設定の値が、Netezza サーバー上のテーブル内のデータ スライス パーティションの合計数より小さいことを確認します。  

- 各データ スライス パーティションのボリューム サイズが依然として大きい場合 (たとえば、10 GB を超える場合) は、動的範囲パーティションに切り替えることをお勧めします。 このオプションを使用すると、パーティションの数と各パーティションのパーティション列ごとのボリュームのサイズやその上限と下限を柔軟に定義できます。

大きなテーブル (つまり、ボリューム サイズが 100 GB より大きい、または 2 時間以内に Azure に移行 *できない* テーブル) では、データをカスタム クエリを使用してパーティション分割し、各コピー ジョブでパーティションを 1 つづつコピーすることをお勧めします。 スループットを向上させるには、複数の Azure Data Factory コピー ジョブを同時に実行することができます。 カスタム クエリにより各コピー ジョブで 1 つのパーティションを読み込むよう設定している場合でも、データ スライスまたは動的範囲を介して並列処理を有効にすることで、スループットを向上させることができます。 

ネットワークまたはデータ ストアの一時的な問題によってコピー ジョブが失敗した場合は、失敗したコピー ジョブを再実行して、そのテーブルから特定のパーティションを再度読み込むことができます。 読み込むパーティションが異なるその他のコピー ジョブは影響を受けません。

Azure Synapse Analytics データベースにデータを読み込む際は、Azure BLOB ストレージをステージングとして、コピー ジョブ内で PolyBase を有効にすることをお勧めします。

### <a name="migrate-delta-data"></a>差分データの移行 

テーブルから新規の行または更新された行を識別するには、スキーマのタイムスタンプ列か増分キーを使用します。 その後、最新の値を高基準値として外部テーブルに格納し、次にデータを読み込むときにその値を使用して差分データをフィルター処理できます。 

テーブルごとに異なる基準値列を使用して、新しい行や更新された行を識別できます。 Microsoft では、外部制御テーブルを作成することをお勧めします。 このテーブルで、各行は特定の基準値列名と高基準値を持つ Netezza サーバー上の 1 つのテーブルを表します。 

### <a name="configure-a-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムを構成する

Netezza サーバーから Azure にデータを移行する場合、サーバーが企業ファイアウォールの内側にあるか仮想ネットワーク環境内にあるかに関係なく、データを移動するエンジンとして、セルフホステッド IR を Windows マシンまたは VM にインストール必要があります。 セルフホステッド IR をインストールする際は、次のアプローチをお勧めします。

- 各 Windows マシンまたは VM で、32 vCPU と 128 GB のメモリの構成を開始します。 データ移行中に IR マシンの CPU とメモリの使用状況を監視して、パフォーマンスを向上させるためにマシンをさらにスケールアップする必要があるか、コストを節約するためにマシンをスケールダウンする必要があるかを確認できます。

- また、1 つのセルフホステッド IR に最大 4 つのノードを関連付けてスケールアウトすることもできます。 セルフホステッド IR に対して実行される 1 回のコピー ジョブで、すべての VM ノードが自動的に適用されてデータが並列してコピーされます。 高可用性を実現するには、データ移行中の単一障害点を回避するために、2 台の VM ノードから始めます。

### <a name="limit-your-partitions"></a>パーティションを制限する

ベスト プラクティスとして、代表的なサンプル データセットを使用してパフォーマンスの概念実証 (POC) を実施し、各コピー アクティビティにおける適切なパーティションのサイズを決定できるようにします。 2 時間以内に各パーティションを Azure に読み込むことをお勧めします。  

テーブルをコピーするには、まず 1 台のセルフホステッド IR マシンで 1 回のコピー アクティビティを行います。 テーブル内のデータ スライス パーティションの数に基づいて `parallelCopies` 設定を徐々に増やします。 コピー ジョブのスループットに基づき、Azure にテーブル全体を 2 時間以内に読み込むことができるかどうかを確認します。 

Azure にテーブルを 2 時間以内に読み込むことができず、かつセルフホステッド IR ノードとデータ ストアの容量が完全に使用されていない場合は、ネットワークの制限またはデータ ストアの帯域幅制限に達するまで、同時コピー アクティビティの数を徐々に増やします。 

引き続きセルフホステッド IR マシンの CPU およびメモリ使用率を監視し、CPU およびメモリが完全に使用されていることが確認されたときに、マシンをスケールアップするか複数のコンピューターにスケールアウトできるよう準備しておきます。 

Azure Data Factory のコピー アクティビティによって報告された調整エラーが発生した場合は、Azure Data Factory の同時実行数または `parallelCopies` の設定値を減らすか、ネットワークとデータ ストアの帯域幅または 1 秒あたりの最大 I/O 操作 (IOPS) の制限値を大きくすることを検討してください。 


### <a name="estimate-your-pricing"></a>価格のお見積り 

オンプレミスの Netezza サーバーから Azure Synapse Analytics データベースにデータを移行するために構築されている、次のパイプラインについて検討します。

![価格パイプライン](media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png)

以下のことがわかると仮定します。 

- データ ボリュームの合計は 50 テラバイト (TB)。 

- 最初のソリューション アーキテクチャを使用してデータを移行する (Netezza サーバーはファイアウォールの内側に設置されている)。

- 50 TB 分のデータ ボリュームが 500 のパーティションに分割され、各コピー アクティビティで 1 つのパーティションが移行される。

- 各コピー アクティビティは 4 台のマシンに対して 1 つのセルフホステッド IR を使用して構成され、20 メガバイト/秒 (Mbps) のスループットを実現する。 (コピー アクティビティ内では、`parallelCopies` が 4 に設定され、テーブルからデータを読み込む各スレッドが 5 MBps のスループットを実現する)。

- ForEach の同時実行数は 3 に設定され、合計スループットは 60 MBps である。

- 合計では、移行が完了するまでに 243 時間かかる。

上記の前提条件に基づき、見積もり価格は次のようになります。 

![価格テーブル](media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png)

> [!NOTE]
> 上記の価格は仮定です。 実際の料金は、環境の実際のスループットによって変わります。 (セルフホステッド IR がインストールされている) Windows マシンの料金は含まれていません。 

### <a name="additional-references"></a>その他のリファレンス

詳細については、次の記事とガイドを参照してください。

- [Azure Data Factory を使用してオンプレミスのリレーショナル データ ウェアハウス データベースから Azure にデータを移行する](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza コネクタ](./connector-netezza.md)
- [ODBC コネクタ](./connector-odbc.md)
- [Azure BLOB ストレージ コネクタ](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 コネクタ](./connector-azure-data-lake-storage.md)
- [Azure Synapse Analytics コネクタ](./connector-azure-sql-data-warehouse.md)
- [コピー アクティビティのパフォーマンスとチューニングに関するガイド](./copy-activity-performance.md)
- [セルフホステッド統合ランタイムを作成して構成する](./create-self-hosted-integration-runtime.md)
- [セルフホステッド統合ランタイム HA とスケーラビリティ](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [データ移動のセキュリティに関する考慮事項](./data-movement-security-considerations.md)
- [Azure Key Vault への資格情報の格納](./store-credentials-in-key-vault.md)
- [1 つのテーブルからデータを増分コピーする](./tutorial-incremental-copy-portal.md)
- [複数のテーブルからデータを増分コピーする](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Azure Data Factory の価格ページ](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>次のステップ

- [Azure Data Factory を使用して複数のコンテナーからファイルをコピーする](solution-template-copy-files-multiple-containers.md)