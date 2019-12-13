---
title: Azure SQL Data Warehouse との間でデータをコピーする
description: Azure Data Factory を使用して Azure SQL Data Warehouse との間でデータをコピーする方法を説明します
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 18f30af4595a7679d5c3ef56763e992d54fae536
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928064"
---
# <a name="copy-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Azure Data Factory を使用した Azure SQL Data Warehouse との間でのデータのコピー
> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](data-factory-azure-sql-data-warehouse-connector.md)
> * [バージョン 2 (最新バージョン)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 現在のバージョンの Data Factory サービスを使用している場合は、[V2 の Azure SQL Data Warehouse コネクタ](../connector-azure-sql-data-warehouse.md)に関するページを参照してください。

この記事では、Azure Data Factory のコピー アクティビティを使って Azure SQL Data Warehouse との間でデータを移動する方法について説明します。 この記事は、コピー アクティビティによるデータ移動の一般的な概要について説明している、[データ移動アクティビティ](data-factory-data-movement-activities.md)に関する記事に基づいています。

> [!TIP]
> 最高のパフォーマンスを実現するには、PolyBase を使用して、Azure SQL Data Warehouse にデータを読み込みます。 「 [PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) 」セクションに詳細が記載されています。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](data-factory-load-sql-data-warehouse.md)に関するページを参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ
**Azure SQL Data Warehouse から**以下のデータ ストアにデータをコピーできます。

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

以下のデータ ストアから **Azure SQL Data Warehouse に**データをコピーできます。

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> SQL Server または Azure SQL Database から Azure SQL Data Warehouse にデータをコピーするとき、コピー先ストアにテーブルが存在しない場合、Data Factory では、ソース データ ストアのテーブルのスキーマを使用して、SQL Data Warehouse にテーブルを自動的に作成することができます。 詳細については、「[テーブルの自動作成](#auto-table-creation)」を参照してください。

## <a name="supported-authentication-type"></a>サポートされている認証の種類
Azure SQL Data Warehouse コネクタは基本認証をサポートしています。

## <a name="getting-started"></a>使用の開始
さまざまなツールや API を使用して、Azure SQL Data Warehouse との間でデータを移動するコピー アクティビティを含むパイプラインを作成できます。

Azure SQL Data Warehouse との間でデータをコピーするパイプラインを作成する最も簡単な方法は、データのコピー ウィザードを使用することです。 手順については、「[チュートリアル: Azure Data Factory を使用した Azure SQL Data Warehouse へのデータの読み込み](../../sql-data-warehouse/sql-data-warehouse-load-with-data-factory.md)」を参照して、データのコピー ウィザードを使用してパイプラインを作成する簡単な手順を確認してください。

また、次のツールを使用してパイプラインを作成することもできます。**Visual Studio**、**Azure PowerShell**、**Azure Resource Manager テンプレート**、 **.NET API**、**REST API**。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)をご覧ください。

ツールと API のいずれを使用する場合も、次の手順を実行して、ソース データ ストアからシンク データ ストアにデータを移動するパイプラインを作成します。

