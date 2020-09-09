---
title: Azure Data Factory を使用して Xero からデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、Xero のデータをサポートされているシンク データ ストアにコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jingwang
ms.openlocfilehash: 14b3857211eca39ebe09a3a0752ca1d8eee17bc0
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529995"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Azure Data Factory を使用して Xero からデータをコピーする

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のコピー アクティビティを使用して、Xero からデータをコピーする方法について説明します。 この記事は、コピー アクティビティの概要を示している[コピー アクティビティの概要](copy-activity-overview.md)に関する記事に基づいています。

## <a name="supported-capabilities"></a>サポートされる機能

この Xero コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

Xero から、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティによってソースまたはシンクとしてサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関する記事の表をご覧ください。

具体的には、この Xero コネクタは以下をサポートします。

- Xero [プライベート アプリケーション](https://developer.xero.com/documentation/getting-started/getting-started-guide) (パブリック アプリケーションは非サポート)。
- "Reports" を除くすべて Xero テーブル (API エンドポイント)。
- OAuth 1.0 および OAuth 2.0 認証。

Azure Data Factory では接続を有効にする組み込みのドライバーが提供されるので、このコネクタを使用してドライバーを手動でインストールする必要はありません。

## <a name="getting-started"></a>作業の開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、Xero コネクタに固有の Data Factory エンティティの定義に使用されるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

Xero のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | type プロパティは、次のように設定する必要があります:**Xero** | はい |
| connectionProperties | Xero への接続方法を定義するプロパティ グループ。 | はい |
| ***`connectionProperties` の下:*** | | |
| host | Xero サーバーのエンドポイント (`api.xero.com`)。  | はい |
| authenticationType | 使用できる値は `OAuth_2.0` と `OAuth_1.0` です。 | はい |
| consumerKey | Xero アプリケーションに関連付けられているコンシューマー キー。 このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| privateKey | Xero プライベート アプリケーション用に生成された .pem ファイルの秘密キー (「[Create a public/private key pair (公開/秘密キー ペアの作成)](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key)」を参照してください)。 `openssl genrsa -out privatekey.pem 512` を使用し、numbits に 512 を指定して、**privatekey.pem を生成します。** 1024 はサポートされていません。 Unix の改行文字 (\n) も含め、.pem ファイルのすべてのテキストを含めます (以下のサンプルを参照してください)。<br/>このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenantId | Xero アプリケーションに関連付けられているテナント ID。 OAuth 2.0 認証に適用されます。<br>[アクセスを許可されているテナントの確認](https://developer.xero.com/documentation/oauth2/auth-flow)セクションで、テナント ID の取得方法を確認してください。 | OAuth 2.0 認証の場合、はい |
| refreshToken | Xero アプリケーションに関連付けられている OAuth 2.0 更新トークン。アクセス トークンの有効期限が切れるときに、アクセス トークンを更新するために使用されます。 OAuth 2.0 認証に適用されます。 更新トークンの取得方法については、[こちらの記事](https://developer.xero.com/documentation/oauth2/auth-flow)を参照してください。<br>更新トークンは期限切れになりません。 更新トークンを取得するには、[offline_access スコープ](https://developer.xero.com/documentation/oauth2/scopes)を要求する必要があります。<br/>このフィールドを SecureString としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | OAuth 2.0 認証の場合、はい |
| useEncryptedEndpoints | データ ソースのエンドポイントが HTTPS を使用して暗号化されるかどうかを指定します。 既定値は、true です。  | いいえ |
| useHostVerification | TLS 経由で接続するときに、サーバーの証明書内のホスト名がサーバーのホスト名と一致する必要があるかどうかを指定します。 既定値は、true です。  | いいえ |
| usePeerVerification | TLS 経由で接続するときに、サーバーの ID を検証するかどうかを指定します。 既定値は、true です。  | いいえ |

**例:OAuth 2.0 認証**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                },
                "tenantId": "<tenant ID>", 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }            
        }
    }
}
```

**例:OAuth 1.0 認証**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**サンプル秘密キー値:**

Unix の改行文字 (\n) も含め、.pem ファイルのすべてのテキストを含めます。

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>データセットのプロパティ

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 このセクションでは、Xero データセットでサポートされるプロパティの一覧を示します。

Xero からデータをコピーするには、データセットの type プロパティを **XeroObject** に設定します。 次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの type プロパティは、次のように設定する必要があります:**XeroObject** | はい |
| tableName | テーブルの名前。 | いいえ (アクティビティ ソースの "query" が指定されている場合) |

**例**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 このセクションでは、Xero ソースでサポートされるプロパティの一覧を示します。

### <a name="xero-as-source"></a>ソースとしての Xero

Xero からデータをコピーするは、コピー アクティビティのソースの種類を **XeroSource** に設定します。 コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの type プロパティは、次のように設定する必要があります:**XeroSource** | はい |
| query | カスタム SQL クエリを使用してデータを読み取ります。 (例: `"SELECT * FROM Contacts"`)。 | いいえ (データセットの "tableName" が指定されている場合) |

**例:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Xero クエリを指定する際には、次の点に注意してください。

- 複雑な項目を含むテーブルは、複数のテーブルに分割されます。 たとえば、銀行トランザクションには複雑なデータ構造 "LineItems" が含まれるため、銀行トランザクションのデータはテーブル `Bank_Transaction` および `Bank_Transaction_Line_Items` にマップされ、それらをリンクするための外部キーとして `Bank_Transaction_ID` が使用されます。

- Xero データは 2 つのスキーマを通じて使用できます。`Minimal` (既定) と `Complete` です。 Complete スキーマには、目的のクエリを実行する前に追加データ (例: ID 列) を必要とする、前提条件呼び出しテーブルが含まれます。

次のテーブルは、Minimal スキーマと Complete スキーマで同じ情報を持ちます。 API 呼び出しの数を減らすには、Minimal スキーマ (既定) を使用します。

- Bank_Transactions
- Contact_Groups 
- 連絡先 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Invoices 
- Invoices_Credit_Notes
- Invoices_ Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

次のテーブルは、Complete スキーマでのみ照会できます。

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[Lookup アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ
コピー アクティビティによってサポートされているデータ ストアの一覧については、[サポートされているデータ ストア](copy-activity-overview.md#supported-data-stores-and-formats)に関するセクションを参照してください。
