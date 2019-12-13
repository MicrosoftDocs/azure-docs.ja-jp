---
title: Azure Data Factory を使用して HTTP ソースからデータをコピーする
description: Azure Data Factory パイプラインでコピー アクティビティを使用して、クラウドまたはオンプレミスの HTTP ソースからサポートされているシンク データ ストアへデータをコピーする方法について説明します。
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
ms.openlocfilehash: 7c942661beea34e7a49223f4a8e4a4d6c0eb66e1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74929316"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Azure Data Factory を使用して HTTP エンドポイントからデータをコピーする

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-http-connector.md)
> * [現在のバージョン](connector-http.md)

この記事では、Azure Data Factory のコピー アクティビティを使用して、HTTP エンドポイントからデータコピーする方法について説明します。 この記事は、コピー アクティビティの概要が説明されている「[Azure Data Factory のコピー アクティビティ](copy-activity-overview.md)」を基に作成されています。

この HTTP コネクタ、[REST コネクタ](connector-rest.md)および [Web テーブル コネクタ](connector-web-table.md)の違いは次のとおりです。

- **REST コネクタ**では、具体的には RESTful API からのデータのコピーがサポートされます。 
- **HTTP コネクタ**では一般的に、HTTP エンドポイントからデータを取得します (たとえば、ファイルをダウンロードします)。 REST コネクタが使用可能になる前に、HTTP コネクタを使用して RESTful API からデータをコピーする場合があります。これはサポートされますが、REST コネクタと比べると機能は低くなります。
- **Web テーブル コネクタ**では、HTML Web ページからテーブルの内容を抽出します。

## <a name="supported-capabilities"></a>サポートされる機能

この HTTP コネクタは、次のアクティビティでサポートされます。

- [サポートされるソース/シンク マトリックス](copy-activity-overview.md)での[コピー アクティビティ](copy-activity-overview.md)
- [Lookup アクティビティ](control-flow-lookup-activity.md)

HTTP ソースから、サポートされている任意のシンク データ ストアにデータをコピーできます。 コピー アクティビティでソースおよびシンクとしてサポートされているデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。

この HTTP コネクタは、以下のために使用できます。

- HTTP の **GET** または **POST** メソッドを使用して、HTTP/S エンドポイントからデータを取得する。
- 次の認証のいずれかを使用してデータを取得する: **Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate**。
- HTTP 応答をそのままコピーするか、[サポートされているファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md)を使用して解析する。

> [!TIP]
> Data Factory で HTTP コネクタを構成する前に、データ取得のために HTTP 要求をテストするには、ヘッダーおよび本文の要件に関する API 仕様を確認してください。 Postman や Web ブラウザーのようなツールを使用して検証することができます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>作業開始

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

次のセクションでは、HTTP コネクタに固有の Data Factory エンティティの定義に使用できるプロパティについて詳しく説明します。

## <a name="linked-service-properties"></a>リンクされたサービスのプロパティ

HTTP のリンクされたサービスでは、次のプロパティがサポートされます。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | **type** プロパティを **HttpServer** に設定する必要があります。 | はい |
| url | Web サーバーへのベース URL | はい |
| enableServerCertificateValidation | HTTP エンドポイントに接続するときに、サーバーの SSL 証明書の検証を有効にするかどうかを指定します。 HTTPS サーバーが自己署名証明書を使用している場合は、このプロパティを **false** に設定します。 | いいえ<br /> (既定値は **true** です)。 |
| authenticationType | 認証の種類を指定します。 使用できる値は、**Anonymous**、**Basic**、**Digest**、**Windows**、**ClientCertificate** です。 <br><br> このような認証の種類のその他のプロパティと JSON サンプルについては、この表の後のセクションを参照してください。 | はい |
| connectVia | データ ストアに接続するために使用される [Integration Runtime](concepts-integration-runtime.md)。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。 指定されていない場合は、既定の Azure Integration Runtime が使用されます。 |いいえ |

### <a name="using-basic-digest-or-windows-authentication"></a>基本、ダイジェスト、または Windows 認証の使用

