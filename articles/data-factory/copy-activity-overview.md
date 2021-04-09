---
title: Azure Data Factory のコピー アクティビティ
description: Azure Data Factory のコピー アクティビティについて説明します。 サポートされているソース データ ストアからサポートされているシンク データ ストアにデータをコピーするために使用できます。
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: jingwang
ms.openlocfilehash: d52a0bba5fddaa865b8fad74b778ba7a3838b2a4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387905"
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティ

> [!div class="op_single_selector" title1="使用している Data Factory のバージョンを選択してください。"]
> * [Version 1](v1/data-factory-data-movement-activities.md)
> * [現在のバージョン](copy-activity-overview.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory では、コピー アクティビティを使用して、オンプレミスにあるデータ ストアやクラウド内のデータ ストアの間でデータをコピーできます。 データをコピーした後は、他のアクティビティを使用してさらに変換および分析できます。 また、コピー アクティビティを使用して、変換や分析の結果を発行し、ビジネス インテリジェンス (BI) やアプリケーションで使用することもできます。

![コピー アクティビティの役割](media/copy-activity-overview/copy-activity.png)

コピー アクティビティは、[統合ランタイム](concepts-integration-runtime.md)で実行されます。 さまざまなデータ コピーのシナリオで、さまざまな種類の統合ランタイムを使用できます。

* 任意の IP からインターネット経由でパブリックにアクセスできる 2 つのデータ ストア間でデータをコピーする場合は、コピー アクティビティに Azure 統合ランタイムを使用できます。 この統合ランタイムは、セキュリティで保護され、信頼性が高く、スケーラブルで、[グローバルに利用できます](concepts-integration-runtime.md#integration-runtime-location)。
* オンプレミス、またはアクセス制御を使用するネットワーク (Azure 仮想ネットワークなど) に配置されているデータ ストアとの間でデータをコピーする場合は、セルフホステッド統合ランタイムを設定する必要があります。

統合ランタイムを各ソースおよびシンク データ ストアに関連付ける必要があります。 使用する統合ランタイムをコピー アクティビティで判別する方法の詳細については、「[使用する IR の判別](concepts-integration-runtime.md#determining-which-ir-to-use)」を参照してください。

ソースからシンクにデータをコピーするために、コピー アクティビティを実行するサービスでは次の手順が実行されます。

1. ソース データ ストアからデータを読み取る。
2. シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピングなどを実行する。 この操作は、入力データセット、出力データセット、およびコピー アクティビティの構成に基づいて実行されます。
3. シンク/宛先データ ストアにデータを書き込む。

![コピー アクティビティの概要](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>サポートされるデータ ストアと形式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>サポートされるファイル形式

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

コピー アクティビティを使用すると、ファイル ベースの 2 つのデータ ストア間でファイルをそのままコピーできます。その場合、データはシリアル化または逆シリアル化なしで効率的にコピーされます。 また、特定の形式のファイルを解析または生成することもできます。たとえば、次のような操作を実行できます。

* SQL Server データベースからデータをコピーし、Parquet 形式で Azure Data Lake Storage Gen2 に書き込む。
* オンプレミスのファイル システムからテキスト (CSV) 形式でファイルをコピーし、Azure BLOB ストレージに Avro 形式で書き込む。
* オンプレミスのファイル システムから zip 形式のファイルをコピーし、その場で圧縮解除して、抽出されたファイルを Azure Data Lake Storage Gen2 に書き込む。
* Azure BLOB ストレージから Gzip 圧縮テキスト (CSV) 形式でデータをコピーし、Azure SQL Database に書き込む。
* シリアル化/逆シリアル化または圧縮/展開を必要とする他の多くのアクティビティ。

## <a name="supported-regions"></a>サポートされているリージョン

コピー アクティビティが有効なサービスは、「[統合ランタイムの場所](concepts-integration-runtime.md#integration-runtime-location)」に記載されているリージョンと場所でグローバルに使うことができます。 グローバルに使用できるトポロジでは効率的なデータ移動が保証されます。このデータ移動では、通常、リージョンをまたがるホップが回避されます。 特定のリージョンで Data Factory とデータ移動を利用できるかどうかを確認するには、[リージョン別の製品](https://azure.microsoft.com/regions/#services)に関する記事を参照してください。

## <a name="configuration"></a>構成

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

一般的に、Azure Data Factory でコピー アクティビティを使用するには、次のことを行う必要があります。

1. **ソース データ ストアとシンク データ ストアのリンクされたサービスを作成します。** サポートされるコネクタの一覧については、この記事の「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」セクションを参照してください。 構成情報とサポートされるプロパティについては、コネクタの記事のリンクされたサービスのプロパティに関するセクションを参照してください。 
2. **ソースとシンクのデータセットを作成します。** 構成情報とサポートされるプロパティについては、ソースとシンク コネクタの記事のデータセットのプロパティに関するセクションを参照してください。
3. **コピー アクティビティを含むパイプラインを作成します。** 次のセクションでは、例を示します。

### <a name="syntax"></a>構文

次のコピー アクティビティのテンプレートは、サポートされるすべてのプロパティの一覧を示しています。 実際のシナリオに適したものを指定してください。

```json
"activities":[
    {
        "name": "CopyActivityTemplate",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<source dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<sink dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                <properties>
            },
            "sink": {
                "type": "<sink type>"
                <properties>
            },
            "translator":
            {
                "type": "TabularTranslator",
                "columnMappings": "<column mapping>"
            },
            "dataIntegrationUnits": <number>,
            "parallelCopies": <number>,
            "enableStaging": true/false,
            "stagingSettings": {
                <properties>
            },
            "enableSkipIncompatibleRow": true/false,
            "redirectIncompatibleRowSettings": {
                <properties>
            }
        }
    }
]
```

#### <a name="syntax-details"></a>構文の詳細

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティの場合は、`Copy` に設定します。 | はい |
| inputs | ソース データを指すように作成したデータセットを指定します。 コピー アクティビティは、1 つの入力のみをサポートします。 | はい |
| outputs | シンク データを指すように作成したデータセットを指定します。 コピー アクティビティは、1 つの出力のみをサポートします。 | はい |
| typeProperties | コピー アクティビティを構成するプロパティを指定します。 | はい |
| source | データを取得するためのコピー ソースの種類と対応するプロパティを指定します。<br/>詳細については、「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」に記載されているコネクタの記事のコピー アクティビティのプロパティに関するセクションを参照してください。 | はい |
| sink | データを書き込むためのコピー シンクの種類と対応するプロパティを指定します。<br/>詳細については、「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」に記載されているコネクタの記事のコピー アクティビティのプロパティに関するセクションを参照してください。 | はい |
| translator | ソースからシンクへの明示的な列マッピングを指定します。 このプロパティは、既定のコピー動作がニーズに合わない場合に適用されます。<br/>詳細については、「[コピー アクティビティでのスキーマ マッピング](copy-activity-schema-and-type-mapping.md)」を参照してください。 | いいえ |
| dataIntegrationUnits | [Azure 統合ランタイム](concepts-integration-runtime.md)がデータのコピーに使用する機能の量を表す単位を指定します。 これらの単位は、以前はクラウド データ移動単位 (DMU) と呼ばれていました。 <br/>詳細については、「[データ統合単位](copy-activity-performance-features.md#data-integration-units)」を参照してください。 | いいえ |
| parallelCopies | ソースからのデータの読み取り時やシンクへのデータの書き込み時にコピー アクティビティで使用する並列処理を指定します。<br/>詳細については、「[並列コピー](copy-activity-performance-features.md#parallel-copy)」を参照してください。 | いいえ |
| preserve | データのコピー中にメタデータ/ACL を保存するかどうかを指定します。 <br/>詳細については、[メタデータの保存](copy-activity-preserve-metadata.md)に関する記事を参照してください。 |いいえ |
| enableStaging<br/>stagingSettings | ソースからシンクにデータを直接コピーするのではなく、BLOB ストレージに中間データをステージングするかどうかを指定します。<br/>役に立つシナリオと構成の詳細については、「[ステージング コピー](copy-activity-performance-features.md#staged-copy)」を参照してください。 | いいえ |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| ソースからシンクにデータをコピーするときに互換性のない行を処理する方法を選択します。<br/>詳細については、「[フォールト トレランス](copy-activity-fault-tolerance.md)」を参照してください。 | いいえ |

## <a name="monitoring"></a>監視

Azure Data Factory では、コピー アクティビティの実行を、視覚的に監視することも、プログラムによって監視することも可能です。 詳細については、「[コピー アクティビティを監視する](copy-activity-monitoring.md)」を参照してください。

## <a name="incremental-copy"></a>増分コピー

Data Factory を使用すると、ソース データ ストアからシンク データ ストアに差分データを増分コピーできます。 詳細については、[チュートリアルのデータの増分コピー](tutorial-incremental-copy-overview.md)に関する記事を参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング

[コピー アクティビティの監視](copy-activity-monitoring.md)エクスペリエンスは、コピー パフォーマンスの統計をアクティビティの実行ごとに示します。 「[コピー アクティビティのパフォーマンスとスケーラビリティに関するガイド](copy-activity-performance.md)」には、Azure Data Factory でのコピー アクティビティによるデータ移動のパフォーマンスに影響する主な要因が説明されています。 また、テスト時に観察されるパフォーマンス値の一覧が示され、コピー アクティビティのパフォーマンスを最適化する方法も説明されます。

## <a name="resume-from-last-failed-run"></a>前回失敗した実行から再開する

コピー アクティビティでは、ファイル ベースのストア間でバイナリ形式を使用してサイズの大きいファイルをそのままコピーする場合に、ソースからシンクへのフォルダー/ファイル階層を保持することを選択した場合 (Amazon S3 から Azure Data Lake Storage Gen2 にデータを移行する場合など)、前回失敗した実行からの再開をサポートします。 これは、次のファイルベース コネクタに適用されます。[Amazon S3](connector-amazon-simple-storage-service.md)、[Azure Blob](connector-azure-blob-storage.md)、[Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)、[Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)、[Azure File Storage](connector-azure-file-storage.md)、[ファイル システム](connector-file-system.md)、[FTP](connector-ftp.md)、[Google Cloud Storage](connector-google-cloud-storage.md)、[HDFS](connector-hdfs.md)、および [SFTP](connector-sftp.md)。

コピー アクティビティの再開は、次の 2 つの方法で利用できます。

- **アクティビティ レベルの再試行:** コピー アクティビティに再試行回数を設定できます。 パイプラインの実行中に、このコピー アクティビティの実行が失敗した場合、次の自動再試行は最後の試行の失敗ポイントから開始されます。
- **失敗したアクティビティから再実行する:** パイプラインの実行完了後、ADF UI 監視ビューまたはプログラムによって失敗したアクティビティから再実行をトリガーすることもできます。 失敗したアクティビティがコピー アクティビティの場合、パイプラインはそのアクティビティから再実行されるだけでなく、前の実行の失敗ポイントからも再開されます。

    ![コピーの再開](media/copy-activity-overview/resume-copy.png)

いくつかの注意点があります。

- 再開は、ファイル レベルで行われます。 ファイルのコピー時にコピー アクティビティが失敗した場合、次回の実行時に、この特定のファイルが再コピーされます。
- 再開が正常に機能するには、再実行の間でコピー アクティビティの設定を変更しないでください。
- Amazon S3、Azure BLOB、Azure Data Lake Storage Gen2、および Google Cloud Storage からデータをコピーする場合、コピー アクティビティは任意の数のコピーされたファイルから再開できます。 ソースとしてのその他のファイル ベースのコネクタの場合、現在のコピー アクティビティは、限られた数のファイルからの再開をサポートしています。通常は 1 万単位の範囲であり、ファイル パスの長さによって異なります。この数を超えるファイルが再実行中に再コピーされます。

バイナリ ファイル コピー以外の他のシナリオでは、コピー アクティビティの再実行は先頭から開始されます。

## <a name="preserve-metadata-along-with-data"></a>データと共にメタデータを保存する

ソースからシンクへデータをコピーするときに、データ レイクの移行のようなシナリオでは、コピー アクティビティを使用して、メタデータと ACL をデータと共に保存することも選択できます。 詳細については、[メタデータの保存](copy-activity-preserve-metadata.md)に関する記事を参照してください。

## <a name="schema-and-data-type-mapping"></a>スキーマとデータ型のマッピング

コピー アクティビティによってソース データがどのようにシンクにマップされるかについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

## <a name="add-additional-columns-during-copy"></a>コピー中に列を追加する

ソース データ ストアからシンクにデータをコピーするだけでなく、シンクにコピーする追加データ列を追加するように構成することもできます。 次に例を示します。

- ファイルベースのソースからコピーする場合は、相対ファイル パスを、データの取得元ファイルをトレースするための追加列として保存します。
- 指定されたソース列を別の列として複製します。 
- ADF 式を含む列を追加して、パイプライン名/パイプライン ID などの ADF システム変数をアタッチするか、上流アクティビティの出力から他の動的な値を保存します。
- 静的な値を持つ列を、下流の使用ニーズに応じて追加します。

コピー アクティビティ ソース タブの構成は次のとおりです。また、定義されている列名を使用して、通常どおりのコピー アクティビティ [スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)で追加の列をマッピングすることもできます。 

![コピー アクティビティで列を追加する](./media/copy-activity-overview/copy-activity-add-additional-columns.png)

>[!TIP]
>この機能は、最新のデータセット モデルで動作します。 UI にこのオプションが表示されない場合は、新しいデータセットを作成してみてください。

これをプログラムによって構成するには、コピー アクティビティ ソースに `additionalColumns` プロパティを追加します。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| additionalColumns | シンクにコピーするデータ列を追加します。<br><br>`additionalColumns` 配列の各オブジェクトは追加列を表します。 `name` は列名を定義します。また、`value` はその列のデータ値を示します。<br><br>使用できるデータ値:<br>-  **`$$FILEPATH`** -予約済み変数。データセットで指定されたフォルダー パスへのソース ファイルの相対パスが格納されることを示します。 ファイルベースのソースに適用されます。<br>-  **`$$COLUMN:<source_column_name>`** - 予約変数パターンは、指定されたソース列を別の列として複製することを示します<br>- **式**<br>- **静的な値** | いいえ |

**例:**

```json
"activities":[
    {
        "name": "CopyWithAdditionalColumns",
        "type": "Copy",
        "inputs": [...],
        "outputs": [...],
        "typeProperties": {
            "source": {
                "type": "<source type>",
                "additionalColumns": [
                    {
                        "name": "filePath",
                        "value": "$$FILEPATH"
                    },
                    {
                        "name": "newColName",
                        "value": "$$COLUMN:SourceColumnA"
                    },
                    {
                        "name": "pipelineName",
                        "value": {
                            "value": "@pipeline().Pipeline",
                            "type": "Expression"
                        }
                    },
                    {
                        "name": "staticValue",
                        "value": "sampleValue"
                    }
                ],
                ...
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="auto-create-sink-tables"></a>シンク テーブルの自動作成

SQL データベース/Azure Synapse Analytics にデータをコピーするときに、コピー先のテーブルが存在しない場合、コピー アクティビティではソース データに基づいてデータが自動的に作成されます。 これは、データの読み込みと SQL データベース/Azure Synapse Analytics の評価をすぐに開始できるようにすることを目的としています。 データ インジェストが完了したら、必要に応じて、シンク テーブル スキーマを確認して調整できます。

この機能は、任意のソースから以下のシンク データ ストアにデータをコピーする際にサポートされます。 このオプションは、*ADF のオーサリング UI* – > *コピー アクティビティ シンク* – > *[テーブル] オプション* – > *[Auto create table]\(テーブルの自動作成\)* の順に選択するか、またはコピー アクティビティ シンク ペイロードの `tableOption` プロパティを使用して確認できます。

- [Azure SQL Database](connector-azure-sql-database.md)
- [Azure SQL Database マネージド インスタンス](connector-azure-sql-managed-instance.md)
- [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)
- [SQL Server](connector-sql-server.md)

![シンク テーブルの作成](media/copy-activity-overview/create-sink-table.png)

## <a name="fault-tolerance"></a>フォールト トレランス

既定では、ソース データ行がシンク データ行と互換性がない場合、コピー アクティビティでデータのコピーが停止され、エラーが返されます。 コピーを成功させるには、互換性のない行をスキップし、ログに記録し、互換性のあるデータのみをコピーするようにコピー アクティビティを構成します。 詳細については、[コピー アクティビティのフォールト トレランス](copy-activity-fault-tolerance.md)に関する記事を参照してください。

## <a name="data-consistency-verification"></a>データ整合性の検証

ソース ストアからコピー先ストアにデータを移動するとき、Azure Data Factory コピー アクティビティでは、データがソース ストアからコピー先ストアに正常にコピーされただけでなく、ソース ストアとコピー先ストアの間の整合性も確保されていることを確認するための、追加のデータ整合性検証を行うことができます。 データの移動中に整合性のないファイルが検出されたら、コピー アクティビティを中止するか、またはフォールト トレランス設定を有効にして整合性のないファイルをスキップすることで、その他のデータをコピーし続けることができます。 スキップされたファイル名を取得するには、コピー アクティビティでセッション ログ設定を有効にします。 詳細については、「[コピー アクティビティでのデータ整合性の検証](copy-activity-data-consistency.md)」を参照してください。

## <a name="session-log"></a>セッション ログ
コピーされたファイル名をログに記録できます。これにより、コピー アクティビティのセッション ログを確認することで、データがコピー元からコピー先ストアに正常にコピーされたことだけでなく、コピー元とコピー先ストアの間で一貫していることも確認できます。 詳細については、「[コピー アクティビティのセッション ログ](copy-activity-log.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
次のクイック スタート、チュートリアル、およびサンプルを参照してください。

- [ある場所から同じ Azure Blob Storage アカウントの別の場所にデータをコピーする](quickstart-create-data-factory-dot-net.md)
- [Azure Blob Storage から Azure SQL Database にデータをコピーする](tutorial-copy-data-dot-net.md)
- [SQL Server データベースから Azure にデータをコピーする](tutorial-hybrid-copy-powershell.md)
