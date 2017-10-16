---
title: "Data Factory を使用して Azure Table Storage をコピー先またはコピー元としてデータをコピーする | Microsoft Docs"
description: "Data Factory を使用して、サポートされるソース ストアから Azure Table Storage にデータをコピーしたり、Table Storage からサポートされるシンク ストアにコピーしたりする方法を説明します。"
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
ms.openlocfilehash: 2fa03b82750585454430da0c29392db57b20d3c9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="copy-data-to-or-from-azure-table-using-azure-data-factory"></a>Azure Data Factory を使用した Azure Table との間でのデータのコピー
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - 一般公開](v1/data-factory-azure-table-connector.md)
> * [バージョン 2 - プレビュー](connector-azure-table-storage.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Table をコピー先またはコピー元としてデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Azure Table Storage コネクタ](v1/data-factory-azure-table-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

サポートされる任意のソース データ ストアのデータを Azure Table にコピーしたり、Azure Table のデータをサポートされる任意のシンク データ ストアにコピーしたりできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Azure Table コネクタは、**アカウント キー**と**サービス SAS** (Shared Access Signature) 認証の両方を使用して、データのコピーをサポートします。

## <a name="get-started"></a>作業開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

以下のセクションでは、Azure Table Storage に固有の Data Factory エンティティの定義に使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

### <a name="using-account-key"></a>アカウント キーの使用

Azure Storage へのグローバル アクセスが可能なデータ ファクトリを提供するアカウント キーを使用して、Azure Storage のリンクされたサービスを作成できます。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureStorage** |はい |
| connectionString | connectionString プロパティのために Azure Storage に接続するために必要な情報を指定します。 このフィールドを SecureString とマークします。 |あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-service-sas-authentication"></a>サービス SAS 認証の使用

記憶域内のすべてまたは固有のリソースへの制限付きまたは期限付きアクセスが可能なデータ ファクトリを提供する Shared Access Signature (SAS) を使用して、Azure Storage のリンクされたサービスを作成することもできます。

Shared Access Signature (SAS) を使用すると、ストレージ アカウント内のリソースへの委任アクセスが可能になります。 これにより、ストレージ アカウントのオブジェクトへの制限付きアクセス許可を、期間とアクセス許可セットを指定してクライアントに付与できます。また、アカウント アクセス キーを共有する必要はありません。 SAS とは、ストレージ リソースへの認証アクセスに必要なすべての情報をクエリ パラメーター内に含む URI です。 クライアントは、SAS 内で適切なコンストラクターまたはメソッドに渡すだけで、SAS でストレージ リソースにアクセスできます。 SAS の詳細については、[Shared Access Signature の SAS モデルの概要](../storage/common/storage-dotnet-shared-access-signature-part-1.md)に関する記事を参照してください。

> [!IMPORTANT]
> Azure Data Factory は、**サービス SAS** のみをサポートします。アカウント SAS はサポートしません。 この 2 種類の SAS と作成方法の詳細については、「[Shared Access Signature の種類](../storage/common/storage-dotnet-shared-access-signature-part-1.md#types-of-shared-access-signatures)」を参照してください。 Azure Portal またはストレージ エクスプローラーから生成できる SAS URL はアカウント SAS であり、サポートされません。
>

サービス SAS 認証の使用には、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureStorage** |あり |
| sasUri | BLOB、コンテナー、テーブルなどの Azure Storage リソースへの Shared Access Signature URI を指定します。 このフィールドを SecureString とマークします。 |あり |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 Azure 統合ランタイムまたは自己ホスト型統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

**例:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "sasUri": {
                "type": "SecureString",
                "value": "<SAS URI of the Azure Storage resource>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**SAS URI**を作成する際は、次の点を考慮してください。

- リンクされたサービス (読み取り、書き込み、読み取り/書き込み) がデータ ファクトリ内でどのように使用されているかに応じて、オブジェクトに対する適切な読み取り/書き込み**アクセス許可**を設定します。
- **有効期限**を適切に設定します。 Azure Storage オブジェクトへのアクセスがパイプラインのアクティブな期間内に期限切れにならないことを確認します。
- URI は、必要に応じて、適切なテーブル レベルで作成する必要があります。

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure Table データセットでサポートされるプロパティの一覧を示します。

Azure Table をコピー先またはコピー元としてデータをコピーするには、データセットの type プロパティを **AzureTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureTable** に設定する必要があります。 |あり |
| tableName |リンクされたサービスが参照する Azure テーブル データベース インスタンスのテーブルの名前です。 |あり |

**例:**

```json
{
    "name": "AzureTableDataset",
    "properties":
    {
        "type": "AzureTable",
        "linkedServiceName": {
            "referenceName": "<Azure Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory によるスキーマ

Azure Table などのスキーマのないデータ ストアの場合、Data Factory サービスは次のいずれかの方法でスキーマを推論します。

1. データセット定義で **structure** プロパティを使用してデータの構造を指定した場合、Data Factory サービスはスキーマとしてこの構造を優先します。 この場合、行に列の値が含まれていないと、null 値が指定されます。
2. データセット定義で **structure** プロパティを使用してデータの構造を指定しなかった場合、Data Factory はデータの最初の行を使用してスキーマを推論します。 この場合、最初の行に完全なスキーマが含まれていないと、コピー操作の結果で一部の列が欠落します。

したがって、スキーマのないデータ ソースでは、 **structure** プロパティを使用してデータの構造を指定するのがベスト プラクティスです。

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Table のソースとシンクでサポートされるプロパティの一覧を示します。

### <a name="azure-table-as-source"></a>ソースとして Azure Table

Azure Table からデータをコピーする場合は、コピー アクティビティのソースの種類を **AzureTableSource** を設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **AzureTableSource** に設定する必要があります。 |あり |
| AzureTableSourceQuery |カスタム AzureTable 　クエリを使用してデータを読み取ります。 次のセクションの例を参照してください。 |いいえ |
| azureTableSourceIgnoreTableNotFound |テーブルが存在しないという例外を受け入れるかどうかを示します。<br/>使用可能な値: **True**、および **False** (既定値)。 |いいえ |

### <a name="azuretablesourcequery-examples"></a>azureTableSourceQuery の例

Azure テーブルの列が文字列型の場合:

```json
"azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', <datetime parameter>)"
```

Azure テーブルの列が datetime 型の場合:

```json
"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', <datetime parameter>, <datetime parameter>)"
```

### <a name="azure-table-as-sink"></a>シンクとしての Azure Table

Azure Table からデータをコピーする場合は、コピー アクティビティのソースの種類を **AzureTableSink** を設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティを **AzureTableSink** に設定する必要があります。 |あり |
| azureTableDefaultPartitionKeyValue |シンクで使用できる既定のパーティション キー値です。 |いいえ |
| azureTablePartitionKeyName |値をパーティション キーとして使用する列の名前を指定します。 指定しない場合、AzureTableDefaultPartitionKeyValue がパーティション キーとして使用されます。 |いいえ |
| azureTableRowKeyName |値を行キーとして使用する列の名前を指定します。 指定しない場合、各行に GUID を使用します。 |いいえ |
| azureTableInsertType |Azure テーブルにデータを挿入する方法です。 このプロパティは、一致するパーティションと列キーを持つ出力テーブル内の既存の行で、値を置換するか結合するかを制御します。 <br/><br/>使用可能な値: **マージ** (既定値) および**置換**。 <br/><br> この設定は、テーブル レベルではなく、行レベルで適用されます。どちらのオプションでも、出力テーブル内の、入力内に存在しない行は削除されません。 これらの設定 (結合と置換) の機能については、「[Insert or Merge Entity (エンティティの挿入または結合)](https://msdn.microsoft.com/library/azure/hh452241.aspx)」および「[Insert or Replace Entity (エンティティの挿入または置換)](https://msdn.microsoft.com/library/azure/hh452242.aspx)」をご覧ください。 |いいえ |
| writeBatchSize |writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。<br/>使用可能な値: 整数 (行数)。 |いいえ (既定値は 10000) |
| writeBatchTimeout |writeBatchSize または writeBatchTimeout に達したときに、Azure テーブルにデータを挿入します。<br/>使用可能な値: 期間。 例: "00:20:00" (20 分) |No (既定値は 90 秒 - ストレージ クライアントの既定のタイムアウト値) |

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Table output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureTableSink",
                "azureTablePartitionKeyName": "<column name>",
                "azureTableRowKeyName": "<column name>"
            }
        }
    }
]
```

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName

azureTablePartitionKeyName として宛先列を使用する前に、"translator" プロパティを使用して、ソース列を宛先列にマップします。

次の例では、ソース列の DivisionID が宛先列の DivisionID にマップされます。

```json
"translator": {
    "type": "TabularTranslator",
    "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
}
```

DivisionID は、パーティション キーとして指定されます。

```json
"sink": {
    "type": "AzureTableSink",
    "azureTablePartitionKeyName": "DivisionID"
}
```

## <a name="data-type-mapping-for-azure-table"></a>Azure Table のデータ型のマッピング

Azure Table をコピー元またはコピー先としてデータをコピーするとき、次の Azure Table のデータ型から Azure Data Factory の中間データ型へのマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

Azure テーブル間でデータの移動時に、次の [Azure Table service により定義されたマッピング](https://msdn.microsoft.com/library/azure/dd179338.aspx)が Azure テーブルの OData 型と .NET 型の間の移動に利用されます。

| Azure Table のデータ型 | Data Factory の中間データ型 | 詳細 |
|:--- |:--- |:--- |
| Edm.Binary |byte[] |バイトの配列 (最大 64 KB)。 |
| Edm.Boolean |bool |ブール値。 |
| Edm.DateTime |DateTime |世界協定時刻 (UTC) を表す 64 ビット値。 サポートされている DateTime 範囲は西暦 1601 年 1 月 1 日 UTC 深夜 12:00 から 始まります。 この範囲は 9999 年 12 月 31 日に終了します。 |
| Edm.Double |double |64 ビットの浮動小数点値。 |
| Edm.Guid |Guid |グローバルで一意となる 128 ビットの識別子。 |
| Edm.Int32 |Int32 |32 ビットの整数。 |
| Edm.Int64 |Int64 |64 ビットの整数。 |
| Edm.String |String |UTF-16 エンコードの値。 文字列値は最大 64 KB になります。 |

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。