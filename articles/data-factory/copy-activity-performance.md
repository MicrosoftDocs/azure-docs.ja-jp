---
title: コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド
description: コピー アクティビティを使用する場合に、Azure Data Factory でのデータ移動のパフォーマンスに影響する主な要因について説明します。
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/24/2019
ms.openlocfilehash: 1b1b02e310c98a78006d258333c0ec10e89e3b31
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927461"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド

> [!div class="op_single_selector" title1="使用している Azure Data Factory のバージョンを選択してください:"]
> * [Version 1](v1/data-factory-copy-activity-performance.md)
> * [現在のバージョン](copy-activity-performance.md)

データ レイクまたはエンタープライズ データ ウェアハウス (EDW) から Azure への大規模なデータ移行を実行する場合でも、ビッグ データ分析のためにさまざまなソースから Azure にデータを大規模に取り込む場合でも、最適なパフォーマンスとスケーラビリティを実現することが重要です。  Azure Data Factory には、データを大規模に取り込むためのパフォーマンス、回復性、およびコスト効率に優れたメカニズムが備わっており、パフォーマンスとスケーラビリティの高いデータ インジェスト パイプラインを構築しようとしているデータ エンジニアに最適です。

この記事を読むと、次の質問に回答できるようになります。

- データ移行シナリオとデータ インジェスト シナリオで ADF のコピー アクティビティを使用すると、どのレベルのパフォーマンスとスケーラビリティを実現できますか。

- ADF のコピー アクティビティのパフォーマンスを調整するには、どのような手順を実行する必要がありますか。
- 1 回のコピー アクティビティの実行でパフォーマンスを最適化するために、どのような ADF のパフォーマンス最適化のヒントを利用できますか。
- コピーのパフォーマンスを最適化するときに考慮する ADF 以外の要因には何がありますか。

> [!NOTE]
> コピー アクティビティ全般に慣れていない場合は、この記事を読む前に、[コピー アクティビティの概要](copy-activity-overview.md)に関するページを参照してください。

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>ADF を使用して実現可能なコピー パフォーマンスとスケーラビリティ

ADF には、さまざまなレベルで並列処理を可能にするサーバーレス アーキテクチャが用意されています。そのため、開発者は、ネットワーク帯域幅だけでなくストレージの IOPS と帯域幅を利用して、データ移動のスループットが環境に合わせて最大限になるパイプラインを構築できます。  これは、ソース データ ストア、ターゲット データ ストア、およびソースとターゲット ストア間のネットワーク帯域幅によって提供される最小スループットを測定することで、実現できるスループットを推定できることを意味します。  次の表では、データ サイズと環境の帯域幅制限に基づいて、コピー期間を計算しています。 

| データ サイズ/ <br/> bandwidth | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2.7 分    | 1.4 分   | 0.3 分   | 0.1 分  | 0.03 分 | 0.01 分 | 0.0 分   |
| **10 GB**                   | 27.3 分   | 13.7 分  | 2.7 分   | 1.3 分  | 0.3 分  | 0.1 分  | 0.03 分  |
| **100 GB**                  | 4.6 時間    | 2.3 時間   | 0.5 時間   | 0.2 時間  | 0.05 時間 | 0.02 時間 | 0.0 時間   |
| **1 TB**                    | 46.6 時間   | 23.3 時間  | 4.7 時間   | 2.3 時間  | 0.5 時間  | 0.2 時間  | 0.05 時間  |
| **10 TB**                   | 19.4 日  | 9.7 日  | 1.9 日  | 0.9 日 | 0.2 日 | 0.1 日 | 0.02 日 |
| **100 TB**                  | 194.2 日 | 97.1 日 | 19.4 日 | 9.7 日 | 1.9 日 | 1 日   | 0.2 日  |
| **1 PB**                    | 64.7 か月    | 32.4 か月   | 6.5 か月    | 3.2 か月   | 0.6 か月   | 0.3 か月   | 0.06 か月   |
| **10 PB**                   | 647.3 か月   | 323.6 か月  | 64.7 か月   | 31.6 か月  | 6.5 か月   | 3.2 か月   | 0.6 か月    |

ADF コピーはさまざまなレベルでスケーラブルです。

![ADF のコピー スケールのしくみ](media/copy-activity-performance/adf-copy-scalability.png)

