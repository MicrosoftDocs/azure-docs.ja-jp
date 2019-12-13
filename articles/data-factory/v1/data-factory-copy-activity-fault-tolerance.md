---
title: 互換性のない行をスキップすることで、Azure Data Factory のコピー アクティビティにフォールト トレランスを追加する
description: 互換性のない行をスキップすることで、Azure Data Factory のコピー アクティビティにフォールト トレランスを追加する方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8ffaee75154fd5fe025bdb683c89f16799d6e86b
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926148"
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>互換性のない行をスキップすることによるコピー アクティビティへのフォールト トレランスの追加

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください。"]
> * [Version 1](data-factory-copy-activity-fault-tolerance.md)
> * [バージョン 2 (最新バージョン)](../copy-activity-fault-tolerance.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory のコピー アクティビティでのフォールト トレランス](../copy-activity-fault-tolerance.md)に関するページを参照してください。

Azure Data Factory の[コピー アクティビティ](data-factory-data-movement-activities.md)には、ソースとシンク データ ストアの間でデータをコピーするときの互換性のない行の取り扱いについて、2 つの方法が用意されています。

- 互換性のないデータが検出されたときに、コピー アクティビティを中止して停止させることができます (既定の動作)。
- フォールト トレランスを追加して互換性のないデータ行をスキップすることで、すべてのデータのコピーを続行できます。 また、Azure Blob Storage 内の互換性のない行をログに記録することもできます。 そうすることで、ログを調査して失敗の原因を確認し、データ ソースの問題のあるデータを修正してから、コピー アクティビティをもう一度実行できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ
コピー アクティビティでは、検出、スキップ、および互換性のないデータのログ記録の 3 つのシナリオをサポートします。

- **ソース データ型とシンク ネイティブ型の間に互換性がない**

    例: Blob Storage 内の CSV ファイルから、**INT** 型の 3 つの列を含むスキーマ定義を持つ SQL データベースにデータをコピーします。 `123,456,789` のような数値データを含む CSV ファイルの行が、シンク ストアに正常にコピーされます。 ただし、`123,456,abc` のような数値以外の値を含む行は、互換性のないものとして検出され、スキップされます。

- **ソースとシンクとの間で列の数が一致しない**

    例: Blob Storage 内の CSV ファイルから、6 つの列を含むスキーマ定義を持つ SQL データベースにデータをコピーします。 6 つの列を含む CSV ファイルの行が、シンク ストアに正常にコピーされます。 含まれる列の数が 6 つでない CSV ファイルの行は、互換性のないものとして検出され、スキップされます。

- **SQL Server/Azure SQL Database/Azure Cosmos DB への書き込み時の主キー違反**

    例: データを SQL サーバーから SQL データベースにコピーします。 シンクの SQL データベースでは主キーが定義されていますが、ソースの SQL サーバーではそのような主キーは定義されていません。 ソースに存在する重複している行は、シンクにはコピーできません。 コピー アクティビティでは、ソース データの最初の行のみがシンクにコピーされます。 それ以降のソース行に重複している主キーの値が含まれている場合、互換性のないものとして検出され、スキップされます。

>[!NOTE]
>この機能は、コピー アクティビティが [Azure SQL Data Warehouse PolyBase](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) や [Amazon Redshift Unload](data-factory-amazon-redshift-connector.md#use-unload-to-copy-data-from-amazon-redshift) などの外部データ読み込みメカニズムを呼び出すように構成されている場合は適用されません。 PolyBase を使用して SQL Data Warehouse にデータを読み込むには、コピー アクティビティで "[polyBaseSettings](data-factory-azure-sql-data-warehouse-connector.md#sqldwsink)" を指定して PolyBase のネイティブ フォールト トレランス サポートを使用します。

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
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| **enableSkipIncompatibleRow** | コピー中に互換性のない行のスキップを有効にするかどうか。 | True<br/>False (既定値) | いいえ |
| **redirectIncompatibleRowSettings** | 互換性のない行をログに記録するときに指定できるプロパティのグループ。 | &nbsp; | いいえ |
| **linkedServiceName** | スキップされる行が含まれたログを格納する Azure Storage のリンク サービス。 | [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) または [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) リンク サービスの名前。これは、ログ ファイルの格納に使用するストレージ インスタンスです。 | いいえ |
| **path** | スキップした行を含むログ ファイルのパス。 | 互換性のないデータをログに記録する Blob Storage のパスを指定します。 パスを指定しないと、サービスによってコンテナーが作成されます。 | いいえ |

## <a name="monitoring"></a>監視
コピー アクティビティの実行が完了すると、監視セクションでスキップされた行の数を次のように確認できます。

![スキップされた互換性のない行の監視](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

互換性のない行をログに記録するように構成した場合は、パス `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` でログ ファイルを見つけることができます。ログ ファイルでは、スキップされた行および非互換性の原因を確認できます。

ログ ファイルには元のデータと対応するエラーが記録されています。 ログ ファイルの内容の例は次のとおりです。
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティについて詳しくは、「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」をご覧ください。
