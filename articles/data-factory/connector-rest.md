---
title: Azure Data Factory を使用して REST ソースからデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、クラウドまたはオンプレミスの REST ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 34abb93dd54245e03baaa6efe0130d951f7565bf
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927739"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Azure Data Factory を使用して REST エンドポイントからデータをコピーする

この記事では、Azure Data Factory のコピー アクティビティを使用して、REST エンドポイントからデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

この REST コネクタ、[REST コネクタ](connector-http.md)および [Web テーブル コネクタ](connector-web-table.md)の違いは次のとおりです。

- **REST コネクタ**では、特に RESTful API からのデータのコピーがサポートされています。 
- **HTTP コネクタ**では一般的に、HTTP エンドポイントからデータを取得します (たとえば、ファイルをダウンロードします)。 この REST コネクタが使用可能になる前に、HTTP コネクタを使用して RESTful API からデータをコピーする場合があります。これはサポートされますが、REST コネクタと比べると機能は低くなります。
- **Web テーブル コネクタ**では、HTML Web ページからテーブルの内容を抽出します。

## <a name="supported-capabilities"></a>サポートされる機能

REST ソースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

具体的には、この汎用 REST コネクタは以下をサポートします。

- **GET** または **POST** メソッドを使用して、REST エンドポイントからデータを取得する。
- 次のいずれかの認証を使用してデータを取得する。**匿名**、**基本**、**AAD サービス プリンシパル**、**Azure リソースのマネージド ID**。
- REST API 内の **[改ページ位置の自動修正](#pagination-support)** 。
- REST JSON 応答を[そのまま](#export-json-response-as-is)コピーするか、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)を使用して解析する。 **JSON** では応答ペイロードのみがサポートされます。

> [!TIP]
> Data Factory で REST コネクタを構成する前に、データ取得のために要求をテストするには、ヘッダーおよび本文の要件に関する API 仕様を確認してください。 Postman や Web ブラウザーのようなツールを使用して検証することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、REST コネクタに固有の Data Factory エンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

REST のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティには **RestService** を設定する必要があります。 | はい |
| url | REST サービスのベース URL。 | はい |
| enableServerCertificateValidation | エンドポイントに接続するときに、サーバー側の SSL 証明書を検証するかどうか。 | いいえ<br /> (既定値は **true** です)。 |
| authenticationType | REST サービスへの接続に使用される認証の種類。 使用できる値は、**Anonymous**、**Basic**、**AadServicePrincipal**、および **ManagedServiceIdentity** です。 それぞれのプロパティとサンプルについては、以下の対応するセクションを参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

### <a name="use-basic-authentication"></a>基本認証を使用する

**authenticationType** プロパティを **Basic** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| userName | REST エンドポイントにアクセスするために使用するユーザー名。 | はい |
| password | ユーザー (**userName** 値) のパスワード。 Data Factory に安全に格納するには、このフィールドを **SecureString** 型として指定します。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | はい |

**例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
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

### <a name="use-aad-service-principal-authentication"></a>AAD サービス プリンシパル認証を使用する

**authenticationType** プロパティを **AadServicePrincipal** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| servicePrincipalId | Azure Active Directory アプリケーションのクライアント ID を指定します。 | はい |
| servicePrincipalKey | Azure Active Directory アプリケーションのキーを指定します。 このフィールドを **SecureString** としてマークして Data Factory に安全に保管するか、[Azure Key Vault に格納されているシークレットを参照](store-credentials-in-key-vault.md)します。 | はい |
| tenant | アプリケーションが存在するテナントの情報 (ドメイン名またはテナント ID) を指定します。 Azure portal の右上隅にマウスを置くことで取得します。 | はい |
| aadResourceId | 認可を要求する AAD リソースを指定します。例: `https://management.core.windows.net`| はい |

**例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure リソースの認証にマネージド ID を使用する

**authenticationType** プロパティを **ManagedServiceIdentity** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| aadResourceId | 認可を要求する AAD リソースを指定します。例: `https://management.core.windows.net`| はい |

**例**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>データセットのプロパティ

このセクションでは、REST データセットでサポートされているプロパティの一覧を示します。 

データセットの定義に使用できるセクションとプロパティの完全な一覧については、「[データセットとリンクされたサービス](concepts-datasets-linked-services.md)」を参照してください。 

REST からのデータ コピーについては、次のプロパティがサポートされています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティを **RestResource** に設定する必要があります。 | はい |
| relativeUrl | データを含むリソースへの相対 URL。 このプロパティが指定されていない場合は、リンクされたサービス定義に指定されている URL のみが使用されます。 HTTP コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service]/[relative URL specified in dataset]` | いいえ |

データセットに `requestMethod`、`additionalHeaders`、`requestBody`、および `paginationRules` を設定していた場合は現状のまま引き続きサポートされますが、今後のアクティビティ ソースでは新しいモデルを使用することをお勧めします。

**例:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、REST ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 

### <a name="rest-as-source"></a>ソースとしての REST

コピー アクティビティの **source** セクションでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **RestSource** に設定する必要があります | はい |
| requestMethod | HTTP メソッド。 使用できる値は、**Get** (既定値) と **Post** です。 | いいえ |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| requestBody | HTTP 要求の本文。 | いいえ |
| paginationRules | 次のページ要求を作成する改ページ位置の自動修正規則。 詳細については、「[pagination support](#pagination-support)」(改ページ位置の自動調整のサポート) セクションを参照してください。 | いいえ |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。  | いいえ |
| requestInterval | 次のページに対する要求を送信する前に待機する時間。 既定値は **00:00:01** です。 |  いいえ |

>[!NOTE]
>REST コネクタは、`additionalHeaders` で指定された "Accept" ヘッダーをすべて無視します。 REST コネクタは JSON の応答のみをサポートし、`Accept: application/json` のヘッダーを自動生成します。

**例 1:改ページ位置の自動修正で Get メソッドを使用する**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**例 2:Post メソッドを使用する**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>改ページ位置の自動調整のサポート

通常、REST API では、1 つの要求の応答ペイロードのサイズが適切な数値を超えないように制限されています。大量のデータが返される場合は、結果が複数のページに分割され、呼び出し元に連続する要求を送信して結果の次のページを取得することが要求されます。 通常、1 つのページに対する要求は動的で、前のページの応答から返される情報で構成されます。

この汎用 REST コネクタでは、次の改ページ位置の自動修正パターンをサポートしています。 

* 次の要求の絶対または相対 URL = 現在の応答本文のプロパティ値
* 次の要求の絶対または相対 URL = 現在の応答ヘッダーのヘッダー値
* 次の要求のクエリ パラメーター = 現在の応答本文のプロパティ値
* 次の要求のクエリ パラメーター = 現在の応答ヘッダーのヘッダー値
* 次の要求のヘッダー = 現在の応答本文のプロパティ値
* 次の要求のヘッダー = 現在の応答ヘッダーのヘッダー値

**改ページ位置の自動修正規則**はデータセット内のディクショナリとして定義されます。これには大文字と小文字を区別する 1 つまたは複数のキーと値のペアが含まれます。 構成は 2 番目のページから始まる要求を生成するのに使用されます。 コネクタで HTTP 状態コード 204 (コンテンツなし) が取得されるか、"paginationRules" 内のいずれかの JSONPath 式で null が返されると、繰り返し処理が停止されます。

改ページ位置の自動修正規則で**サポートされるキー**:

| Key | 説明 |
|:--- |:--- |
| AbsoluteUrl | 次の要求を発行する URL を示します。 これは、**絶対 URL と相対 URL のどちらか**です。 |
| QueryParameters.*request_query_parameter* または QueryParameters['request_query_parameter'] | "request_query_parameter" は、次の HTTP 要求 URL 内で 1 つのクエリ パラメーター名を参照するユーザー定義です。 |
| Headers.*request_header* または Headers['request_header'] | "request_header" は、次の HTTP 要求内で 1 つのヘッダー名を参照するユーザー定義です。 |

改ページ位置の自動修正規則で**サポートされる値**:

| 値 | 説明 |
|:--- |:--- |
| Headers.*response_header* または Headers['response_header'] | "response_header" は、現在の HTTP 応答内で 1 つのヘッダー名を参照するユーザー定義で、次の要求を発行するために使用される値です。 |
| (応答本文のルートを表す) "$" から始まる JSONPath 式 | 応答本文には、1 つの JSON オブジェクトのみを含める必要があります。 JSONPath 式では、1 つのプリミティブ値が返される必要があります。この値は、次の要求を発行するために使用されます。 |

**例:**

Facebook Graph API によって、次の構造で応答が返されます。この場合、次のページの URL は ***paging.next*** で表されます。

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

対応する REST コピー アクティビティ ソース構成、特に `paginationRules` は次のようになります。

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="export-json-response-as-is"></a>JSON の応答をそのままエクスポートする

この REST コネクタを使用して REST API JSON の応答をそのままさまざまなファイル ベースのストアにエクスポートできます。 このようなスキーマに依存しないコピーを実現するには、データセットの "構造" ("*スキーマ*" とも呼ばれる) のセクションと、コピー アクティビティでのスキーマ マッピングをスキップします。

## <a name="schema-mapping"></a>スキーマ マッピング

REST エンドポイントから表形式のシンクにデータをコピーするには、[スキーマ マッピング](copy-activity-schema-and-type-mapping.md#schema-mapping)を参照してください。

## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