- ADF 制御フローでは、複数のコピー アクティビティを並列して開始できます。たとえば、[For Each ループ](control-flow-for-each-activity.md)を使用します。
- 1 回のコピー アクティビティで、スケーラブルなコンピューティング リソースを利用できます。Azure Integration Runtime を使用する場合は、サーバーレス方式で各コピー アクティビティに対して[最大 256 DIU](#data-integration-units) を指定できます。セルフホステッド統合ランタイムを使用する場合は、手動でマシンをスケールアップするか、複数のマシン ([最大 4 ノード](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) にスケールアウトすることができます。また、1 回のコピー アクティビティによって、すべてのノードでファイル セットがパーティション分割されます。
- 1 回のコピー アクティビティで、複数のスレッドを使用したデータ ストアの読み取りと書き込みが[並列](#parallel-copy)で行われます。

## <a name="performance-tuning-steps"></a>パフォーマンス チューニングの手順

Azure Data Factory サービスとコピー アクティビティのパフォーマンスをチューニングするには、次の手順を実行します。

1. **テスト データセットを選択し、ベースラインを確立します。** 開発フェーズでは、代表的なデータ サンプルに対してコピー アクティビティを使用して、パイプラインをテストします。 選択するデータセットは、一般的なデータ パターン (フォルダー構造、ファイル パターン、データ スキーマなど) を表し、コピーのパフォーマンスを評価するのに十分な大きさ (たとえば、コピー アクティビティが完了するまで 10 分以上かかる) である必要があります。 [コピー アクティビティの監視](copy-activity-overview.md#monitoring)の後に、実行の詳細とパフォーマンス特性を収集します。

2. **1 回のコピー アクティビティのパフォーマンスを最大化する方法**:

   まず第一に、1 回のコピー アクティビティを使用するパフォーマンスを最大化することをお勧めします。

   **コピー アクティビティが Azure Integration Runtime で実行される場合:**

   [データ統合単位 (DIU)](#data-integration-units) と[並列コピー](#parallel-copy)の設定の既定値から始めます。  パフォーマンス テストを実行し、達成されたパフォーマンスと、DIU と並列コピーに使用される実際の値を書き留めます。  実行結果と使用されたパフォーマンス設定を収集する方法については、[コピー アクティビティの監視](copy-activity-overview.md#monitoring)に関する記事を参照してください。

   次に、毎回 DIU 設定の値を倍増しながら、追加のパフォーマンス テストを実行します。  または、既定の設定を使用して達成されたパフォーマンスが予想を大幅に下回っていると思う場合は、以降のテストの実行で DIU の設定を大幅に増やすことができます。

   DIU 設定を増やすと、コピー アクティビティはほぼ直線的に増加します。  DIU 設定を 2 倍にしてもスループットが倍増しない場合は、次の 2 つが発生している可能性があります。

   - 実行している特定のコピー パターンでは、DIU を追加してもメリットはありません。  より大きな DIU 値を指定した場合でも、実際に使用される DIU は同じままであるため、前と同じスループットが得られます。  この場合は、手順 3 を参照し、同時に複数のコピーを実行して合計スループットを最大化します。
   - DIU (より多くの馬力) を追加することで、データの抽出、転送、読み込みの速度が向上し、ソース データ ストア、ストア間のネットワーク、またはターゲット データ ストアがボトルネックに達したために調整されている可能性があります。  この場合は、データ ストアの管理者またはネットワーク管理者に連絡して上限を上げるか、調整の発生が止まるまで DIU の設定を減らしてみます。

   **コピー アクティビティがセルフホステッド統合ランタイムで実行される場合:**

   統合ランタイムをホストするには、データ ストアをホストするサーバーとは別の専用のコンピューターを使用することをお勧めします。

   [並列コピー](#parallel-copy)設定の既定値とセルフホステッド IR 用の単一のノードの使用から始めます。  パフォーマンス テストを実行し、達成されたパフォーマンスを書き留めます。

   より高いスループットを実現するには、セルフホステッド IR をスケールアップするかスケールアウトします。

   - セルフホステッド IR ノード上の CPU と使用可能なメモリが十分に活用されていないにもかかわらず、同時ジョブの実行が上限に達する場合は、ノードで実行できる同時実行ジョブの数を増やすことでスケールアップを行う必要があります。  手順については、[こちら](create-self-hosted-integration-runtime.md#scale-up)を参照してください。
   - 一方、セルフホステッド IR ノードの CPU が高いか、使用可能なメモリが少ない場合は、新しいノードを追加して、複数のノード間で負荷をスケールアウトすることができます。  手順については、[こちら](create-self-hosted-integration-runtime.md#high-availability-and-scalability)を参照してください。

   セルフホステッド IR の容量をスケールアップまたはスケールアウトするときは、パフォーマンス テストの実行を繰り返して、スループットが向上するかどうかを確認します。  スループットの向上が止まった場合は、ソース データ ストア、ストア間のネットワーク、またはターゲット データ ストアのいずれかがボトルネックに達したため、調整が開始された可能性があります。 この場合は、データ ストアの管理者またはネットワーク管理者に連絡して上限を上げるか、セルフホステッド IR の前回のスケーリング設定に戻ってみます。 

3. **複数のコピーを同時に実行することで合計スループットを最大化する方法:**

   これで、1 回のコピー アクティビティのパフォーマンスが最大になったので、環境 (ネットワーク、ソース データ ストア、およびターゲット データ ストア) のスループットの上限にまだ達していなければ、[For Each ループ](control-flow-for-each-activity.md)などの ADF 制御フロー コンストラクトを使用して、複数のコピー アクティビティを並行して実行できます。

4. **パフォーマンス チューニングのヒントと最適化機能。** 場合によっては、Azure Data Factory でコピー アクティビティを実行すると、次の例に示されているように、[コピー アクティビティの監視](copy-activity-overview.md#monitor-visually)の上部に "パフォーマンス チューニングに関するヒント" が表示されます。 このメッセージからは、特定のコピー実行で確認されたボトルネックが伝えられます。 コピー スループットを上げるために変更すべき点も指摘されます。 パフォーマンス チューニングのヒントからは現在のところ、次のような提案が表示されます。

   - Azure SQL Data Warehouse にデータをコピーするとき PolyBase を使用する。
   - データ ストア側のリソースがボトルネックのとき、Azure Cosmos DB の要求ユニットまたは Azure SQL Database の DTU (データベース スループット ユニット) を増やす。
   - 不要なステージング コピーを削除する。

   パフォーマンスのチューニングのルールも、徐々に強化されていきます。

   **例:パフォーマンス チューニングのヒントを参考にして Azure SQL Database にコピーする**

   このサンプルでは、コピー実行の間、Azure Data Factory では、シンク Azure SQL Database の DTU 利用率が高くなり、それが書き込み操作を遅らせていることが検出されます。 DTU を増やし、Azure SQL Database 層を増やすことが提案されます。 

   ![パフォーマンスのチューニングに関するヒントを使用したコピーの監視](media/copy-activity-overview/copy-monitoring-with-performance-tuning-tips.png)

   さらに、次のようなパフォーマンスの最適化機能について理解しておく必要があります。

   - [並列コピー](#parallel-copy)
   - [データ統合単位](#data-integration-units)
   - [ステージング コピー](#staged-copy)
   - [セルフホステッド統合ランタイムのスケーラビリティ](concepts-integration-runtime.md#self-hosted-integration-runtime)

5. **構成をデータセット全体まで拡張します。** 実行の結果とパフォーマンスに問題がなければ、データセット全体を網羅するよう、定義とパイプラインを拡張できます。

## <a name="copy-performance-optimization-features"></a>コピー パフォーマンス最適化機能

Azure Data Factory には、次のパフォーマンス最適化機能があります。

- [並列コピー](#parallel-copy)
- [データ統合単位](#data-integration-units)
- [ステージング コピー](#staged-copy)

### <a name="data-integration-units"></a>データ統合単位

データ統合単位は、Azure Data Factory の 1 つの単位の能力 (CPU、メモリ、ネットワーク リソース割り当ての組み合わせ) を表す尺度です。 データ統合単位は [Azure 統合ランタイム](concepts-integration-runtime.md#azure-integration-runtime)にのみ適用され、[セルフホステッド統合ランタイム](concepts-integration-runtime.md#self-hosted-integration-runtime)には適用されません。

**使用済み DIU の数 \* コピー期間 \* 単価/DIU 時間** が課金されます。 現在の価格については、[こちら](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)を参照してください。 サブスクリプションの種類ごとに、現地通貨と個別の割引が適用される場合があります。

コピー アクティビティの実行を支援するために許可される DIU は **2 から 256** です。 指定しなかった場合、または UI で [自動] を選択した場合、ソースとシンクのペアとデータ パターンに基づいて、最適な DIU 設定が Data Factory によって動的に適用されます。 次の表に、さまざまなコピー シナリオで使用される既定の DIU を示します。

| コピー シナリオ | サービスによって決定される既定の DIU |
|:--- |:--- |
| ファイル ベースのストア間でのデータのコピー | ファイルの数とサイズに応じて 4 〜 32。 |
| Azure SQL Database または Azure Cosmos DB にデータをコピーする |シンクの Azure SQL Database または Cosmos DB の層 (DTU/RU の数) に応じて 4 から 16。 |
| その他すべてのコピー シナリオ | 4 |

この既定の動作をオーバーライドするには、**dataIntegrationUnits** プロパティに次のように値を指定します。 コピー操作が実行時に使用する *DIU の実際の数*は、データ パターンに応じて、構成されている値以下になります。

アクティビティ実行を監視すると、コピー アクティビティ出力で、各コピーで使用された DIU を確認できます。 詳細については、[コピー アクティビティ監視](copy-activity-overview.md#monitoring)に関するページを参照してください。

> [!NOTE]
> 4 より大きい DIU の設定は現在、Azure Blob、ADLS Gen1、ADLS Gen2、Amazon S3、Google Cloud Storage、クラウド FTP、クラウド SFTP から、またはパーティション オプションが有効になっているクラウド リレーショナル データ ストア ([Oracle](connector-oracle.md#oracle-as-source)/[Netezza](connector-netezza.md#netezza-as-source)/[Teradata](connector-teradata.md#teradata-as-source) など) から、他の任意のクラウド データ ストアに複数のファイルをコピーする場合にのみ適用されます。

**例:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

### <a name="parallel-copy"></a>並列コピー

**parallelCopies** プロパティを使用し、コピー アクティビティで使用する並列処理を指示できます。 このプロパティは、並列でソースから読み取る、またはシンク データ ストアに書き込むことができるコピー アクティビティ内の最大スレッド数と見なすことができます。

コピー アクティビティ実行ごとに、Azure Data Factory によって、コピー元のデータ ストアからデータをコピーする際と、コピー先のデータ ストアにデータをコピーする際に使用する並列コピーの数が決定されます。 使用する並列コピーの既定数は、使用するソースやシンクの種類に依存します。

| コピー シナリオ | サービスによって決定される並列コピーの既定数 |
| --- | --- |
| ファイル ベースのストア間でのデータのコピー |ファイルのサイズと、2 つのクラウド データ ストア間でのデータのコピーで使用される DMU の数またはセルフホステッド統合ランタイム マシンの物理構成によって異なります。 |
| パーティション オプションが有効のリレーショナル データ ストアからコピー ([Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[Teradata](connector-teradata.md#teradata-as-source)、[SAP テーブル](connector-sap-table.md#sap-table-as-source)、[SAP オープン ハブ](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)を含む)|4 |
| 任意のコピー元ストアから Azure Table ストレージにデータをコピーする |4 |
| 他のすべてのコピー シナリオ |1 |

> [!TIP]
> ファイルベースのストア間でデータをコピーするとき、通常、既定の動作で最適なスループットが与えられます。 既定の動作は、コピー元のファイル パターンに基づいて自動的に決定されます。

データ ストアをホストしているコンピューターの負荷を制御したり、コピーのパフォーマンスをチューニングしたりするには、**parallelCopies** プロパティの値を指定し、既定値をオーバーライドできます。 値は 1 以上の整数でなければなりません。 実行時にコピー アクティビティは、設定された値以下でパフォーマンスが最大になる値を使用します。

**注意する点:**

- ファイル ベースのストア間でデータをコピーする場合は、**parallelCopies** によってファイル レベルでの並列処理が決まります。 単一ファイル内でのチャンク化は裏で自動的かつ透過的に行われます。 指定されたソース データ ストアの種類に最適なチャンク サイズを使用し、**parallelCopies** とは独立に並行してデータを読み込むよう設計されています。 実行時にコピー操作でデータ移動サービスに使用される並列コピーの実際の数は、存在するファイルの数以下となります。 コピー動作が **mergeFile** の場合、コピー アクティビティはファイル レベルでの並列処理を活用できません。
- ([Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[Teradata](connector-teradata.md#teradata-as-source)、[SAP テーブル](connector-sap-table.md#sap-table-as-source)、[SAP オープン ハブ](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source) コネクタをコピー元とし、データ パーティショニングを有効にする場合を除き) ファイル ベース以外のストアからファイル ベースのストアにデータをコピーする場合、データ移動サービスでは **parallelCopies** プロパティは無視されます。 並列処理が指定されても、この場合は適用されません。
- **parallelCopies** プロパティは **dataIntegrationUnits** と無関係です。 前者はすべてのデータ統合単位全体でカウントされます。
- **parallelCopies** プロパティに値を指定するとき、コピー元データ ストアとシンク データ ストアの負荷増加を考慮してください。 また、ハイブリッド コピーなどで、セルフホステッド統合ランタイムによってコピー アクティビティが支援される場合、セルフホステッド統合ランタイムの負荷増加を考慮してください。 この負荷増加は、特に複数のアクティビティがある場合や、同じデータ ストアに対して実行される同じアクティビティの同時実行がある場合に発生します。 データ ストアまたはセルフホステッド統合ランタイムの負荷の上限に達したことがわかった場合は、**parallelCopies** の値を減らし、負荷を軽減してください。

**例:**

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "AzureDataLakeStoreSink"
            },
            "parallelCopies": 32
        }
    }
]
```

### <a name="staged-copy"></a>ステージング コピー

ソース データ ストアからシンク データ ストアにデータをコピーする場合、中間のステージング ストアとして Blob Storage を使用できます。 ステージングは、特に次のような場合に役立ちます。

- **PolyBase を使ってさまざまなデータ ストアから SQL Data Warehouse にデータを取り込む。** SQL Data Warehouse は、大量のデータを読み込むための高スループットのメカニズムとして PolyBase を使用します。 ソース データが Blob Storage または Azure Data Lake Store にあることと追加の条件を満たすことが必要です。 Blob Storage または Azure Data Lake Store 以外のデータ ストアからデータを読み込む際は、中間ステージング Blob Storage 経由のデータ コピーを有効にすることができます。 その場合、Azure Data Factory は、PolyBase の要件を満たすために必要なデータ変換を実行します。 その後、PolyBase を使用して、効率的にデータを SQL Data Warehouse に読み込みます。 詳細については、「[PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-sql-data-warehouse)」を参照してください。
- **ネットワーク接続が遅い場合、ハイブリッド データ移動 (オンプレミス データ ストアからクラウド データ ストアへのコピー) の実行に少し時間がかかる場合がある。** パフォーマンスを向上させるため、ステージング コピーを使用してオンプレミスのデータを圧縮することで、クラウド内のステージング データ ストアにデータを移動する時間を短縮できます。 その後、データは、ターゲット データ ストアに読み込む前に、ステージング ストアで圧縮を解除できます。
- **企業の IT ポリシーが理由で、ファイアウォールでポート 80 とポート 443 以外のポートを開きたくない。** たとえば、オンプレミス データ ストアから Azure SQL Database シンクまたは SQL Data Warehouse シンクにデータをコピーする場合、Windows ファイアウォールと会社のファイアウォールの両方で、ポート 1433 の送信 TCP 通信を有効にする必要があります。 このシナリオでは、ステージング コピーは、セルフホステッド統合ランタイムを利用して、まずポート 443 で HTTP または HTTPS 経由で Blob Storage ステージング インスタンスにデータをコピーします。 次に、Blob Storage ステージングから SQL Database または SQL Data Warehouse にデータを読み込みます。 このフローでは、ポート 1433 を有効にする必要はありません。

#### <a name="how-staged-copy-works"></a>ステージング コピーのしくみ

ステージング機能を有効にすると、最初にデータがソース データ ストアからステージング Blob Storage (独自に用意) にコピーされます。 次に、データは、ステージング データ ストアからシンク データ ストアにコピーされます。 Azure Data Factory では、2 段階のフローが自動的に管理されます。 Azure Data Factory ではまた、データ移動の完了後、ステージング ストレージから一時データが消去されます。

![ステージング コピー](media/copy-activity-performance/staged-copy.png)

ステージング ストアを使用したデータ移動を有効にすると、ソース データ ストアから中間またはステージング データ ストアにデータを移動する前にデータを圧縮し、中間またはステージング データ ストアからシンク データ ストアにデータを移動する前にそのデータの圧縮を解除するかどうかを指定できます。

現在のところ、ステージング コピーの使用に関係なく、異なるセルフホステッド統合ランタイムで接続されている 2 つのデータ ストア間でデータをコピーできません。 このようなシナリオの場合、コピー元からステージングにコピーし、その後、ステージングからシンクにコピーするよう、明示的につながれた 2 つのコピー アクティビティを構成できます。

#### <a name="configuration"></a>構成

コピー アクティビティの **enableStaging** 設定を構成して、目的のデータ ストアに読み込む前にデータを Blob Storage にステージングするかどうかを指定します。 **enableStaging** を `TRUE` に設定した場合は、次の表に記載されている追加のプロパティを指定する必要があります。 ステージング用に Azure Storage または Storage Shared Access Signature のリンクされたサービスがない場合は、作成する必要もあります。

| プロパティ | 説明 | 既定値 | 必須 |
| --- | --- | --- | --- |
| enableStaging |中間ステージング ストアを経由してデータをコピーするかどうかを指定します。 |False |いいえ |
| linkedServiceName |[AzureStorage ](connector-azure-blob-storage.md#linked-service-properties) のリンクされたサービスの名前を指定します。これは、中間ステージング ストアとして使用する Storage のインスタンスを表します。 <br/><br/> PolyBase を使用してデータを SQL Data Warehouse に読み込むために、Shared Access Signature を持つ Storage を使用することはできません。 それ以外のすべてのシナリオでは使用できます。 |該当なし |はい ( **enableStaging** が TRUE に設定されている場合) |
| path |ステージング データを格納する Blob Storage のパスを指定します。 パスを指定しないと、一時データを格納するコンテナーがサービスによって作成されます。 <br/><br/> パスを指定するのは、Shared Access Signature を持つ Storage を使用する場合、または一時データを特定の場所に保存する必要がある場合のみです。 |該当なし |いいえ |
| enableCompression |データをコピーする前に圧縮するかどうかを指定します。 この設定により、転送するデータの量が減ります。 |False |いいえ |

>[!NOTE]
> 圧縮を有効にしてステージング コピーを使用する場合、サービスとリンクされたステージング Blob でのサービス プリンシパルと MSI 認証はサポートされません。

上の表に記載されているプロパティを持つコピー アクティビティの定義の例を次に示します。

```json
"activities":[
    {
        "name": "Sample copy activity",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path",
                "enableCompression": true
            }
        }
    }
]
```

#### <a name="staged-copy-billing-impact"></a>ステージング コピーの課金への影響

コピーの期間とコピーの種類という 2 つのステップに基づいて課金されます。

* クラウド コピー (クラウド データ ストアから別のクラウド データ ストアへのデータのコピー、どちらのステージも Azure 統合ランタイムで強化されている) でステージングを使用する場合、料金は、"ステップ 1 とステップ 2 のコピー時間の合計" x "クラウド コピーの単価" で計算されます。
* ハイブリッド コピー (オンプレミス データ ストアからクラウド データ ストアへのデータのコピー、1 つのステージがセルフホステッド統合ランタイムで強化されている) でステージングを使用する場合、料金は、"ハイブリッド コピーの時間" x "ハイブリッド コピーの単価" + "クラウド コピーの時間" x "クラウド コピーの単価" で計算されます。

## <a name="references"></a>参照

ここでは、サポートされているいくつかのデータ ストアについて、パフォーマンスの監視とチューニングに関するリファレンス情報をいくつか示します。

* Azure Storage (Blob Storage と Table Storage を含む):[Azure Storage のスケーラビリティのターゲット](../storage/common/storage-scalability-targets.md)に関する記事と [Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../storage/common/storage-performance-checklist.md)に関する記事。
* Azure SQL Database:[パフォーマンスを監視](../sql-database/sql-database-single-database-monitor.md)し、データベース トランザクション ユニット (DTU) の割合を確認できます。
* Azure SQL Data Warehouse: その機能は、データ ウェアハウス単位 (DWU) で測定されます。 「[Azure SQL Data Warehouse のコンピューティング能力の管理 (概要)](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)」を参照してください。
* Azure Cosmos DB:[Azure Cosmos DB のパフォーマンス レベル](../cosmos-db/performance-levels.md)。
* オンプレミスの SQL Server: [パフォーマンスの監視とチューニング](https://msdn.microsoft.com/library/ms189081.aspx)。
* オンプレミスのファイル サーバー: [ファイル サーバーのパフォーマンス チューニング](https://msdn.microsoft.com/library/dn567661.aspx)。

## <a name="next-steps"></a>次の手順
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [Azure Data Factory を使用してデータ レイクまたはデータ ウェアハウスから Azure にデータを移行する](data-migration-guidance-overview.md)
- [Amazon S3 から Azure Storage にデータを移行する](data-migration-guidance-s3-azure-storage.md)
