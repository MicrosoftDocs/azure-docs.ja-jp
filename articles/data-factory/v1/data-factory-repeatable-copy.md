---
title: Azure Data Factory での反復可能なコピー
description: データをコピーするスライスが複数回実行されたときに重複を回避する方法について説明します。
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
ms.openlocfilehash: 7188cb5774699fc6e31fc3b8c78068bb33c6f552
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929008"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Azure Data Factory での反復可能なコピー

## <a name="repeatable-read-from-relational-sources"></a>リレーショナル ソースからの反復可能な読み取り
リレーショナル データ ストアからデータをコピーする場合は、意図しない結果を避けるため、再現性に注意する必要があります。 Azure Data Factory では、スライスを手動で再実行できます。 障害が発生したときにスライスを再実行できるように、データセットの再試行ポリシーを構成することもできます。 いずれかの方法でスライスが再実行された際は、何度スライスが実行されても同じデータが読み込まれることを確認する必要があります。  
 
> [!NOTE]
> 以下の例は Azure SQL 向けですが、四角形のデータセットをサポートする任意のデータ ストアに適用できます。 ソースの**種類**とデータ ストアの **query** プロパティ (例: sqlReaderQuery の代わりに query) の調整が必要になる場合があります。   

通常は、リレーショナル ストアからの読み取り時に、そのスライスに対応するデータのみを読み込む必要があります。 これを行うには、Azure Data Factory で使用可能な、WindowStart と WindowEnd システム変数を使用します。 Azure Data Factory の変数と関数については、「[Azure Data Factory - 関数およびシステム変数](data-factory-functions-variables.md)」を参照してください。 例: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
このクエリでは、スライス期間の範囲内 (WindowStart -> WindowEnd) にあるデータを MyTable テーブルから読み取ります。 このスライスを再実行すると、常に同じデータが読み取られます。 

その他の場合は、次のようにテーブル全体を読み取って、sqlReaderQuery を定義できます。

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>SqlSink への反復可能な書き込み
他のデータ ストアから **Azure SQL/SQL Server** にデータをコピーする場合、意図しない結果を回避するために、再現性の維持を考慮する必要があります。 

Azure SQL/SQL Server Database にデータをコピーすると、既定では、コピー アクティビティによりデータがシンク テーブルに付加されます。 たとえば、2 つのレコードを含む CSV (コンマ区切り値) ファイルのデータを、Azure SQL/SQL Server Database の次のテーブルにコピーするとします。 スライスを実行すると、2 つのレコードは SQL テーブルにコピーされます。 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

たとえば、ソース ファイルにエラーが見つかり、Down Tube の数を 2 から 4 に更新したとします。 その期間のデータ スライスを手動で再実行すると、Azure SQL/SQL Server データベースに 2 つの新しいレコードが付加されます。 この例では、テーブルには主キーの制約がある列がないと想定しています。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

この問題を回避するために、次の 2 つのメカニズムのいずれかを使用して、UPSERT セマンティクスを指定する必要があります。

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>メカニズム 1: sqlWriterCleanupScript の使用
**sqlWriterCleanupScript** プロパティを使用すると、スライスの実行時、データを挿入する前に、シンク テーブルからデータをクリーンアップできます。 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

クリーンアップ スクリプトは、スライスの実行時に最初に実行され、そのスライスに対応するデータがSQL テーブルから削除されます。 その後、コピー アクティビティにより、データが SQL テーブルに挿入されます。 スライスを再実行すると、数量が必要に応じて更新されます。

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

たとえば、Flat Washer レコードを元の csv から削除するとします。 次にスライスを再実行すると、次の結果が生成されます。 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

コピー アクティビティでクリーンアップ スクリプトが実行され、そのスライスに対応するデータが削除されました。 次に、csv から入力が読み込まれ (今度は 1 レコードのみが含まれます)、テーブルに挿入されます。 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>メカニズム 2: sliceIdentifierColumnName の使用
> [!IMPORTANT]
> 現在、Azure SQL Data Warehouse では sliceIdentifierColumnName はサポートされていません。 

再現性を実現するための 2 つ目のメカニズムは、対象のテーブルに専用の列 (sliceIdentifierColumnName) を用意する方法です。 この列は、Azure Data Factory がソースと対象の同期状態を保つために使用されます。 この手法は、対象の SQL テーブル スキーマを変更または定義する際に柔軟性がある場合に機能します。 

この列は、再現性のために Azure Data Factory で使用されます。このプロセスで、Azure Data Factory はテーブルのスキーマを変更しません。 この手法を使用する方法:

1. 対象 SQL テーブルで **binary (32)** 型の列を定義します。 この列に制約は設定しないでください。 この例では、この列に AdfSliceIdentifier という名前を付けます。


    ソース テーブル:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    対象テーブル: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. コピー アクティビティで、次のように使用します。
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

ソースと対象の同期状態を維持するために必要なので、Azure Data Factory でこの列が設定されます。 この列の値は、この状況以外で使用されることはありません。 

メカニズム 1 と同様、コピー アクティビティにより、指定したスライスのデータが対象 SQL テーブルからクリーンアップされます。 その後、ソースのデータが対象のテーブルに挿入されます。 

## <a name="next-steps"></a>次の手順
完全な JSON の例については、次のコネクタに関する記事を参照してください。 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