**authenticationType** プロパティを **Basic**、**Digest**、または **Windows** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| userName | HTTP エンドポイントにアクセスするために使用するユーザー名。 | はい |
| password | ユーザー (**userName** 値) のパスワード。 Data Factory に安全に格納するには、このフィールドを **SecureString** 型として指定します。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | はい |

**例**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
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

### <a name="using-clientcertificate-authentication"></a>クライアント証明書認証の使用

ClientCertificate 認証を使用するには、**authenticationType** プロパティを **ClientCertificate** に設定します。 前のセクションで説明した汎用的なプロパティに加えて、次のプロパティを指定します。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| embeddedCertData | Base64 でエンコードされた証明書データ。 | **embeddedCertData** または **certThumbprint** のいずれかを指定します。 |
| certThumbprint | セルフホステッド統合ランタイム マシンの証明書ストアにインストールされている証明書の拇印。 セルフホステッド型の統合ランタイムが **connectVia** プロパティで指定されている場合にのみ適用されます。 | **embeddedCertData** または **certThumbprint** のいずれかを指定します。 |
| password | 証明書に関連付けられているパスワード。 Data Factory に安全に格納するには、このフィールドを **SecureString** 型として指定します。 [Azure Key Vault に格納されているシークレットを参照する](store-credentials-in-key-vault.md)こともできます。 | いいえ |

認証に **certThumbprint** を使用し、証明書がローカル コンピューターの個人用ストアにインストールされている場合は、セルフホステッド統合ランタイムに読み取りアクセス許可を付与します。

1. Microsoft 管理コンソール (MMC) を開きます。 **ローカル コンピューター**を対象とする **[証明書]** スナップインを追加します。
2. **[証明書]**  >  **[個人]** を展開し、 **[証明書]** を選択します。
3. 個人用ストアの証明書を右クリックし、 **[すべてのタスク]**  >  **[秘密キーの管理]** の順に選択します。
3. **[セキュリティ]** タブで、証明書に対する読み取りアクセス権を使用して Integration Runtime Host Service (DIAHostService) を実行しているユーザー アカウントを追加します。

**例 1:certThumbprint の使用**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**例 2:embeddedCertData の使用**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
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

データセットを定義するために使用できるセクションとプロパティの完全な一覧については、[データセット](concepts-datasets-linked-services.md)に関する記事をご覧ください。 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

HTTP では、形式ベースのデータセットの `location` 設定において、次のプロパティがサポートされています。

| プロパティ    | 説明                                                  | 必須 |
| ----------- | ------------------------------------------------------------ | -------- |
| type        | データセットの `location` の type プロパティは、**HttpServerLocation** に設定する必要があります。 | はい      |
| relativeUrl | データを含むリソースへの相対 URL。 HTTP コネクタは、次の結合された URL からデータをコピーします。`[URL specified in linked service]/[relative URL specified in dataset]`   | いいえ       |

> [!NOTE]
> サポートされる HTTP 要求のペイロード サイズは約 500 KB です。 Web エンドポイントに渡すペイロード サイズが 500 KB を超える場合は、より小さなチャンクにペイロードをまとめることを検討してください。

**例:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HttpServerLocation",
                "relativeUrl": "<relative url>"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

### <a name="legacy-dataset-model"></a>レガシ データセット モデル

