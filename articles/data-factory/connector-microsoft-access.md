---
title: Azure Data Factory を使用して Microsoft Access ソースからデータをコピーする | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Microsoft Access ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 2010799dd590808a2c5be7d9e2330bd60e2c7913
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2019
ms.locfileid: "68841938"
---
# <a name="copy-data-from-and-to-microsoft-access-data-stores-using-azure-data-factory"></a>Azure Data Factory を使用して Microsoft Access データ ストアをコピー元またはコピー先としてデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、Microsoft Access データ ストアからデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

Microsoft Access ソースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

## <a name="prerequisites"></a>前提条件

この Microsoft Access コネクタを使用するには、次のことを行う必要があります。

- セルフホステッド統合ランタイムをセットアップする。 詳細については、[セルフホステッド統合ランタイム](create-self-hosted-integration-runtime.md)に関する記事をご覧ください。
- データ ストア用の Microsoft Access ODBC ドライバーを統合ランタイム マシンにインストールする。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Microsoft Access コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Microsoft Access のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**MicrosoftAccess** | はい |
| connectionString | 資格情報部分を除外した ODBC 接続文字列。 接続文字列を指定するか、Integration Runtime マシンに設定したシステム DSN (データ ソース名) を使用することができます (その場合も、リンクされたサービスの資格情報部分をそれに応じて指定する必要があります)。<br>このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。| はい |
| authenticationType | Microsoft Access データ ストアへの接続に使用される認証の種類です。<br/>使用できる値は、以下のとおりです。**Basic** と **Anonymous**。 | はい |
| userName | 基本認証を使用している場合は、ユーザー名を指定します。 | いいえ |
| password | userName に指定したユーザー アカウントのパスワードを指定します。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | いいえ |
| credential | ドライバー固有のプロパティ値の形式で指定された接続文字列のアクセス資格情報の部分。 このフィールドを SecureString とマークします。 | いいえ |
| connectVia | データ ストアに接続するために使用される[統合ランタイム](concepts-integration-runtime.md)。 「[前提条件](#prerequisites)」に記されているように、セルフホステッド統合ランタイムが必要です。 |はい |

**例:**

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Microsoft Access",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
            "authenticationType": "Basic",
            "userName": "<username>",
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、データセットに関する記事をご覧ください。 このセクションでは、Microsoft Access データセット でサポートされるプロパティの一覧を示します。

Microsoft Access からデータをコピーする場合、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**MicrosoftAccessTable** | はい |
| tableName | Microsoft Access 内のテーブルの名前。 | ソースの場合はいいえ (アクティビティ ソースの "query" が指定されている場合)、<br/>シンクの場合ははい |

**例**

```json
{
    "name": "MicrosoftAccessDataset",
    "properties": {
        "type": "MicrosoftAccessTable",
        "linkedServiceName": {
            "referenceName": "<Microsoft Access linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Microsoft Access ソースでサポートされるプロパティの一覧を示します。

### <a name="microsoft-access-as-source"></a>ソースとしての Microsoft Access

Microsoft Access 互換データ ストアからデータをコピーする場合、コピー アクティビティの **source** セクションで次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**MicrosoftAccessSource** | はい |
| query | カスタム クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromMicrosoftAccess",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Microsoft Access input dataset name>",
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
                "type": "MicrosoftAccessSource",
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
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md##supported-data-stores-and-formats)の表をご覧ください。
