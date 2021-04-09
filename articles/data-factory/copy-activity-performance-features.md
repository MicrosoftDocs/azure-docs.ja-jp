---
title: コピー アクティビティ パフォーマンス最適化機能
description: Azure Data Factory のコピー アクティビティのパフォーマンスを最適化するのに役立つ主な機能について説明します。
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/24/2020
ms.openlocfilehash: ecb4550b218b069273cba2e3d70a9510c1cc74ca
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387803"
---
# <a name="copy-activity-performance-optimization-features"></a>コピー アクティビティ パフォーマンス最適化機能

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory で利用できるコピー アクティビティのパフォーマンス最適化機能について説明します。

## <a name="data-integration-units"></a>データ統合単位

データ統合単位は、Azure Data Factory の 1 つの単位の能力 (CPU、メモリ、ネットワーク リソース割り当ての組み合わせ) を表す尺度です。 データ統合単位は [Azure 統合ランタイム](concepts-integration-runtime.md#azure-integration-runtime)にのみ適用され、[セルフホステッド統合ランタイム](concepts-integration-runtime.md#self-hosted-integration-runtime)には適用されません。

コピー アクティビティの実行を支援するために許可される DIU は **2 から 256** です。 指定しなかった場合、または UI で [自動] を選択した場合、ソースとシンクのペアとデータ パターンに基づいて、最適な DIU 設定が Data Factory によって動的に適用されます。 次の表に、さまざまなコピー シナリオでサポートされている DIU 範囲と既定の動作をリストします。

| コピー シナリオ | サポートされている DIU 範囲 | サービスによって決定される既定の DIU |
|:--- |:--- |---- |
| ファイル ストア間 |- **単一ファイルとの間でコピーする**:2-4 <br>- **複数のファイルとの間でコピーする**:ファイルの数とサイズに応じて 2 〜 256。 <br><br>たとえば、4 つの大きなファイルを含むフォルダーからデータをコピーし、階層を保持することを選択した場合、最大の有効な DIU は 16 です。ファイルのマージを選択すると、最大の有効な DIU は 4 になります。 |ファイルの数とサイズに応じて 4 〜 32。 |
| ファイル ストアから非ファイル ストアへ |- **単一ファイルからコピーする**:2-4 <br/>- **複数のファイルからコピーする**:ファイルの数とサイズに応じて 2 〜 256。 <br/><br/>例えば、4 つの大きなファイルを含むフォルダーからデータをコピーする場合、最大の有効な DIU は 16 です。 |- **Azure SQL Database または Azure Cosmos DB**: シンク層 (Dtu/Ru) とソース ファイルのパターンに応じて 4 ～ 16 の範囲にコピーします。<br>PolyBase または COPY ステートメントを使用して、- **Azure Synapse Analytics にコピーします**。2<br>- その他のシナリオ:4 |
| 非ファイル ストアからファイル ストアへ |- **パーティション オプションが有効なデータ ストア** ([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、[Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、[Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[SQL Server](connector-sql-server.md#sql-server-as-a-source)、[Teradata](connector-teradata.md#teradata-as-source)) からコピーします。フォルダーに書き込む場合は 2-256 で、単一のファイルに書き込む場合は 2-4 です。 ソース データ パーティションごとに最大 4 つの DIU を使用できることに注意してください。<br>- **その他のシナリオ**:2-4 |- **REST または HTTP からコピーする**:1<br/>UNLOAD を使用して - **Amazon Redshift からコピーする**:2<br>- **その他のシナリオ**:4 |
| 非ファイル ストア間 |- **パーティション オプションが有効なデータ ストア** ([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、[Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、[Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[SQL Server](connector-sql-server.md#sql-server-as-a-source)、[Teradata](connector-teradata.md#teradata-as-source)) からコピーします。フォルダーに書き込む場合は 2-256 で、単一のファイルに書き込む場合は 2-4 です。 ソース データ パーティションごとに最大 4 つの DIU を使用できることに注意してください。<br/>- **その他のシナリオ**:2-4 |- **REST または HTTP からコピーする**:1<br>- **その他のシナリオ**:4 |

コピー アクティビティの監視ビューまたはアクティビティの出力で、各コピー実行に使用される DIU を確認できます。 詳細については、[コピー アクティビティ監視](copy-activity-monitoring.md)に関するページを参照してください。 この既定の動作をオーバーライドするには、`dataIntegrationUnits` プロパティに次のように値を指定します。 コピー操作が実行時に使用する *DIU の実際の数* は、データ パターンに応じて、構成されている値以下になります。

**使用済み DIU の数 \* コピー期間 \* 単価/DIU 時間** が課金されます。 現在の価格については、[こちら](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)を参照してください。 サブスクリプションの種類ごとに、現地通貨と個別の割引が適用される場合があります。

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
            "dataIntegrationUnits": 128
        }
    }
]
```

## <a name="self-hosted-integration-runtime-scalability"></a>セルフホステッド統合ランタイムのスケーラビリティ

より高いスループットを実現するには、セルフホステッド IR をスケールアップするかスケールアウトします。

- セルフホステッド IR ノード上の CPU と使用可能なメモリが十分に活用されていないにもかかわらず、同時ジョブの実行が上限に達する場合は、ノードで実行できる同時実行ジョブの数を増やすことでスケールアップを行う必要があります。  手順については、[こちら](create-self-hosted-integration-runtime.md#scale-up)を参照してください。
- 一方、セルフホステッド IR ノードの CPU が高いか、使用可能なメモリが少ない場合は、新しいノードを追加して、複数のノード間で負荷をスケールアウトすることができます。  手順については、[こちら](create-self-hosted-integration-runtime.md#high-availability-and-scalability)を参照してください。

注次のシナリオでは、単一のコピーアクティビティの実行で、複数の自己ホスト型 IR ノードを利用できることに注意してください。

- ファイルの数とサイズに応じて、ファイルベースのストアからデータをコピーします。
- パーティション オプションが有効なデータ ストア ([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、[Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、[Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[SAP HANA](connector-sap-hana.md#sap-hana-as-source)、[SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、[SAP テーブル](connector-sap-table.md#sap-table-as-source)、[SQL Server](connector-sql-server.md#sql-server-as-a-source)、[Teradata](connector-teradata.md#teradata-as-source) など) からデータをコピーします。

## <a name="parallel-copy"></a>並列コピー

コピー アクティビティで並列コピー（`parallelCopies` プロパティ）を設定し、コピー アクティビティで使用する並列処理を指示できます。 このプロパティは、並列でソースから読み取る、またはシンク データ ストアに書き込むコピー アクティビティ内の最大スレッド数と見なすことができます。

並列コピーは、[データ統合ユニット](#data-integration-units) または [セルフホステッド IR ノード](#self-hosted-integration-runtime-scalability) に直交します。 すべての DIU またはセルフホステッド IR ノードでカウントされます。

コピー アクティビティが実行されるたびに、Azure Data Factory では、既定で、ソースとシンクのペアおよびデータ パターンに基づいて、最適な並列コピー設定が動的に適用されます。 

> [!TIP]
> 通常、並列コピーの既定の動作では、ソースシンク ペア、データパターン、DIU の数、またはセルフホステッド IR の CPU/メモリ/ノード数に基づいて ADF によって自動決定される最適なスループットが得られます。 並列コピーを調整するタイミングについては、「[コピーアクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)」を参照してください。

次の表は、並列コピーの動作をリストしています。

| コピー シナリオ | 並列コピー動作 |
| --- | --- |
| ファイル ストア間 | `parallelCopies` は、**ファイルレベル** での並列処理を決定します。 それぞれのファイル内でのチャンク化は裏で自動的かつ透過的に行われます。 指定されたソース データ ストアの種類に最適なチャンク サイズを使用し、並行してデータを読み込むよう設計されています。 <br/><br/>実行時にコピー アクティビティが使用する並列コピーの実際の数は、存在するファイルの数以下となります。 コピー動作が **mergeFile** をファイルシンクにマージする場合、コピー アクティビティはファイル レベルでの並列処理を活用できません。 |
| ファイル ストアから非ファイル ストアへ | - Azure SQL Database または Azure Cosmos DB にデータをコピーする場合、既定の並列コピーはシンク層 (Dtu/Ru の数) にも依存します。<br>- Azure テーブルにデータをコピーする場合、既定の並列コピーは 4 です。 |
| 非ファイル ストアからファイル ストアへ | - パーティション オプションが有効なデータ ストア ([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、[Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、[Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[SAP HANA](connector-sap-hana.md#sap-hana-as-source)、[SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、[SAP テーブル](connector-sap-table.md#sap-table-as-source)、[SQL Server](connector-sql-server.md#sql-server-as-a-source)、[Teradata](connector-teradata.md#teradata-as-source) など) からデータをコピーする場合、既定の並列コピーは 4 となります。 実行時のコピーアクティビティで使用される並列コピーの実際の数は、所有しているデータ パーティションの数以下になります。 セルフホステッド統合ランタイムを使用して Azure Blob/ADLS Gen2 にコピーする場合は、IR ノードあたりの最大の有効な並列コピー数が 4 または 5 であることに注意してください。<br>- その他のシナリオでは、並列コピーは有効になりません。 並列処理が指定されても、この場合は適用されません。 |
| 非ファイル ストア間 | - Azure SQL Database または Azure Cosmos DB にデータをコピーする場合、既定の並列コピーはシンク層 (Dtu/Ru の数) にも依存します。<br/>- パーティション オプションが有効なデータ ストア ([Azure SQL Database](connector-azure-sql-database.md#azure-sql-database-as-the-source)、[Azure SQL Managed Instance](connector-azure-sql-managed-instance.md#sql-managed-instance-as-a-source)、[Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#azure-synapse-analytics-as-the-source)、[Oracle](connector-oracle.md#oracle-as-source)、[Netezza](connector-netezza.md#netezza-as-source)、[SAP HANA](connector-sap-hana.md#sap-hana-as-source)、[SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)、[SAP テーブル](connector-sap-table.md#sap-table-as-source)、[SQL Server](connector-sql-server.md#sql-server-as-a-source)、[Teradata](connector-teradata.md#teradata-as-source) など) からデータをコピーする場合、既定の並列コピーは 4 となります。<br>- Azure テーブルにデータをコピーする場合、既定の並列コピーは 4 です。 |

お使いのデータ ストアをホストしているマシンの負荷を制御したり、コピーのパフォーマンスをチューニングしたりするには、規定値をオーバーライドし、`parallelCopies` プロパティの値を指定することができます。 値は 1 以上の整数でなければなりません。 実行時にコピー アクティビティは、設定された値以下でパフォーマンスが最大になる値を使用します。

`parallelCopies` プロパティに値を指定するとき、コピー元データ ストアとシンク データ ストアの負荷増加を考慮してください。 また、セルフホステッド統合ランタイムによってコピー アクティビティが支援される場合、セルフホステッド統合ランタイムの負荷増加を考慮してください。 この負荷増加は、特に複数のアクティビティがある場合や、同じデータ ストアに対して実行される同じアクティビティの同時実行がある場合に発生します。 データ ストアまたはセルフホステッド統合ランタイムの負荷の上限に達したことがわかった場合は、`parallelCopies` の値を減らし、負荷を軽減してください。

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

## <a name="staged-copy"></a>ステージング コピー

ソース データ ストアからシンク データ ストアにデータをコピーする場合、中間ステージング ストアとして Azure Blob Storage または Azure Data Lake Storage Gen2 を使用することを選択できます。 ステージングは、特に次のような場合に役立ちます。

- **PolyBase を介してさまざまなデータ ストアから Azure Synapse Analytics にデータを取り込む、Snowflake との間でデータをコピーする、または Amazon Redshift および HDFS からデータを効率的に取り込む。** 詳細については以下を参照してください。
  - PolyBase を使用して Azure Synapse Analytics にデータを[読み込む](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics)
  - [Snowflake コネクタ](connector-snowflake.md)
  - [Amazon Redshift コネクタ](connector-amazon-redshift.md)
  - [HDFS コネクタ](connector-hdfs.md)
- **企業の IT ポリシーが理由で、ファイアウォールでポート 80 とポート 443 以外のポートを開きたくない。** たとえば、オンプレミスのデータ ストアから Azure SQL Database または Azure Synapse Analytics にデータをコピーする場合、Windows ファイアウォールと会社のファイアウォールの両方で、ポート 1433 の送信 TCP 通信を有効にする必要があります。 このシナリオでは、ステージング コピーにセルフホステッド統合ランタイムを利用し、まずポート 443 で HTTP または HTTPS を介してステージング ストレージにデータをコピーし、次にステージングから SQL Database または Azure Synapse Analytics にデータを読み込むことができます。 このフローでは、ポート 1433 を有効にする必要はありません。
- **ネットワーク接続が遅い場合、ハイブリッド データ移動 (オンプレミス データ ストアからクラウド データ ストアへのコピー) の実行に少し時間がかかる場合がある。** パフォーマンスを向上させるため、ステージング コピーを使用してオンプレミスのデータを圧縮することで、クラウド内のステージング データ ストアにデータを移動する時間を短縮できます。 その後、データは、ターゲット データ ストアに読み込む前に、ステージング ストアで圧縮を解除できます。

### <a name="how-staged-copy-works"></a>ステージング コピーのしくみ

ステージング機能をアクティブにすると、まずデータがソース データ ストアからステージング ストレージにコピーされます (ご自分の Azure Blob または Azure Data Lake Storage Gen2 が使用されます)。 次に、データはステージングからシンクのデータ ストアにコピーされます。 Azure Data Factory のコピー アクティビティを使用すると、2 段階のフローが自動的に管理され、データの移動が完了した後は、ステージング ストレージから一時データがクリーンアップされます。

![ステージング コピー](media/copy-activity-performance/staged-copy.png)

ステージング ストアを使用したデータ移動をアクティブにすると、ソース データ ストアからステージング ストアにデータを移動する前にデータを圧縮し、中間データ ストアまたはステージング データ ストアからシンク データ ストアにデータを移動する前に圧縮を解除するかどうかを指定できます。

現在のところ、ステージング コピーの使用に関係なく、異なるセルフホステッド統合ランタイムで接続されている 2 つのデータ ストア間でデータをコピーできません。 このようなシナリオの場合、コピー元からステージングにコピーし、その後、ステージングからシンクにコピーするよう、明示的につながれた 2 つのコピー アクティビティを構成できます。

### <a name="configuration"></a>構成

コピー アクティビティの **enableStaging** 設定を構成して、目的のデータ ストアに読み込む前にデータをストレージにステージングするかどうかを指定します。 **enableStaging** を `TRUE` に設定した場合は、次の表に記載されている追加のプロパティを指定する必要があります。 

| プロパティ | 説明 | 既定値 | 必須 |
| --- | --- | --- | --- |
| enableStaging |中間ステージング ストアを経由してデータをコピーするかどうかを指定します。 |False |いいえ |
| linkedServiceName |[Azure Blob ストレージ](connector-azure-blob-storage.md#linked-service-properties)または [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) リンク サービスの名前を指定します。これは、中間ステージング ストアとして使用する Storage のインスタンスを示します。 |該当なし |はい ( **enableStaging** が TRUE に設定されている場合) |
| path |ステージング データを格納するパスを指定します。 パスを指定しないと、一時データを格納するコンテナーがサービスによって作成されます。 |該当なし |いいえ |
| enableCompression |データをコピーする前に圧縮するかどうかを指定します。 この設定により、転送するデータの量が減ります。 |False |いいえ |

>[!NOTE]
> 圧縮を有効にしてステージング コピーを使用する場合、サービスとリンクされたステージング Blob でのサービス プリンシパルと MSI 認証はサポートされません。

上の表に記載されているプロパティを持つコピー アクティビティの定義の例を次に示します。

```json
"activities":[
    {
        "name": "CopyActivityWithStaging",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
            },
            "sink": {
                "type": "SqlDWSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                },
                "path": "stagingcontainer/path"
            }
        }
    }
]
```

### <a name="staged-copy-billing-impact"></a>ステージング コピーの課金への影響

コピーの期間とコピーの種類という 2 つのステップに基づいて課金されます。

* クラウド コピー (クラウド データ ストアから別のクラウド データ ストアへのデータのコピー、どちらのステージも Azure 統合ランタイムで強化されている) でステージングを使用する場合、料金は、"ステップ 1 とステップ 2 のコピー時間の合計" x "クラウド コピーの単価" で計算されます。
* ハイブリッド コピー (オンプレミス データ ストアからクラウド データ ストアへのデータのコピー、1 つのステージがセルフホステッド統合ランタイムで強化されている) でステージングを使用する場合、料金は、"ハイブリッド コピーの時間" x "ハイブリッド コピーの単価" + "クラウド コピーの時間" x "クラウド コピーの単価" で計算されます。

## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド](copy-activity-performance.md)
- [コピー アクティビティのパフォーマンスのトラブルシューティング](copy-activity-performance-troubleshooting.md)
- [Azure Data Factory を使用してデータ レイクまたはデータ ウェアハウスから Azure にデータを移行する](data-migration-guidance-overview.md)
- [Amazon S3 から Azure Storage にデータを移行する](data-migration-guidance-s3-azure-storage.md)