---
title: Azure Data Factory のコピー アクティビティからストアド プロシージャを呼び出す
description: Azure Data Factory のコピー アクティビティから、Azure SQL Database または SQL Server のストアド プロシージャを呼び出す方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924099"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Azure Data Factory のコピー アクティビティからのストアド プロシージャの呼び出し
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[Data Factory でのストアド プロシージャ アクティビティを使用したデータ変換](../transform-data-using-stored-procedure.md)に関するページを参照してください。


データを [SQL Server](data-factory-sqlserver-connector.md) または [Azure SQL Database](data-factory-azure-sql-connector.md) にコピーするときに、ストアド プロシージャを呼び出すように、コピー アクティビティで **SqlSink** を構成することができます。 ストアド プロシージャを使用して、データを対象テーブルに挿入する前に、必要な追加処理 (列の結合、追加の値の検索、複数のテーブルへの挿入など) を実行することもできます。 この機能は、[テーブル値パラメーター](https://msdn.microsoft.com/library/bb675163.aspx)を利用しています。 

次の例は、Data Factory パイプライン (コピー アクティビティ) から SQL Server データベースのストアド プロシージャを呼び出す方法を示しています。  

## <a name="output-dataset-json"></a>出力データセット JSON
出力データセットの JSON で、**type** を **SqlServerTable** に設定します。 Azure SQL データベースで使用するには、**AzureSqlTable** に設定します。 **tableName** プロパティの値は、ストアド プロシージャの最初のパラメーターの名前と一致する必要があります。  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>コピー アクティビティ JSON の SqlSink セクション
次のように、コピー アクティビティ JSON の **SqlSink** セクションを定義します。 シンク/対象データベースにデータを挿入しているときにストアド プロシージャを呼び出すには、**SqlWriterStoredProcedureName** プロパティと **SqlWriterTableType** プロパティの両方の値を指定します。 このプロパティについては、[SQL Server コネクタに関する記事の「SqlSink」セクション](data-factory-sqlserver-connector.md#sqlsink)を参照してください。

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>ストアド プロシージャの定義 
データベース内で、**SqlWriterStoredProcedureName** と同じ名前のストアド プロシージャを定義します。 ストアド プロシージャは、ソース データ ストアから入力データを処理して、対象データベースのテーブルにデータを挿入します。 ストアド プロシージャの最初のパラメーター名は、データセット JSON (Marketing) で定義された tableName と同じにする必要があります。

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>テーブル型の定義
データベースで、**SqlWriterTableType** と同じ名前のテーブル型を定義します。 テーブル型のスキーマは、入力データセットのスキーマと同じにする必要があります。

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>次の手順
完全な JSON の例については、次のコネクタに関する記事を参照してください。 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