>[!NOTE]
>下位互換性を確保するために、次のデータセット モデルは引き続き現状のままサポートされます。 今後は、前セクションで言及した新しいモデルを使用することをお勧めします。ADF オーサリング UI はこの新しいモデルを生成するように切り替えられています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | データセットの **type** プロパティを **HttpFile** に設定する必要があります。 | はい |
| relativeUrl | データを含むリソースへの相対 URL。 このプロパティが指定されていない場合は、リンクされたサービス定義に指定されている URL のみが使用されます。 | いいえ |
| requestMethod | HTTP メソッド。 使用できる値は、**Get** (既定値) と **Post** です。 | いいえ |
| additionalHeaders | 追加の HTTP 要求ヘッダー。 | いいえ |
| requestBody | HTTP 要求の本文。 | いいえ |
| format | データを解析せずにデータを HTTP エンドポイントからそのまま取得し、ファイル ベースのストアにデータをコピーする場合は、入力と出力の両方のデータセット定義で **format** セクションをスキップします。<br/><br/>コピー中に HTTP 応答の内容を解析する場合に、サポートされるファイル形式の種類は、**TextFormat**、**JsonFormat**、**AvroFormat**、**OrcFormat**、**ParquetFormat**。 **format** の **type** プロパティをいずれかの値に設定します。 詳細については、[JSON 形式](supported-file-formats-and-compression-codecs.md#json-format)、[Text 形式](supported-file-formats-and-compression-codecs.md#text-format)、[Avro 形式](supported-file-formats-and-compression-codecs.md#avro-format)、[Orc 形式](supported-file-formats-and-compression-codecs.md#orc-format)、[Parquet 形式](supported-file-formats-and-compression-codecs.md#parquet-format)の各セクションを参照してください。 |いいえ |
| compression | データの圧縮の種類とレベルを指定します。 詳細については、[サポートされるファイル形式と圧縮コーデック](supported-file-formats-and-compression-codecs.md#compression-support)に関する記事を参照してください。<br/><br/>サポートされる種類は、**GZip**、**Deflate**、**BZip2**、**ZipDeflate** です。<br/>サポートされるレベルは、**Optimal** と **Fastest** です。 |いいえ |

> [!NOTE]
> サポートされる HTTP 要求のペイロード サイズは約 500 KB です。 Web エンドポイントに渡すペイロード サイズが 500 KB を超える場合は、より小さなチャンクにペイロードをまとめることを検討してください。

**例 1:Get メソッド (既定値) を使用する**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**例 2:Post メソッドを使用する**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>コピー アクティビティのプロパティ

このセクションでは、HTTP ソースでサポートされているプロパティの一覧を示します。

アクティビティの定義に利用できるセクションとプロパティの完全な一覧については、[パイプライン](concepts-pipelines-activities.md)に関する記事を参照してください。 

### <a name="http-as-source"></a>ソースとしての HTTP

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

HTTP では、形式ベースのコピー ソースの `storeSettings` 設定において、次のプロパティがサポートされています。

| プロパティ                 | 説明                                                  | 必須 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` の type プロパティは **HttpReadSetting** に設定する必要があります。 | はい      |
| requestMethod            | HTTP メソッド。 <br>使用できる値は、**Get** (既定値) と **Post** です。 | いいえ       |
| addtionalHeaders         | 追加の HTTP 要求ヘッダー。                             | いいえ       |
| requestBody              | HTTP 要求の本文。                               | いいえ       |
| httpRequestTimeout           | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。 | いいえ       |
| maxConcurrentConnections | 同時にストレージ ストアに接続する接続の数。 データ ストアへのコンカレント接続を制限する場合にのみ指定します。 | いいえ       |

**例:**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSetting",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HttpReadSetting",
                    "requestMethod": "Post",
                    "additionalHeaders": "<header key: header value>\n<header key: header value>\n",
                    "requestBody": "<body for POST HTTP request>"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="legacy-source-model"></a>レガシ ソース モデル

>[!NOTE]
>下位互換性を確保するために、次のコピー ソース モデルは引き続き現状のままサポートされます。 今後は、前述した新しいモデルを使用することをお勧めします。ADF オーサリング UI はこの新しいモデルを生成するように切り替えられています。

| プロパティ | 説明 | 必須 |
|:--- |:--- |:--- |
| type | コピー アクティビティのソースの **type** プロパティを **HttpSource** に設定する必要があります。 | はい |
| httpRequestTimeout | HTTP 要求が応答を取得する際のタイムアウト (**TimeSpan** 値)。 この値は、応答データの読み取りのタイムアウトではなく、応答の取得のタイムアウトです。 既定値は **00:01:40** です。  | いいえ |

**例**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Lookup アクティビティのプロパティ

プロパティの詳細については、[ルックアップ アクティビティ](control-flow-lookup-activity.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順

Azure Data Factory のコピー アクティビティによってソースおよびシンクとしてサポートされるデータ ストアの一覧については、「[サポートされるデータ ストアと形式](copy-activity-overview.md#supported-data-stores-and-formats)」を参照してください。
