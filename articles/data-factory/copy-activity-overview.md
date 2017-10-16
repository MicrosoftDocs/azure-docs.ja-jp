---
title: "Azure Data Factory のコピー アクティビティ | Microsoft Docs"
description: "サポートされるソース データ ストアからサポートされるシンク データ ストアにデータをコピーするために使用できる Azure Data Factory のコピー アクティビティについて説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 784b9489911be0d9a559b6fe7795a6c2cbdb09fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティ

## <a name="overview"></a>概要

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-data-movement-activities.md)
> * [バージョン 2 - プレビュー](copy-activity-overview.md)

Azure Data Factory では、コピー アクティビティを使用して、オンプレミスにあるデータ ストアやクラウド内のデータ ストアの間でデータをコピーできます。 コピーされたデータをさらに変換して分析することができます。 また、コピー アクティビティを使用して、変換や分析の結果を発行し、ビジネス インテリジェンス (BI) やアプリケーションで使用することもできます。

![コピー アクティビティの役割](media/copy-activity-overview/copy-activity.png)

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[Copy Activity in V1 (V1 でのコピー アクティビティ)](v1/data-factory-data-movement-activities.md)」を参照してください。

コピー アクティビティは、[統合ランタイム](concepts-integration-runtime.md)で実行されます。 異なるデータ コピーのシナリオでは、別の種類の統合ランタイムを利用できます。