1. **Data Factory**を作成します。 データ ファクトリには、1 つまたは複数のパイプラインを設定できます。 
2. **リンクされたサービス**を作成し、入力データ ストアと出力データ ストアをデータ ファクトリにリンクします。 たとえば、Azure Blob Storage から Azure SQL Data Warehouse にデータをコピーする場合、リンクされたサービスを 2 つ作成して、Azure ストレージ アカウントと Azure SQL Data Warehouse をデータ ファクトリにリンクします。 Azure SQL Data Warehouse に固有のリンクされたサービスのプロパティについては、「[リンクされたサービスのプロパティ](#linked-service-properties)」セクションをご覧ください。 
3. コピー操作用の入力データと出力データを表す**データセット**を作成します。 最後の手順で説明されている例では、データセットを作成して入力データを含む BLOB コンテナーとフォルダーを指定します。 また、もう 1 つのデータセットを作成して、Blob Storage からコピーされたデータを保持する Azure SQL Data Warehouse のテーブルを指定します。 Azure SQL Data Warehouse に固有のデータセットのプロパティについては、「[データセットのプロパティ](#dataset-properties)」セクションをご覧ください。
4. 入力としてのデータセットと出力としてのデータセットを受け取るコピー アクティビティを含む**パイプライン**を作成します。 前に説明した例では、コピー アクティビティのソースとして BlobSource を、シンクとして SqlDWSink を使います。 同様に、Azure SQL Data Warehouse から Azure Blob Storage にコピーする場合は、SqlDWSource と BlobSink をコピー アクティビティで使います。 Azure SQL Data Warehouse に固有のコピー アクティビティのプロパティについては、「[コピー アクティビティのプロパティ](#copy-activity-properties)」セクションをご覧ください。 ソースまたはシンクとしてデータ ストアを使う方法について詳しくは、前のセクションのデータ ストアのリンクをクリックしてください。

ウィザードを使用すると、Data Factory エンティティ (リンクされたサービス、データセット、パイプライン) に関する JSON の定義が自動的に作成されます。 (.NET API を除く) ツールまたは API を使う場合は、JSON 形式でこれらの Data Factory エンティティを定義します。 Azure SQL Data Warehouse との間でのデータ コピーに使用する Data Factory エンティティの JSON 定義サンプルは、この記事の「[JSON の例](#json-examples-for-copying-data-to-and-from-sql-data-warehouse)」セクションをご覧ください。

以下のセクションでは、Azure SQL Data Warehouse に固有の Data Factory エンティティの定義に使用される JSON プロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ
次の表は、Azure SQL Data Warehouse のリンクされたサービスに固有の JSON 要素の説明をまとめたものです。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| type |type プロパティは、次のように設定する必要があります:**AzureSqlDW** |はい |
| connectionString |connectionString プロパティの Azure SQL Data Warehouse インスタンスに接続するために必要な情報を指定します。 基本認証だけがサポートされています。 |はい |

> [!IMPORTANT]
> [サーバーへのアクセスを Azure サービスに許可する](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)ように [Azure SQL Database ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) とデータベース サーバーを構成します。 さらに、Azure の外部から (たとえば、Data Factory ゲートウェイのあるオンプレミスのデータ ソースから) Azure SQL Data Warehouse にデータをコピーする場合は、Azure SQL Data Warehouse にデータを送信するマシンに適切な IP アドレス範囲を設定します。

## <a name="dataset-properties"></a>データセットのプロパティ
データセットの定義に利用できるセクションとプロパティの完全な一覧については、「[データセットの作成](data-factory-create-datasets.md)」という記事を参照してください。 データセット JSON の構造、可用性、ポリシーなどのセクションは、データセットのすべての型 (Azure SQL、Azure BLOB、Azure テーブルなど) でほぼ同じです。

typeProperties セクションはデータセット型ごとに異なり、データ ストアのデータの場所などに関する情報を提供します。 **AzureSqlDWTable** 型のデータセットの **typeProperties** セクションには次のプロパティがあります。

| プロパティ | 説明 | 必須 |
| --- | --- | --- |
| tableName |リンクされたサービスが参照する Azure SQL Data Warehouse データベースのテーブルまたはビューの名前です。 |はい |

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ
アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、「[パイプラインの作成](data-factory-create-pipelines.md)」という記事を参照してください。 名前、説明、入力テーブル、出力テーブル、ポリシーなどのプロパティは、あらゆる種類のアクティビティで使用できます。

> [!NOTE]
> コピー アクティビティは入力を 1 つだけ受け取り、出力を 1 つだけ生成します。

一方、アクティビティの typeProperties セクションで使用できるプロパティは、各アクティビティの種類によって異なります。 コピー アクティビティの場合、ソースとシンクの種類によって異なります。

### <a name="sqldwsource"></a>SqlDWSource
コピー アクティビティで、source の種類が **SqlDWSource** である場合は、**typeProperties** セクションで次のプロパティを使用できます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| sqlReaderQuery |カスタム クエリを使用してデータを読み取ります。 |SQL クエリ文字列。 例: Select * from MyTable。 |いいえ |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 |ストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |いいえ |
| storedProcedureParameters |ストアド プロシージャのパラメーター。 |名前と値のペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |

SqlDWSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティでは、データを取得するために SQL Data Warehouse ソースに対してこのクエリを実行します。

または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。

sqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Data Warehouse に対して実行するクエリを作成するために、データセット JSON の構造セクションで定義された列が使用されます。 例: `select column1, column2 from mytable`. データセット定義に構造がない場合は、すべての列がテーブルから選択されます。

#### <a name="sqldwsource-example"></a>SqlDWSource の例

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**ストアド プロシージャの定義:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>パイプライン
**SqlDWSink** では次のプロパティがサポートされます。

| プロパティ | 説明 | 使用できる値 | 必須 |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |特定のスライスのデータを消去するコピー アクティビティのクエリを指定します。 詳細については、 [再現性に関するセクション](#repeatability-during-copy)をご覧ください。 |クエリ ステートメント。 |いいえ |
| allowPolyBase |BULKINSERT メカニズムではなく PolyBase (該当する場合) を使用するかどうかを示します。 <br/><br/> **SQL Data Warehouse へのデータ読み込みには、PolyBase の使用をお勧めします。** 制約と詳細については、「 [PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 」をご覧ください。 |True <br/>False (既定値) |いいえ |
| polyBaseSettings |**allowPolybase** プロパティが **true** に設定されているときに指定できるプロパティのグループ。 |&nbsp; |いいえ |
| rejectValue |クエリが失敗するまでに拒否できる行の数または割合を指定します。 <br/><br/>PolyBase の拒否オプションの詳細については、「 **CREATE EXTERNAL TABLE (Transact-SQL)** 」の「 [Arguments (引数)](https://msdn.microsoft.com/library/dn935021.aspx) 」をご覧ください。 |0 (既定値)、1、2、… |いいえ |
| rejectType |rejectValue オプションをリテラル値と割合のどちらで指定するかを指定します。 |Value (既定値)、Percentage |いいえ |
| rejectSampleValue |拒否された行の割合が PolyBase で再計算されるまでに取得する行数を決定します。 |1、2、… |はい (**rejectType** が **percentage** の場合) |
| useTypeDefault |PolyBase がテキスト ファイルからデータを取得する場合にどのように区切りテキスト ファイル内の不足値を処理するかを、指定します。<br/><br/>このプロパティの詳細については、[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) Arguments セクションをご覧ください。 |True、False (既定値) |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に到達したときに SQL テーブルにデータを挿入します。 |整数 (行数) |いいえ (既定値:10000) |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。 |TimeSpan<br/><br/> 例:"00:30:00" (30 分)。 |いいえ |

#### <a name="sqldwsink-example"></a>SqlDWSink の例

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む
**[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** を使用すると、高いスループットで Azure SQL Data Warehouse に大量のデータを効率的に読み込むことができます。 既定の BULKINSERT メカニズムではなく PolyBase を使用することで、スループットが大幅に向上することがわかります。 詳細な比較については、[コピー パフォーマンスの参考数値](data-factory-copy-activity-performance.md#performance-reference)に関するページを参照してください。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](data-factory-load-sql-data-warehouse.md)に関するページを参照してください。

* ソース データが **Azure Blob または Azure Data Lake Store** 内にあり、PolyBase と互換性のある形式の場合は、PolyBase を使用して Azure SQL Data Warehouse に直接コピーできます。 詳細については、「 **[PolyBase を使用して直接コピーする](#direct-copy-using-polybase)** 」を参照してください。
* ソース データのストアと形式が、本来は PolyBase でサポートされていない形式の場合は、代わりに **[PolyBase を使用したステージング コピー](#staged-copy-using-polybase)** を使用できます。 これによりデータが自動的に PolyBase に対応する形式に変換されたうえで、Azure Blob Storage に格納されるため、スループットも向上します。 その後、データは SQL Data Warehouse に読み込まれます。

次の例に示すように、Azure Data Factory で PolyBase を使用して Azure SQL Data Warehouse にデータをコピーするために、`allowPolyBase` プロパティを **true** に設定します。 allowPolyBase を true に設定すると、`polyBaseSettings` プロパティ グループを使用して PolyBase 固有のプロパティを指定できます。 polyBaseSettings で使用できるプロパティの詳細については、上記の「 [SqlDWSink](#sqldwsink) 」をご覧ください。

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>PolyBase を使用して直接コピーする
SQL Data Warehouse の PolyBase は (サービス プリンシパルを使用して)、Azure Blob と Azure Data Lake Store をソースとして、特定のファイル形式の要件付きで直接サポートしています。 ソース データがこのセクションで説明する条件を満たす場合は、PolyBase を使用してソース データ ストアから Azure SQL Data Warehouse に直接コピーできます。 それ以外の場合は、 [PolyBase を使用したステージング コピー](#staged-copy-using-polybase)を利用できます。

> [!TIP]
> データを効率的に Data Lake Store から SQL Data Warehouse にコピーするには、「[Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)」(Azure Data Factory を利用すれば、SQL Data Warehouse と共に Data Lake Store を使用する場合にデータからさらに容易かつ便利に情報を引き出せるようになる) を参考にしてください。

要件が満たされない場合は、Azure Data Factory が設定を確認し、データ移動には自動的に BULKINSERT メカニズムが使用されるように戻ります。

1. **ソースのリンクされたサービス**の種類が **AzureStorage** または**サービス プリンシパルの認証を使用する AzureDataLakeStore** である。
2. **入力データセット**の種類が **AzureBlob** または **AzureDataLakeStore** で、`type` プロパティの形式の種類が次の構成の **OrcFormat**、**ParquetFormat** または **TextFormat** である。

   1. `rowDelimiter` が **\n** である。
   2. `nullValue` が **空の文字列** ("") に設定されている。または、`treatEmptyAsNull` が **true** に設定されている。
   3. `encodingName` が **utf-8** に設定されている。これは**既定値**です。
   4. `escapeChar`、`quoteChar`、`firstRowAsHeader`、および `skipLineCount` が指定されていない。
   5. `compression` が **圧縮なし**、**GZip**、または **Deflate**である。

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. パイプラインのコピー アクティビティでは、**BlobSource** または **AzureDataLakeStore**の下に `skipHeaderLineCount` 設定がないこと。
4. パイプラインのコピー アクティビティでは、**SqlDWSink** の下に `sliceIdentifierColumnName` 設定がないこと (PolyBase で保証されるのは、1 回の実行ですべてのデータが更新されるか、何も更新されないかのいずれかです。 **再現性**を実現するには、`sqlWriterCleanupScript` を使用します)。
5. 関連付けられたコピー アクティビティで `columnMapping` が使用されていないこと。

### <a name="staged-copy-using-polybase"></a>PolyBase を使用したステージング コピー
ソース データが前のセクションで紹介した条件を満たしていない場合は、中間ステージング Azure Blob Storage 経由でのデータのコピーを有効にすることができます (Premium Storage にはできません)。 その場合、Azure Data Factory は PolyBase のデータ形式の要件を満たすためにデータの変換を自動的に実行し、PolyBase を使用して SQL Data Warehouse にデータを読み込み、最後に Blob ストレージから一時データをクリーンアップします。 ステージング Azure BLOB 経由でデータをコピーする通常の操作方法の詳細については、「 [ステージング コピー](data-factory-copy-activity-performance.md#staged-copy) 」をご覧ください。

> [!NOTE]
> オンプレミスのデータ ストアから PolyBase を使用して Azure SQL Data Warehouse にデータをコピーし、ステージングする場合、Data Management Gateway のバージョンが 2.4 未満であれば、ソース データを適切な形式に変換するために使用するゲートウェイ マシンに JRE (Java ランタイム環境) をインストールする必要があります。 このような依存関係を回避するため、ゲートウェイは最新バージョンにアップグレードすることをお勧めします。
>

この機能を使用するには、中間 Blob Storage がある Azure ストレージ アカウントを参照する [Azure Storage のリンクされたサービス](data-factory-azure-blob-connector.md#azure-storage-linked-service)を作成し、次のコードに示すように、コピー アクティビティの `enableStaging` および `stagingSettings` プロパティを指定します。

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>PolyBase を使用する際のベスト プラクティス
次のセクションでは、「[Azure SQL Data Warehouse のベスト プラクティス](../../sql-data-warehouse/sql-data-warehouse-best-practices.md)」に記載されている内容に追加するベスト プラクティスを説明します。

### <a name="required-database-permission"></a>必要なデータベース アクセス許可
PolyBase を使用するには、データを SQL Data Warehouse に読み込むために使用されるユーザーが、ターゲット データベースでの ["CONTROL" アクセス許可](https://msdn.microsoft.com/library/ms191291.aspx)を持っている必要があります。 これを実現する方法の 1 つに、ユーザーを "db_owner" ロールのメンバーとして追加するという方法があります。 具体的な手順については、[こちらのセクション](../../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)に従ってください。

### <a name="row-size-and-data-type-limitation"></a>行のサイズとデータ型の制限
Polybase 読み込みは両方が **1 MB** 未満の行の読み込みに制限され、VARCHR(MAX)、NVARCHAR(MAX)、VARBINARY(MAX) に読み込むことはできません。 [こちら](../../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)を参照してください。

ソース データの行のサイズが 1 MB を超える場合は、ソース テーブルを垂直方向に複数の小さいテーブルに分割し、各テーブルの行の最大サイズが制限を超えないようにすることができます。 その後、この分割した小さいテーブルは、PolyBase を使用して Azure SQL Data Warehouse に読み込み、マージすることができます。

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse リソース クラス
可能な限りスループットを最大化するには、PolyBase を通じて SQL Data Warehouse にデータを読み込むために使用されるユーザーに、より大きなリソース クラスを割り当てることを検討してください。 「[ユーザー リソース クラスの変更例](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)」で、実行方法を確認してください。

### <a name="tablename-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の tableName
次の表では、スキーマとテーブル名のさまざまな組み合わせについて、データセットの JSON で **tableName** プロパティを指定する方法の例を示します。

| DB スキーマ | テーブル名 | tableName JSON プロパティ |
| --- | --- | --- |
| dbo |MyTable |MyTable、dbo.MyTable、または [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable または [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] または [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1].[My.Table] |

次のエラーが表示される場合は、tableName プロパティに指定した値に問題がある可能性があります。 tableName JSON プロパティの値を指定する正しい方法については、上の表を参照してください。

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>既定値を持つ列
現在、Data Factory の PolyBase 機能では、ターゲット テーブルと同じ数の列のみを使用できます。 たとえば、4 つの列を含むテーブルがあり、その列の 1 つには既定値が定義されているとします。 そのような場合にも、入力データには 4 つの列を含める必要があります。 3 列の入力データセットを指定すると、次のメッセージのようなエラーが発生します。

```
All columns of the table must be specified in the INSERT BULK statement.
```
null 値は、特殊な形式の既定値です。 列が null 値を許容している場合は、その列の (BLOB 内の) 入力データが空である可能性があります (入力データセットからなくすことはできません)。 PolyBase は、その列の null 値を Azure SQL Data Warehouse に挿入します。

## <a name="auto-table-creation"></a>テーブルの自動作成
コピー ウィザードを使用して SQL Server または Azure SQL Database から Azure SQL Data Warehouse にデータをコピーするとき、ソース テーブルに対応するテーブルがコピー先ストアにが存在しない場合、Data Factory では、ソース テーブル スキーマを使用して、そのテーブルをデータ ウェアハウスに自動的に作成できます。

Data Factory は、コピー元データ ストアのテーブルと同じ名前を使って、コピー先にテーブルを作成します。 列のデータ型は、次の型マッピングに基づいて選択されます。 必要に応じて型変換が実行され、コピー元とコピー先のストアの非互換性が修正されます。 また、ラウンド ロビン テーブルのディストリビューションも使用されます。

| コピー元 SQL Database の列の型 | コピー先 SQL DW の列の型 (サイズ制限) |
| --- | --- |
| int | int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bit | Bit |
| Decimal | Decimal |
| Numeric | Decimal |
| Float | Float |
| Money | Money |
| Real | Real |
| SmallMoney | SmallMoney |
| Binary | Binary |
| Varbinary | Varbinary (最大 8000) |
| Date | Date |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Time | Time |
| DateTimeOffset | DateTimeOffset |
| SmallDateTime | SmallDateTime |
| Text | Varchar (最大 8000) |
| NText | NVarChar (最大 4000) |
| Image | VarBinary (最大 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (最大 8000) |
| NVarChar | NVarChar (最大 4000) |
| xml | Varchar (最大 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse の型のマッピング
[データ移動アクティビティ](data-factory-data-movement-activities.md) に関する記事のとおり、コピー アクティビティは次の 2 段階のアプローチで型を source から sink に自動的に変換します。

1. ネイティブの source 型から .NET 型に変換する
2. .NET 型からネイティブの sink 型に変換する

Azure SQL Data Warehouse との間でデータを移動するとき、SQL 型から .NET 型へのマッピング (およびその逆) に次のマッピングが使用されます。

マッピングは [ADO.NET の SQL Server データ型マッピング](https://msdn.microsoft.com/library/cc716729.aspx)と同じです。

| SQL Server Databases エンジンの型 | .NET Framework 型 |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

コピー アクティビティ定義で、ソース データセットの列をシンク データセットの列にマップすることもできます。 詳細については、[Azure Data Factory のデータセット列のマッピング](data-factory-map-columns.md)に関するページを参照してください。

## <a name="json-examples-for-copying-data-to-and-from-sql-data-warehouse"></a>SQL Data Warehouse との間でのデータのコピーに関する JSON の例
次の例は、[Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) または [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md) を使用してパイプラインを作成する際に使用できるサンプルの JSON 定義です。 これらの例は、SQL Data Warehouse と Azure BLOB ストレージの間でデータをコピーする方法を示しています。 ただし、Azure Data Factory のコピー アクティビティを使用して、 **こちら** に記載されているいずれかのシンクに、任意のソースからデータを [直接](data-factory-data-movement-activities.md#supported-data-stores-and-formats) コピーすることができます。

### <a name="example-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>例:Azure SQL Data Warehouse から Azure BLOB にデータをコピーする
このサンプルでは、次の Data Factory のエンティティを定義します。

1. [AzureSqlDW](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [AzureSqlDWTable](#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [SqlDWSource](#copy-activity-properties) と [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) を使用するコピー アクティビティを含む[パイプライン](data-factory-create-pipelines.md)。

このサンプルは時系列 (時間単位、日単位など) のデータを 1 時間おきに Azure SQL Data Warehouse データベースのテーブルから BLOB にコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL Data Warehouse のリンクされたサービス:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure BLOB ストレージのリンクされたサービス:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure SQL Data Warehouse の入力データセット:**

このサンプルでは、Azure SQL Data Warehouse で「MyTable」という名前のテーブルを作成し、時系列データ用に「timestampcolumn」という名前の列が含まれているものと想定しています。

”external” を ”true” に設定すると、データセットが Data Factory の外部にあり、Data Factory のアクティビティによって生成されたものではないことが Data Factory サービスに通知されます。

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure BLOB の出力データセット:**

データは新しい BLOB に 1 時間おきに書き込まれます (frequency: hour、interval: 1)。 BLOB のフォルダー パスは、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日、時刻の部分を使用します。

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**SqlDWSource と BlobSink を使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプライン JSON 定義で、**source** 型が **SqlDWSource** に設定され、**sink** 型が **BlobSink** に設定されています。 **SqlReaderQuery** プロパティに指定されている SQL クエリは過去のデータを選択してコピーします。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
> [!NOTE]
> この例では、SqlDWSource に **sqlReaderQuery** が指定されています。 コピー アクティビティでは、データを取得するために SQL Data Warehouse ソースに対してこのクエリを実行します。
>
> または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。
>
> SqlReaderQuery や sqlReaderStoredProcedureName を指定しない場合は、Azure SQL Database に対して実行するクエリを作成するために、データセット JSON の構造セクションで定義された列が使用されます (mytable から column1 と column2 を選択)。 データセット定義に構造がない場合は、すべての列がテーブルから選択されます。
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>例:Azure BLOB から Azure SQL Data Warehouse にデータをコピーする
このサンプルでは、次の Data Factory のエンティティを定義します。

1. [AzureSqlDW](#linked-service-properties)型のリンクされたサービス。
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)型のリンクされたサービス。
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 型の入力[データセット](data-factory-create-datasets.md)。
4. [AzureSqlDWTable](#dataset-properties) 型の出力[データセット](data-factory-create-datasets.md)。
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) と [SqlDWSink](#copy-activity-properties) を使用するコピー アクティビティの[パイプライン](data-factory-create-pipelines.md)。

このサンプルは、Azure SQL Data Warehouse データベースのテーブルから Azure BLOB に時系列データ (時間単位、日単位など) を 1 時間おきにコピーします。 これらのサンプルで使用される JSON プロパティの説明はサンプルに続くセクションにあります。

**Azure SQL Data Warehouse のリンクされたサービス:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure BLOB ストレージのリンクされたサービス:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Azure BLOB の入力データセット:**

データは新しい BLOB から 1 時間おきに取得されます (頻度: 時間、間隔:1)。 BLOB のフォルダー パスとファイル名は、処理中のスライスの開始時間に基づき、動的に評価されます。 フォルダー パスは開始時間の年、月、日の部分を利用し、ファイル名は開始時間の時刻部分を使用します。 "external": "true" 設定は Data Factory サービスにこのテーブルが Data Factory の外部にあり、Data Factory のアクティビティでは生成されないことを通知します。

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL Data Warehouse の出力データセット:**

このサンプルは Azure SQL Data Warehouse の「MyTable」というテーブルにデータをコピーします。 BLOB CSV ファイルに含めることが予想される列の数と同じ列数で Azure SQL Data Warehouse にテーブルを作成する必要があります。 新しい行は 1 時間ごとにテーブルに追加されます。

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**BlobSource と SqlDWSink を使用するパイプラインでのコピー アクティビティ:**

パイプラインには、入力データセットと出力データセットを使用するように構成され、1 時間おきに実行するようにスケジュールされているコピー アクティビティが含まれています。 パイプラインの JSON 定義で、**source** 型が **BlobSource** に設定され、**sink** 型が **SqlDWSink** に設定されています。

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
チュートリアルについては、Azure SQL Data Warehouse ドキュメントの「[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む](data-factory-load-sql-data-warehouse.md)」および [Azure Data Factory でのデータの読み込み](../../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md)に関する記事をご覧ください。

## <a name="performance-and-tuning"></a>パフォーマンスとチューニング
Azure Data Factory でのデータ移動 (コピー アクティビティ) のパフォーマンスに影響する主な要因と、パフォーマンスを最適化するための各種方法については、「[コピー アクティビティのパフォーマンスとチューニングに関するガイド](data-factory-copy-activity-performance.md)」を参照してください。
