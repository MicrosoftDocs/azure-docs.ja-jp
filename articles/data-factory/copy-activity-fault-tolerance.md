---
title: Azure Data Factory のコピー アクティビティのフォールト トレランス
description: 互換性のない行をスキップすることによって Azure Data Factory のコピー アクティビティにフォールト トレランスを追加する方法について説明します。
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: yexu
ms.openlocfilehash: b874c0dcc1f394866e74b45e6bc335a25ce24499
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930013"
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティのフォールト トレランス
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-copy-activity-fault-tolerance.md)
> * [現在のバージョン](copy-activity-fault-tolerance.md)

Azure Data Factory のコピー アクティビティには、ソースとシンク データ ストアの間でデータをコピーするときに互換性のない行を処理するための 2 つの方法が用意されています。

- 互換性のないデータが検出されたときに、コピー アクティビティを中止して停止させることができます (既定の動作)。
- フォールト トレランスを追加して互換性のないデータ行をスキップすることで、すべてのデータのコピーを続行できます。 また、Azure Blob Storage または Azure Data Lake Store 内の互換性のない行をログに記録することもできます。 そうすることで、ログを調査して失敗の原因を確認し、データ ソースの問題のあるデータを修正してから、コピー アクティビティをもう一度実行できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ
コピー アクティビティでは、検出、スキップ、および互換性のないデータのログ記録の 3 つのシナリオをサポートします。

- **ソース データの型とシンクのネイティブ型の間の非互換性**。 

    例: Blob Storage 内の CSV ファイルから、INT 型の 3 つの列を含むスキーマ定義を持つ SQL データベースにデータをコピーします。 数値データを含む CSV ファイルの行 (123,456,789 など) はシンク ストアに正常にコピーされます。 ただし、数値以外を含む行 (123,456, abc など) は互換性のない行として検出され、スキップされます。

- **ソースとシンクの間での列数の不一致**。

    例: Blob Storage 内の CSV ファイルから、6 つの列を含むスキーマ定義を持つ SQL データベースにデータをコピーします。 6 つの列を含む CSV ファイルの行が、シンク ストアに正常にコピーされます。 含まれる列の数が 6 つでない CSV ファイルの行は、互換性のないものとして検出され、スキップされます。

- **SQL Server/Azure SQL Database/Azure Cosmos DB への書き込み時の主キー違反**。

    例: データを SQL サーバーから SQL データベースにコピーします。 シンクの SQL データベースでは主キーが定義されていますが、ソースの SQL サーバーではそのような主キーは定義されていません。 ソースに存在する重複している行は、シンクにはコピーできません。 コピー アクティビティでは、ソース データの最初の行のみがシンクにコピーされます。 それ以降のソース行に重複している主キーの値が含まれている場合、互換性のないものとして検出され、スキップされます。

>[!NOTE]
>- PolyBase を使用して SQL Data Warehouse にデータを読み込むには、コピー アクティビティの拒否ポリシーで "[polyBaseSettings](connector-azure-sql-data-warehouse.md#azure-sql-data-warehouse-as-sink)" を指定して PolyBase のネイティブ フォールト トレランス設定を構成します。 PolyBase 互換性のない行は、次に示すように、通常どおり Blob または ADLS へのリダイレクトを有効にできます。
>- コピー アクティビティが [Amazon Redshift Unload](connector-amazon-redshift.md#use-unload-to-copy-data-from-amazon-redshift) を呼び出すように構成されている場合、この機能は適用されません。
>- コピー アクティビティが [SQL シンクからストアド プロシージャ](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#invoke-a-stored-procedure-from-a-sql-sink)を呼び出すように構成されている場合、この機能は適用されません。

## <a name="configuration"></a>構成
次の例では、コピー アクティビティで互換性のない行をスキップするように構成する JSON 定義について説明します。

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },
    "enableSkipIncompatibleRow": true,
    "redirectIncompatibleRowSettings": {
         "linkedServiceName": {
              "referenceName": "<Azure Storage or Data Lake Store linked service>",
              "type": "LinkedServiceReference"
            },
            "path": "redirectcontainer/erroroutput"
     }
}
```

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | -------- 
enableSkipIncompatibleRow | コピー中に互換性のない行をスキップするかどうかを指定します。 | True<br/>False (既定値) | いいえ
redirectIncompatibleRowSettings | 互換性のない行をログに記録するときに指定できるプロパティのグループ。 | &nbsp; | いいえ
linkedServiceName | スキップされる行が含まれたログを格納する [Azure Storage](connector-azure-blob-storage.md#linked-service-properties) または [Azure Data Lake Store](connector-azure-data-lake-store.md#linked-service-properties) のリンク サービス。 | `AzureStorage` または `AzureDataLakeStore` 型のリンクされたサービスの名前。これは、ログ ファイルを格納するために使用するインスタンスを示します。 | いいえ
path | スキップした行を含むログ ファイルのパス。 | 互換性のないデータをログに記録するパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ

## <a name="monitor-skipped-rows"></a>スキップされた行を監視する
コピー アクティビティの実行が完了したら、コピー アクティビティの出力で、スキップされた行の数を次のように確認できます。

```json
"output": {
            "dataRead": 95,
            "dataWritten": 186,
            "rowsCopied": 9,
            "rowsSkipped": 2,
            "copyDuration": 16,
            "throughput": 0.01,
            "redirectRowPath": "https://myblobstorage.blob.core.windows.net//myfolder/a84bf8d4-233f-4216-8cb5-45962831cd1b/",
            "errors": []
        },

```
互換性のない行をログに記録するように構成した場合は、パス `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` でログ ファイルを見つけることができます。 

ログ ファイルは、csv のファイル形式のみです。 スキップされた元のデータは、必要に応じて、列区切り記号にコンマを使用して記録されます。 非互換性の根本原因を確認できるように、ログ ファイルの元のソース データに加えて、"ErrorCode" および "ErrorMessage" という 2 つの列を追加しています。 ErrorCode および ErrorMessage は、二重引用符で囲まれます。 

ログ ファイルの内容の例は次のとおりです。

```
data1, data2, data3, "UserErrorInvalidDataValue", "Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'."
data4, data5, data6, "2627", "Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4)."
```

## <a name="next-steps"></a>次の手順
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)