* どちらもパブリックにアクセス可能なデータ ストア間でデータをコピーする場合は、セキュリティ保護され、信頼性が高く、スケーラブルで、かつ[グローバルに使用可能な](concepts-integration-runtime.md#integration-runtime-location) **Azure 統合ランタイム**でコピー アクティビティを機能強化できます。
* オンプレミスにあるデータ ストアや、アクセス制御が設定されたネットワーク (Azure Virtual Network など) 内のデータ ストアの間でデータをコピーする場合は、データ コピーを機能強化するために**自己ホスト型統合ランタイム**を設定する必要があります。

統合ランタイムを各ソースおよびシンク データ ストアに関連付ける必要があります。 コピー アクティビティが、[使用する IR を決定する](concepts-integration-runtime.md#determining-which-ir-to-use)方法について詳細を学習してください。

コピー アクティビティは、次の各段階を通して、ソースからシンクにデータをコピーします。 コピー アクティビティを実行するサービスが行う処理を次に示します。

1. ソース データ ストアからデータを読み取る。
2. シリアル化/逆シリアル化、圧縮/圧縮解除、列マッピングなどを実行する。この操作は、入力データセット、出力データセット、およびコピー アクティビティの構成に基づいて実行されます。
3. シンク/宛先データ ストアにデータを書き込む。

![コピー アクティビティの概要](media/copy-activity-overview/copy-activity-overview.png)

## <a name="supported-data-stores-and-formats"></a>サポートされるデータ ストアと形式

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

### <a name="supported-file-formats"></a>サポートされるファイル形式

コピー アクティビティを使用すると、ファイル ベースの 2 つのデータ ストア間で**ファイルをそのままコピー**できます。その場合、データはシリアル化/逆シリアル化なしで効率的にコピーされます。

コピー アクティビティではまた、指定された形式 (**テキスト、JSON、Avro、ORC、および Parquet**) のファイルの読み取りと書き込みもサポートされ、圧縮コーデック **GZip、Deflate、BZip2、および ZipDeflate** がサポートされています。 詳細については、「[サポートされているファイル形式と圧縮形式](supported-file-formats-and-compression-codecs.md)」を参照してください。

たとえば、次のようなコピー アクティビティを実行できます。

* オンプレミスの SQL Server のデータをコピーし、Azure Data Lake Store に ORC 形式で書き込む。
* オンプレミスのファイル システムからテキスト (CSV) 形式でファイルをコピーし、Azure BLOB に Avro 形式で書き込む。
* オンプレミスのファイルシステムから zip ファイルをコピーし、圧縮を解除したうえで Azure Data Lake Store 書き込む。
* Azure BLOB から GZip 圧縮テキスト (CSV) 形式でデータをコピーし、Azure SQL Database に書き込む。

## <a name="configuration"></a>構成

Azure Data Factory のコピー アクティビティを使用するには、次のことを行う必要があります。

1. **ソース データ ストアとシンク データ ストアのリンクされたサービスを作成します。** 構成方法およびサポートされるプロパティについては、コネクタの記事の「リンクされたサービスのプロパティ」セクションを参照してください。 サポートされるコネクタの一覧は、「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」セクションで見つけることができます。
2. **ソースとシンクのデータセットを作成します。** 構成方法およびサポートされるプロパティについては、ソースおよびシンク コネクタの記事の「データセットのプロパティ」セクションを参照してください。
3. **コピー アクティビティを含むパイプラインを作成します。** 次のセクションでは、例を示します。  

### <a name="syntax"></a>構文

コピー アクティビティの次のテンプレートは、すべてのサポートされるプロパティの一覧を示しています。 実際のシナリオに適したものを指定してください。

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
                "ColumnMappings": "<column mapping>"
            },
            "cloudDataMovementUnits": <number>,
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

### <a name="syntax-details"></a>構文の詳細

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティの type プロパティは、**Copy** に設定する必要があります。 | あり |
| inputs | ソース データを指す、前に作成したデータセットを指定します。 コピー アクティビティは、1 つの入力のみをサポートします。 | あり |
| outputs | シンク データを指す、前に作成したデータセットを指定します。 コピー アクティビティは、1 つの出力のみをサポートします。 | あり |
| typeProperties | コピー アクティビティを構成するためのプロパティのグループ。 | あり |
| source セクション | コピー ソースの型と、データを取得する方法に関する対応するプロパティを指定します。<br/><br/>「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」に一覧表示されているコネクタの記事にある「コピー アクティビティのプロパティ」セクションで詳細を学習してください。 | あり |
| sink | コピー シンクの型と、データを書き込む方法に関する対応するプロパティを指定します。<br/><br/>「[サポートされるデータ ストアと形式](#supported-data-stores-and-formats)」に一覧表示されているコネクタの記事にある「コピー アクティビティのプロパティ」セクションで詳細を学習してください。 | あり |
| translator | ソースからシンクへの明示的な列マッピングを指定します。 既定のコピー動作が要求を満足できない場合に適用されます。<br/><br/>「[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)」で詳細を学習してください。 | いいえ |
| cloudDataMovementUnits | データ コピーを機能強化するために、[Azure 統合ランタイム](concepts-integration-runtime.md)の強力な機能を指定します。<br/><br/>「[クラウド データ移動単位](copy-activity-performance.md)」で詳細を学習してください。 | いいえ |
| parallelCopies | ソースからのデータの読み取り時やシンクへのデータの書き込み時にコピー アクティビティで使用する並列処理を指定します。<br/><br/>「[並列コピー](copy-activity-performance.md#parallel-copy)」で詳細を学習してください。 | いいえ |
| enableStaging<br/>stagingSettings | ソースからシンクに直接データをコピーするのではなく、中間データを BLOB ストレージにステージングすることを選択します。<br/><br/>役立つシナリオや構成の詳細を「[ステージング コピー](copy-activity-performance.md#staged-copy)」で学習してください。 | いいえ |
| enableSkipIncompatibleRow<br/>redirectIncompatibleRowSettings| ソースからシンクにデータをコピーするときに互換性のない行を処理する方法を選択します。<br/><br/>「[フォールト トレランス](copy-activity-fault-tolerance.md)」で詳細を学習してください。 | いいえ |

## <a name="monitoring"></a>監視

コピー アクティビティの実行の詳細とパフォーマンス特性は、コピー アクティビティの実行結果の [出力] セクションで返されます。 すべての一覧を次に示します。 アクティビティの実行を監視する方法は、[クイック スタートの監視セクション](quickstart-create-data-factory-dot-net.md#monitor-a-pipeline-run)で学習してください。 お客様のシナリオのパフォーマンスと構成を、社内テストから得られたコピー アクティビティの[パフォーマンス リファレンス](copy-activity-performance.md#performance-reference)と比較できます。

| プロパティ名  | Description | 単位 |
|:--- |:--- |:--- |
| dataRead | ソースから読み取られたデータ サイズ | Int64 値 (バイト数) |
| dataWritten | シンクに書き込まれたデータ サイズ | Int64 値 (バイト数) |
| rowsCopied | コピーされた行数 (バイナリ コピーには適用されません)。 | Int64 値 (単位なし) |
| rowsSkipped | スキップされた互換性のない行の数。 この機能は、"enableSkipIncompatibleRow" を true に設定することによって有効にできます。 | Int64 値 (単位なし) |
| throughput | データが転送された速度 | 浮動小数点数 (KB/秒) |
| copyDuration | コピーの持続期間 | Int32 値 (秒数) |
| sqlDwPolyBase | SQL Data Warehouse にデータをコピーするときに PolyBase が使用される場合。 | Boolean |
| redshiftUnload | Redshift からデータをコピーするときに UNLOAD が使用される場合。 | Boolean |
| hdfsDistcp | HDFS からデータをコピーするときに DistCp が使用される場合。 | Boolean |
| effectiveIntegrationRuntime | アクティビティの実行を機能強化するために、どの統合ランタイムが使用されるかを "`<IR name> (<region if it's Azure IR>)`" の形式で示します。 | Text (文字列) |
| usedCloudDataMovementUnits | コピー中の効率的なクラウド データ移動単位。 | Int32 値 |
| redirectRowPath | "redirectIncompatibleRowSettings" で構成した、BLOB ストレージ内のスキップされた互換性のない行のログのパス。 下の例を参照してください。 | Text (文字列) |
| billedDuration | データ移動に対して課金された期間。 | Int32 値 (秒数) |

```json
"output": {
    "dataRead": 1024,
    "dataWritten": 2048,
    "rowsCopies": 100,
    "rowsSkipped": 2,
    "throughput": 1024.0,
    "copyDuration": 3600,
    "redirectRowPath": "https://<account>.blob.core.windows.net/<path>/<pipelineRunID>/",
    "redshiftUnload": true,
    "sqlDwPolyBase": true,
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 8,
    "billedDuration": 28800
}
```

## <a name="schema-and-data-type-mapping"></a>スキーマとデータ型のマッピング

コピー アクティビティがソース データをシンクにマッピングする方法について説明している「[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)」を参照してください。

## <a name="fault-tolerance"></a>フォールト トレランス

既定では、コピー アクティビティはソースとシンクの間に互換性のないデータを検出した場合、データのコピーを停止して失敗を返します。 そのコピーを成功させるために、互換性のない行をスキップしてログに記録し、互換性があるデータのみをコピーするように明示的に構成できます。 詳細については、「[Copy Activity fault tolerance](copy-activity-fault-tolerance.md)」 (コピー アクティビティのフォールト トレランス) を参照してください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング

Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因については、「 [コピー アクティビティのパフォーマンスとチューニングに関するガイド](copy-activity-performance.md)」をご覧ください。 このガイドでは、内部テスト実行時の実際のパフォーマンスを一覧表示すると共に、コピー アクティビティのパフォーマンスを最適化するさまざまな方法についても説明します。

## <a name="next-steps"></a>次のステップ
次のクイック スタート、チュートリアル、およびサンプルを参照してください。

- [Copy data from one location to another location in the same Azure Blob Storage (同じ Azure Blob Storage 内のある場所から別の場所にデータをコピーする)](quickstart-create-data-factory-dot-net.md)
- [Copy data from Azure Blob Storage to Azure SQL Database (Azure Blob Storage から Azure SQL Database にデータをコピーする)](tutorial-copy-data-dot-net.md)
- [Copy data from on-premises SQL Server to Azure (オンプレミスの SQL Server から Azure にデータをコピーする)](tutorial-hybrid-copy-powershell.md)
