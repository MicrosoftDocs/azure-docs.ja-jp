---
title: "Azure Data Factory のコピー アクティビティへのフォールト トレランスの追加 - 互換性のない行のスキップ | Microsoft Docs"
description: "互換性のない行をスキップすることで、Azure Data Factory のコピー アクティビティにフォールト トレランスを追加する方法について説明します。"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 7a8c866106f7e2c2538a9cf6c44cb34ddfaa2887
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-fault-tolerance-in-copy-activity-by-skipping-incompatible-rows"></a>互換性のない行をスキップすることによるコピー アクティビティへのフォールト トレランスの追加

Azure Data Factory の[コピー アクティビティ](data-factory-data-movement-activities.md)には、ソースとシンク データ ストアの間でデータをコピーするときの互換性のない行の取り扱いについて、2 つの方法が用意されています。

- 互換性のないデータが検出されたときに、コピー アクティビティを中止して停止させることができます (既定の動作)。
- フォールト トレランスを追加して互換性のないデータ行をスキップすることで、すべてのデータのコピーを続行できます。 また、Azure Blob Storage 内の互換性のない行をログに記録することもできます。 そうすることで、ログを調査して失敗の原因を確認し、データ ソースの問題のあるデータを修正してから、コピー アクティビティをもう一度実行できます。

## <a name="supported-scenarios"></a>サポートされるシナリオ
コピー アクティビティでは、検出、スキップ、および互換性のないデータのログ記録の 3 つのシナリオをサポートします。

- **ソース データ型とシンク ネイティブ型の間に互換性がない**

    例: 3 つの **INT** 型の列を含むスキーマ定義とともに、Blob ストレージ内の CSV ファイルから SQL データベースにデータをコピーします。 `123,456,789` のような数値データを含む CSV ファイルの行が、シンク ストアに正常にコピーされます。 ただし、`123,456,abc` のような数値以外の値を含む行は、互換性のないものとして検出され、スキップされます。

- **ソースとシンクとの間で列の数が一致しない**

    例: 6 つの列を含むスキーマ定義とともに、Blob Storage 内の CSV ファイルから SQL データベースにデータをコピーします。 6 つの列を含む CSV ファイルの行が、シンク ストアに正常にコピーされます。 含まれる列の数が 6 つでない CSV ファイルの行は、互換性のないものとして検出され、スキップされます。

- **リレーショナル データべースに書き込む際の主キー違反**

    例: データを SQL サーバーから SQL データベースにコピーします。 シンクの SQL データベースでは主キーが定義されていますが、ソースの SQL サーバーではそのような主キーは定義されていません。 ソースに存在する重複している行は、シンクにはコピーできません。 コピー アクティビティでは、ソース データの最初の行のみがシンクにコピーされます。 それ以降のソース行に重複している主キーの値が含まれている場合、互換性のないものとして検出され、スキップされます。

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

互換性のない行をログに記録するように構成した場合、ログ ファイルは `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv` のパスにあります。ログ ファイルでは、スキップされた行と互換性がないことの根本原因を確認できます。

ログ ファイルには元のデータと対応するエラーが記録されています。 ログ ファイルの内容の例は次のとおりです。
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).
```

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティについて詳しくは、「[コピー アクティビティを使用したデータの移動](data-factory-data-movement-activities.md)」をご覧ください。