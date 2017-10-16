---
title: "Data Factory を使用して Azure SQL Data Warehouse をコピー先またはコピー元としてデータをコピーする | Microsoft Docs"
description: "Data Factory を使用して、サポートされるソース ストアのデータを Azure SQL Data Warehouse にコピーしたり、Azure SQL Data Warehouse のデータをサポートされるシンク ストアにコピーしたりできます。"
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
ms.date: 09/18/2017
ms.author: jingwang
ms.openlocfilehash: fb67c70d1e85307c38a185e2b47729880880d55b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Azure Data Factory を使用して Azure SQL Data Warehouse をコピー先またはコピー元としてデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - GA](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [バージョン 2 - プレビュー](connector-azure-sql-data-warehouse.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure SQL Data Warehouse をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Azure SQL Data Warehouse コネクタ](v1/data-factory-azure-sql-data-warehouse-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

Azure SQL Data Warehouse のデータを、サポートされる任意のシンク データ ストアにコピーしたり、サポートされる任意のソース データ ストアのデータを Azure SQL Data Warehouse にコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Azure SQL Data Warehouse コネクタは以下をサポートします。

- SQL 認証を使用したデータのコピー。
- ソースとしての SQL クエリまたはストアド プロシージャを使用したデータの取得。
- シンクとしての **PolyBase** または一括挿入を使用したデータの読み込み。 コピーのパフォーマンスを向上させるために前者を**推奨します**。

## <a name="getting-started"></a>使用の開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

以下のセクションでは、Azure SQL Data Warehouse コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure SQL Data Warehouse のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureSqlDW** | はい |
| connectionString |connectionString プロパティの Azure SQL Data Warehouse インスタンスに接続するために必要な情報を指定します。 基本認証だけがサポートされています。 このフィールドを SecureString とマークします。 |あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |


> [!IMPORTANT]
> [サーバーへのアクセスを Azure サービスに許可する](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)ように [Azure SQL Data Warehouse ファイアウォール](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)とデータベース サーバーを構成します。 さらに、Azure の外部から (たとえばセルフホステッド統合ランタイムを使用するオンプレミスのデータ ソースから) Azure SQL Data Warehouse にデータをコピーする場合は、Azure SQL Data Warehouse にデータを送信するコンピューターの適切な IP アドレス範囲を構成します。

**例:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure SQL Data Warehouse データセットでサポートされるプロパティの一覧を示します。

Azure SQL Data Warehouse をコピー元またはコピー先としてデータをコピーするには、データセットの type プロパティを **AzureSqlDWTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureSqlDWTable** を設定する必要があります。 | あり |
| tableName |リンクされたサービスが参照する Azure SQL Data Warehouse インスタンスのテーブルまたはビューの名前。 | あり |

**例:**

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure SQL Data Warehouse のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-sql-data-warehouse-as-source"></a>ソースとしての Azure SQL Data Warehouse

Azure SQL Data Warehouse からデータをコピーする場合は、コピー アクティビティのソースの種類を **SqlDWSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **SqlDWSource** を設定する必要があります。 | あり |
| SqlReaderQuery |カスタム SQL クエリを使用してデータを読み取ります。 例: `select * from MyTable`. |なし |
| sqlReaderStoredProcedureName |ソース テーブルからデータを読み取るストアド プロシージャの名前。 最後の SQL ステートメントはストアド プロシージャの SELECT ステートメントにする必要があります。 |なし |
| storedProcedureParameters |ストアド プロシージャのパラメーター。<br/>使用可能な値: 名前/値ペア。 パラメーターの名前とその大文字と小文字は、ストアド プロシージャのパラメーターの名前とその大文字小文字と一致する必要があります。 |いいえ |

**注意する点:**

- SqlSource に **sqlReaderQuery** が指定されている場合、コピー アクティビティは、Azure SQL Data Warehouse ソースに対してこのクエリを実行してデータを取得します。 または、**sqlReaderStoredProcedureName** と **storedProcedureParameters** を指定して、ストアド プロシージャを指定することができます (ストアド プロシージャでパラメーターを使用する場合)。
- "sqlReaderQuery" または "sqlReaderStoredProcedureName" を指定しない場合は、データセット JSON の "structure" セクションに定義された列を使用して、Azure SQL Data Warehouse に対して実行するクエリ (`select column1, column2 from mytable`) が作成されます。 データセット定義に "structure" がない場合は、テーブルのすべての列が選択されます。
- **sqlReaderStoredProcedureName** を使用する場合でも、ダミーの **tableName** プロパティをデータセット JSON に指定する必要があります。

**例: SQL クエリの使用**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**例: ストアド プロシージャの使用**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlDWSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**ストアド プロシージャの定義:**

```sql
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

### <a name="azure-sql-data-warehouse-as-sink"></a>シンクとしての Azure SQL Data Warehouse

Azure SQL Data Warehouse にデータをコピーする場合は、コピー アクティビティのシンクの種類を **SqlDWSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **SqlDWSink** を設定する必要があります。 | あり |
| allowPolyBase |BULKINSERT メカニズムではなく PolyBase (該当する場合) を使用するかどうかを示します。 <br/><br/> **SQL Data Warehouse へのデータ読み込みには、PolyBase の使用をお勧めします。** 制約と詳細については、「 [PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 」をご覧ください。<br/><br/>使用可能な値: **True** (既定値) および **False**。  |いいえ |
| polyBaseSettings |**allowPolybase** プロパティが **true** に設定されているときに指定できるプロパティのグループ。 |いいえ |
| rejectValue |クエリが失敗するまでに拒否できる行の数または割合を指定します。<br/><br/>PolyBase の拒否オプションの詳細については、「 **CREATE EXTERNAL TABLE (Transact-SQL)** 」の「 [Arguments (引数)](https://msdn.microsoft.com/library/dn935021.aspx) 」をご覧ください。 <br/><br/>使用可能な値: 0 (既定値)、1、2 … |いいえ |
| rejectType |rejectValue オプションをリテラル値と割合のどちらで指定するかを指定します。<br/><br/>使用可能な値:**値**(既定値) および**割合**。 |いいえ |
| rejectSampleValue |拒否された行の割合が PolyBase で再計算されるまでに取得する行数を決定します。<br/><br/>使用可能な値: 1、2 … |はい (**rejectType** が **percentage** の場合) |
| useTypeDefault |PolyBase がテキスト ファイルからデータを取得する場合にどのように区切りテキスト ファイル内の不足値を処理するかを、指定します。<br/><br/>このプロパティの詳細については、[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx) Arguments セクションをご覧ください。<br/><br/>使用可能な値: **True****False** (既定値)。 |いいえ |
| writeBatchSize |バッファー サイズが writeBatchSize に達したときに SQL テーブルにデータを挿入します。 PolyBase が使用されない場合にのみ適用されます。<br/><br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 10000) |
| writeBatchTimeout |タイムアウトする前に一括挿入操作の完了を待つ時間です。PolyBase が使用されない場合にのみ適用されます。<br/><br/>使用可能な値: 期間。 例: "00:30:00" (30 分)。 |いいえ |
| preCopyScript |コピー アクティビティの毎回の実行で、データを Azure SQL Data Warehouse に書き込む前に実行する SQL クエリを指定します。 このプロパティを使用して、事前に読み込まれたデータをクリーンアップできます。 |いいえ |(#repeatability-during-copy)。 |クエリ ステートメント。 |いいえ |

**例:**

```json
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

次のセクションで、PolyBase を使用して SQL Data Warehouse への読み込みを効率的に実行する方法について詳しく説明します。

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>PolyBase を使用して Azure SQL Data Warehouse にデータを読み込む

**[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide)** を使用すると、高いスループットで Azure SQL Data Warehouse に大量のデータを効率的に読み込むことができます。 既定の BULKINSERT メカニズムではなく PolyBase を使用することで、スループットが大幅に向上することがわかります。 詳細な比較については、[コピー パフォーマンスの参考数値](copy-activity-performance.md#performance-reference)に関するページを参照してください。 ユース ケースを使用したチュートリアルについては、[1 TB のデータを Azure Data Factory を使用して 15 分以内に Azure SQL Data Warehouse に読み込む方法](connector-azure-sql-data-warehouse.md)に関するページを参照してください。

* ソース データが **Azure Blob または Azure Data Lake Store** 内にあり、PolyBase と互換性のある形式の場合は、PolyBase を使用して Azure SQL Data Warehouse に直接コピーできます。 詳細については、「**[PolyBase を使用して直接コピーする](#direct-copy-using-polybase)**」を参照してください。
* ソース データのストアと形式が、本来は PolyBase でサポートされていない形式の場合は、代わりに **[PolyBase を使用したステージング コピー](#staged-copy-using-polybase)**を使用できます。 これによりデータが自動的に PolyBase に対応する形式に変換されたうえで、Azure Blob Storage に格納されるため、スループットも向上します。 その後、データは SQL Data Warehouse に読み込まれます。

### <a name="direct-copy-using-polybase"></a>PolyBase を使用して直接コピーする

SQL Data Warehouse の PolyBase は (サービス プリンシパルを使用して)、Azure Blob と Azure Data Lake Store をソースとして、特定のファイル形式の要件付きで直接サポートしています。 ソース データがこのセクションで説明する条件を満たす場合は、PolyBase を使用してソース データ ストアから Azure SQL Data Warehouse に直接コピーできます。 それ以外の場合は、 [PolyBase を使用したステージング コピー](#staged-copy-using-polybase)を利用できます。

> [!TIP]
> データを効率的に Data Lake Store から SQL Data Warehouse にコピーするには、「[Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)」(Azure Data Factory を利用すれば、SQL Data Warehouse と共に Data Lake Store を使用する場合にデータからさらに容易かつ便利に情報を引き出せるようになる) を参考にしてください。

要件が満たされない場合は、Azure Data Factory が設定を確認し、データ移動には自動的に BULKINSERT メカニズムが使用されるように戻ります。

1. **ソースのリンクされたサービス**の型が **AzureStorage** または **AzureDataLakeStore** であること。
2. **入力データセット**の種類が **AzureBlob** または **AzureDataLakeStoreFile** で、`type` プロパティの形式が **OrcFormat** または次の構成の **TextFormat** であること。

   1. `rowDelimiter` は **\n** である必要があります。
   2. `nullValue` が **空の文字列** ("") に設定されている。または、`treatEmptyAsNull` が **true** に設定されている。
   3. `encodingName` が **utf-8** に設定されている。これは**既定値**です。
   4. `escapeChar`、`quoteChar`、`firstRowAsHeader`、および `skipLineCount` が指定されていない。
   5. `compression` が **圧縮なし**、**GZip**、または **Deflate**である。

    ```json
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
4. パイプラインのコピー アクティビティでは、**SqlDWSink** の下に `sliceIdentifierColumnName` 設定がないこと  (PolyBase で保証されるのは、1 回の実行ですべてのデータが更新されるか、何も更新されないかのいずれかです。 **再現性**を実現するには、`sqlWriterCleanupScript` を使用します)。
5. 関連付けられたコピー アクティビティで `columnMapping` が使用されていないこと。

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-using-polybase"></a>PolyBase を使用したステージング コピー

ソース データが前のセクションで紹介した条件を満たしていない場合は、中間ステージング Azure Blob Storage 経由でのデータのコピーを有効にすることができます (Premium Storage にはできません)。 その場合、Azure Data Factory は PolyBase のデータ形式の要件を満たすためにデータの変換を自動的に実行し、PolyBase を使用して SQL Data Warehouse にデータを読み込んだ後、BLOB ストレージから一時データをクリーンアップします。 ステージング Azure BLOB 経由でデータをコピーする通常の操作方法の詳細については、「 [ステージング コピー](copy-activity-performance.md#staged-copy) 」をご覧ください。

この機能を使用するには、中間 Blob Storage がある Azure ストレージ アカウントを参照する [Azure Storage のリンクされたサービス](connector-azure-blob-storage.md#linked-service-properties)を作成し、次のコードに示すように、コピー アクティビティの `enableStaging` および `stagingSettings` プロパティを指定します。

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
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

次のセクションでは、「[Azure SQL Data Warehouse のベスト プラクティス](../sql-data-warehouse/sql-data-warehouse-best-practices.md)」に記載されている内容に追加するベスト プラクティスを説明します。

### <a name="required-database-permission"></a>必要なデータベース アクセス許可

PolyBase を使用するには、データを SQL Data Warehouse に読み込むために使用されるユーザーが、ターゲット データベースでの ["CONTROL" アクセス許可](https://msdn.microsoft.com/library/ms191291.aspx)を持っている必要があります。 これを実現する方法の 1 つに、ユーザーを "db_owner" ロールのメンバーとして追加するという方法があります。 具体的な手順については、[こちらのセクション](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)に従ってください。

### <a name="row-size-and-data-type-limitation"></a>行のサイズとデータ型の制限

Polybase 読み込みは両方が **1 MB** 未満の行の読み込みに制限され、VARCHR(MAX)、NVARCHAR(MAX)、VARBINARY(MAX) に読み込むことはできません。 [こちら](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)を参照してください。

ソース データの行のサイズが 1 MB を超える場合は、ソース テーブルを垂直方向に複数の小さいテーブルに分割し、各テーブルの行の最大サイズが制限を超えないようにすることができます。 その後、この分割した小さいテーブルは、PolyBase を使用して Azure SQL Data Warehouse に読み込み、マージすることができます。

### <a name="sql-data-warehouse-resource-class"></a>SQL Data Warehouse リソース クラス

可能な限りスループットを最大化するには、PolyBase を通じて SQL Data Warehouse にデータを読み込むために使用されるユーザーに、より大きなリソース クラスを割り当てることを検討してください。 「[ユーザー リソース クラスの変更例](../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example)」で、実行方法を確認してください。

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

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse のデータ型のマッピング

Azure SQL Data Warehouse をコピー元またはコピー先としてデータをコピーするとき、次の Azure SQL Data Warehouse のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

| Azure SQL Data Warehouse のデータ型 | Data Factory の中間データ型 |
|:--- |:--- |
| bigint |Int64 |
| バイナリ |Byte[] |
| ビット |Boolean |
| char |String、Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| FILESTREAM 属性 (varbinary(max)) |Byte[] |
| Float |Double |
| イメージ |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String、Char[] |
| ntext |String、Char[] |
| numeric |Decimal |
| nvarchar |String、Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| テキスト |String、Char[] |
| time |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String、Char[] |
| xml |xml |

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。