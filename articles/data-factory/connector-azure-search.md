---
title: "Azure Data Factory を使用して Search インデックスにデータをコピーする | Microsoft Docs"
description: "Azure Data Factory パイプラインでコピー アクティビティを使用して、Azure Search インデックスにデータをコピーまたはプッシュする方法について説明します。"
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ae4537ee37103392f78c4fa554984f1375634f17
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Azure Data Factory を使用して Azure Search インデックスにデータをコピーする

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [バージョン 1 - GA](v1/data-factory-azure-search-connector.md)
> * [バージョン 2 - プレビュー](connector-azure-search.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Azure Search インデックスにデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!NOTE]
> この記事は、現在プレビュー段階にある Data Factory のバージョン 2 に適用されます。 一般公開 (GA) されている Data Factory サービスのバージョン 1 を使用している場合は、[V1 の Azure Search コネクタ](v1/data-factory-azure-search-connector.md)に関する記事を参照してください。

## <a name="supported-scenarios"></a>サポートされるシナリオ

サポートされているソース データ ストアから Azure Search インデックスにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

## <a name="getting-started"></a>使用の開始
コピー アクティビティを含むパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを含むパイプラインを作成するための詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

以下のセクションでは、Azure Search コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティの詳細を説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Azure Search のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティを **AzureSearch** に設定する必要があります。 | はい |
| URL | Azure Search サービスの URL。 | はい |
| key | Azure Search サービスの管理者キー。 このフィールドは、SecureString とマークします。 | はい |
| connectVia | データ ストアに接続するために[統合ランタイム](concepts-integration-runtime.md)が使用されます。 Azure 統合ランタイムまたはセルフホステッド統合ランタイム (データ ストアがプライベート ネットワークにある場合) を使用できます。 指定されていない場合は、既定の Azure 統合ランタイムが使用されます。 |いいえ |

> [!IMPORTANT]
> Azure Search のリンクされたサービスでクラウド データ ストアのデータを Azure Search インデックスにコピーする場合は、connactVia で明示的なリージョンを指定して Azure 統合ランタイムを参照する必要があります。 Azure Search が存在するリージョンを設定します。 詳細については、[Azure 統合ランタイム] (concepts-integration-runtime.md#azure-integration-runtime) を参照してください。

**例:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Azure Search データセットでサポートされるプロパティの一覧を示します。

Azure Search にデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、**AzureSearchIndex** を設定する必要があります。 | はい |
| indexName | Azure Search インデックスの名前。 Data Factory では、インデックスは作成されません。 Azure Search にこのインデックスが存在する必要があります。 | はい |

**例:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Azure Search ソースでサポートされるプロパティの一覧を示します。

### <a name="azure-search-as-sink"></a>シンクとしての Azure Search

Azure Search にデータをコピーするには、コピー アクティビティのソースの種類を **AzureSearchIndexSink** に設定します。 コピー アクティビティの **sink** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは **AzureSearchIndexSink** を設定する必要があります。 | はい |
| WriteBehavior | ドキュメントがそのインデックスに既に存在する場合に、マージするか置換するかを指定します。 詳細については、「[WriteBehavior プロパティ](#writebehavior-property)」を参照してください。<br/><br/>使用可能な値: **マージ** (既定値) および **アップロード**。 | いいえ |
| WriteBatchSize | バッファー サイズが writeBatchSize に達したときに、Azure Search インデックスにデータをアップロードします。 詳細については、「[WriteBatchSize プロパティ](#writebatchsize-property)」を参照してください。<br/><br/>使用可能な値: 1 ～ 1,000 の整数。既定値は 1000 です。 | いいえ |

### <a name="writebehavior-property"></a>WriteBehavior プロパティ

データを書き込むときに AzureSearchSink で upsert されます。 つまり、ドキュメントを書き込むときに Azure Search インデックスにそのドキュメントのキーが既に存在する場合は、Azure Search は競合の例外をスローするのではなく、既存のドキュメントを更新します。

AzureSearchSink で提供される upsert 動作 (AzureSearch SDK の使用による) は、次の 2 とおりあります。

- **マージ**: 新しいドキュメントのすべての列を既存の列と結合します。 新しいドキュメント内に null 値を持つ列がある場合は、既存の列の値が保持されます。
- **アップロード**: 既存のドキュメントが新しいドキュメントで置き換えられます。 新しいドキュメントで指定されていない列の場合は、既存のドキュメントに null 以外の値があるかどうかに関係なく、値は null に設定されます。

既定の動作は**マージ**です。

### <a name="writebatchsize-property"></a>WriteBatchSize プロパティ

Azure Search サービスでは、バッチとしてのドキュメントの書き込みをサポートていします。 バッチには、1 ～ 1,000 のアクションを含めることができます。 1 つのアクションで、1 つのドキュメントのアップロード/マージ操作の実行を処理します。

**例:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "WriteBehavior": "Merge"
            }
        }
    }
]
```

### <a name="data-type-support"></a>データ型のサポート

次の表は、Azure Search データ型がサポートされているかどうかを示します。

| Azure Search データ型 | Azure Search のシンクでサポートされている |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| ブール | Y |
| DataTimeOffset | Y |
| 文字列配列 | N |
| GeographyPoint | N |

## <a name="next-steps"></a>次のステップ
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
