---
title: Azure Data Factory を使用して Google AdWords からデータをコピーする (プレビュー) | Microsoft Docs
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Google AdWords からサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 212f1bcc7bc2bb91a402461209dad4dc288106ad
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090335"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Azure Data Factory を使用して Google AdWords からデータをコピーする (プレビュー)

この記事では、Azure Data Factory でのコピー アクティビティを使用して、Google AdWords からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

> [!IMPORTANT]
> このコネクタは、現在プレビューの段階です。 実際にお試しいただき、フィードバックをお寄せください。 ソリューションでプレビュー版コネクタの依存関係を取得したい場合、[Azure サポート](https://azure.microsoft.com/support/)にお問い合わせください。

## <a name="supported-capabilities"></a>サポートされる機能

この Google AdWords コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)


Google AdWords から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>使用の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Google AdWords コネクタに固有の Data Factory エンティティを定義するために使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Google AdWords のリンクされたサービスには、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**GoogleAdWords** | はい |
| clientCustomerID | レポート データをフェッチする対象の、AdWords アカウントのクライアント顧客 ID。  | はい |
| developerToken | AdWords API へのアクセスを許可するために使用する、マネージャー アカウントに関連付けられている開発者トークン。  このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時に ADF コピー アクティビティでそこからプルするかを選択できます。詳細については、[Key Vault への資格情報の格納](store-credentials-in-key-vault.md)に関するページを参照してください。 | はい |
| authenticationType | 認証に使用される OAuth 2.0 認証メカニズム。 ServiceAuthentication はセルフホステッド IR のみで使用できます。 <br/>使用できる値は、以下のとおりです。**ServiceAuthentication**、**UserAuthentication** | はい |
| refreshToken | UserAuthentication で AdWords へのアクセスを承認するために Google から取得した更新トークン。 このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時に ADF コピー アクティビティでそこからプルするかを選択できます。詳細については、[Key Vault への資格情報の格納](store-credentials-in-key-vault.md)に関するページを参照してください。 | いいえ |
| clientId | 更新トークンを取得するために使用される google アプリケーションのクライアント ID。 このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時に ADF コピー アクティビティでそこからプルするかを選択できます。詳細については、[Key Vault への資格情報の格納](store-credentials-in-key-vault.md)に関するページを参照してください。 | いいえ |
| clientSecret | 更新トークンを取得するために使用される google アプリケーションのクライアント シークレット。 このフィールドを SecureString としてマークして ADF に安全に格納するか、Azure Key Vault にパスワードを格納し、データ コピーの実行時に ADF コピー アクティビティでそこからプルするかを選択できます。詳細については、[Key Vault への資格情報の格納](store-credentials-in-key-vault.md)に関するページを参照してください。 | いいえ |
| email | ServiceAuthentication で使用されるサービス アカウントの電子メール ID。これはセルフホステッド IR のみで使用できます。  | いいえ |
| keyFilePath | サービス アカウントの電子メール アドレスを認証するために使用される .p12 キー ファイルへの完全なパス。これはセルフホステッド IR のみで使用できます。  | いいえ |
| trustedCertPath | SSL 経由で接続するときにサーバーを検証するための信頼された CA 証明書を含む .pem ファイルの完全なパス。 このプロパティは、セルフホステッド IR で SSL を使用する場合にのみ設定できます。 既定値は、IR でインストールされる cacerts.pem ファイルです。  | いいえ |
| useSystemTrustStore | システムの信頼ストアと指定した PEM ファイルのどちらの CA 証明書を使用するかを指定します。 既定値は false です。  | いいえ |

**例:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Google AdWords データセットでサポートされるプロパティの一覧を示します。

Google AdWords からデータをコピーするには、データセットの type プロパティを **GoogleAdWordsObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**GoogleAdWordsObject** | はい |
| tableName | テーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Google AdWords ソースでサポートされるプロパティの一覧を示します。

### <a name="google-adwords-as-source"></a>ソースとしての Google AdWords

Google AdWords からデータをコピーするには、コピー アクティビティでのソースの種類を **GoogleAdWordsSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**GoogleAdWordsSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM MyTable"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>ルックアップ アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関する記事を参照してください。


## <a name="next-steps"></a>次の手順
Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、[サポートされるデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)の表をご覧ください。
