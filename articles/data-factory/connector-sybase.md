---
title: Azure Data Factory を使用した Sybase からのデータ コピー | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Sybase からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 55ff6d37f18f4ffa2f12e17bd33dd196b77f79af
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025778"
---
# <a name="copy-data-from-sybase-using-azure-data-factory"></a>Azure Data Factory を使用して Sybase からデータをコピーする
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Version 1](v1/data-factory-onprem-sybase-connector.md)
> * [現在のバージョン](connector-sybase.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、Sybase データベースからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Sybase データベースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Sybase コネクタは以下をサポートします。

- SAP Sybase SQL Anywhere (ASA) **バージョン 16 以降** (IQ および ASE はサポートされません)。
- **基本**または **Windows** 認証を使用したデータのコピー。

## <a name="prerequisites"></a>前提条件

この Sybase コネクタを使用するには、次の手順が必要です。

- セルフホステッド統合ランタイムをセットアップします。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。
- 統合ランタイムのコンピューターに、[Sybase iAnywhere.Data.SQLAnywhere 用データ プロバイダー](https://go.microsoft.com/fwlink/?linkid=324846) (16 以降) をインストールします。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Sybase コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Sybase のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Sybase** | はい |
| server | Sybase サーバーの名前です。 |はい |
| database | Sybase データベースの名前です。 |はい |
| authenticationType | Sybase データベースへの接続に使用される認証の種類です。<br/>使用できる値は、以下のとおりです。**Basic** および **Windows**。 |はい |
| username | Sybase データベースに接続するユーザー名を指定します。 |はい |
| password | ユーザー名に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 |はい |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Sybase データセット でサポートされるプロパティの一覧を示します。

Sybase からデータをコピーするには、データセットの type プロパティを **RelationalTable** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**RelationalTable** | はい |
| tableName | Sybase データベースのテーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "SybaseDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Sybase ソースでサポートされるプロパティの一覧を示します。

### <a name="sybase-as-source"></a>ソースとしての Sybase

Sybase からデータをコピーするには、コピー アクティビティのソース タイプを **RelationalSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**RelationalSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
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
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sybase"></a>Sybase のデータ型マッピング

Sybase からデータをコピーするとき、Sybase のデータ型から Azure Data Factory の中間データ型への、以下のマッピングが使用されます。 コピー アクティビティでソースのスキーマとデータ型がシンクにマッピングされるしくみについては、[スキーマとデータ型のマッピング](copy-activity-schema-and-type-mapping.md)に関する記事を参照してください。

Sybase では、T-SQL 型をサポートします。 SQL 型から Azure Data Factory 中間データ型へのマッピング テーブルについては、「[Azure SQL Database Connector - data type mapping (Azure SQL Database Connector - データ型マッピング)](connector-azure-sql-database.md#data-type-mapping-for-azure-sql-database)」セクションを参照してください。


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
