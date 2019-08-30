---
title: Azure Data Factory を使用して Netezza からデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Netezza のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: c3c179cfbf86c2dddfb34b46540aba8898038751
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966488"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory"></a>Azure Data Factory を使用して Netezza からデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Netezza からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

## <a name="supported-capabilities"></a>サポートされる機能

Netezza から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

Azure Data Factory では、接続を可能にする組み込みのドライバーが提供されます。 このコネクタを使用するためにドライバーを手動でインストールする必要はありません。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>作業開始

コピー アクティビティを使用するパイプラインは、.NET SDK、Python SDK、Azure PowerShell、REST API、または Azure Resource Manager テンプレートを使用して作成できます。 コピー アクティビティを使用するパイプラインを作成する詳細な手順については、[コピー アクティビティのチュートリアル](quickstart-create-data-factory-dot-net.md)をご覧ください。

次のセクションでは、Netezza コネクタに固有の Data Factory エンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Netezza のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **Netezza** に設定する必要があります。 | はい |
| connectionString | Netezza に接続するための ODBC 接続文字列。 <br/>Data Factory に安全に格納するには、このフィールドを SecureString として指定します。 パスワードを Azure Key Vault に格納して、接続文字列から `pwd` 構成をプルすることもできます。 詳細については、次のサンプルと「[Azure Key Vault への資格情報の格納](store-credentials-in-key-vault.md)」の記事を参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

一般的な接続文字列は `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>` です。 次の表では、設定できる他のプロパティについて説明します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| SecurityLevel | ドライバーがデータ ストアへの接続に使用するセキュリティ (SSL/TLS) のレベル。 例: `SecurityLevel=preferredSecured`. サポートされる値は次のとおりです。<br/>- **セキュリティで保護されていない接続のみ** (**onlyUnSecured**):ドライバーで SSL を使用しません。<br/>- **セキュリティで保護されていない接続を優先 (preferredUnSecured) (既定値)** :サーバーによって選択肢が提供される場合、ドライバーでは SSL を使用しません。 <br/>- **セキュリティで保護されている接続を優先 (preferredSecured)** :サーバーによって選択肢が提供される場合、ドライバーで SSL を使用します。 <br/>- **セキュリティで保護されている接続のみ (onlySecured)** :SSL 接続を利用できない場合、ドライバーは接続しません。 | いいえ |
| CaCertFile | サーバーによって使用される SSL 証明書の完全パス。 例: `CaCertFile=<cert path>;`| はい (SSL が有効になっている場合) |

**例**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例: パスワードを Azure Key Vault に格納する**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

このセクションでは、Netezza データセットでサポートされているプロパティの一覧を示します。

データセットの定義に使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関するページをご覧ください。

Netezza からデータをコピーするには、データセットの **type** プロパティを **NetezzaTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります: **NetezzaTable** | はい |
| tableName | テーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、Netezza ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。

### <a name="netezza-as-source"></a>ソースとしての Netezza

Netezza からデータをコピーするには、コピー アクティビティの **source** のタイプを **NetezzaSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **NetezzaSource** に設定する必要があります。 | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 例: `"SELECT * FROM MyTable"` | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
