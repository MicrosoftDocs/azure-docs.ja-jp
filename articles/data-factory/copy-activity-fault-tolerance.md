---
title: "Azure Data Factory のコピー アクティビティのフォールト トレランス | Microsoft Docs"
description: "互換性のない行をスキップすることによって Azure Data Factory のコピー アクティビティにフォールト トレランスを追加する方法について説明します。"
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
ms.date: 09/05/2017
ms.author: jingwang
ms.openlocfilehash: d96c89ed3650c09ac6465e30754ef1155b06d601
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
#  <a name="fault-tolerance-of-copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティのフォールト トレランス
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-copy-activity-fault-tolerance.md)
> * [バージョン 2 - プレビュー](copy-activity-fault-tolerance.md)

Azure Data Factory のコピー アクティビティには、ソースとシンク データ ストアの間でデータをコピーするときに互換性のない行を処理するための 2 つの方法が用意されています。

- 互換性のないデータが検出されたときに、コピー アクティビティを中止して停止させることができます (既定の動作)。
- フォールト トレランスを追加して互換性のないデータ行をスキップすることで、すべてのデータのコピーを続行できます。 また、Azure Blob Storage 内の互換性のない行をログに記録することもできます。 そうすることで、ログを調査して失敗の原因を確認し、データ ソースの問題のあるデータを修正してから、コピー アクティビティをもう一度実行できます。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、「[copy activity fault tolerance in V1 (V1 でのコピー アクティビティのフォールト トレランス)](v1/data-factory-copy-activity-fault-tolerance.md)」を参照してください。


 ## <a name="supported-scenarios"></a>サポートされるシナリオ
コピー アクティビティでは、検出、スキップ、および互換性のないデータのログ記録の 3 つのシナリオをサポートします。

- **ソース データの型とシンクのネイティブ型の間の非互換性**。 <br/><br/> 例: BLOB ストレージ内の CSV ファイルから、INT 型の 3 つの列を含むスキーマ定義を持つ SQL データベースにデータをコピーします。 数値データを含む CSV ファイルの行 (123,456,789 など) はシンク ストアに正常にコピーされます。 ただし、数値以外を含む行 (123,456, abc など) は互換性のない行として検出され、スキップされます。
- **ソースとシンクの間での列数の不一致**。 <br/><br/> 例: 6 つの列を含むスキーマ定義とともに、Blob Storage 内の CSV ファイルから SQL データベースにデータをコピーします。 6 つの列を含む CSV ファイルの行が、シンク ストアに正常にコピーされます。 含まれる列の数が 6 つでない CSV ファイルの行は、互換性のないものとして検出され、スキップされます。
- **リレーショナル データベースへの書き込み時の主キー違反**。<br/><br/> 例: データを SQL サーバーから SQL データベースにコピーします。 シンクの SQL データベースでは主キーが定義されていますが、ソースの SQL サーバーではそのような主キーは定義されていません。 ソースに存在する重複している行は、シンクにはコピーできません。 コピー アクティビティでは、ソース データの最初の行のみがシンクにコピーされます。 それ以降のソース行に重複している主キーの値が含まれている場合、互換性のないものとして検出され、スキップされます。

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
              "referenceName": "AzureBlobLinkedService",
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
既定のコンテナー | スキップされる行が含まれたログを格納する Azure Storage のリンク サービス。 | AzureStorage または AzureStorageSas リンクされたサービスの名前。これは、ログ ファイルを格納するために使用するストレージ インスタンスを示します。 | いいえ
path | スキップした行を含むログ ファイルのパス。 | 互換性のないデータをログに記録する Blob Storage のパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ

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

ログ ファイルでは、スキップされた行および非互換性の原因を確認できます。

ログ ファイルには元のデータと対応するエラーが記録されています。 ログ ファイルの内容の例は次のとおりです。

```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>次のステップ
コピー アクティビティの他の記事を参照してください。

- [コピー アクティビティの概要](copy-activity-overview.md)
- [コピー アクティビティのパフォーマンス](copy-activity-performance.md)


