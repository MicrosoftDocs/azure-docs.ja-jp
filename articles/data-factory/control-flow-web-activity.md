---
title: Azure Data Factory の Web アクティビティ | Microsoft Docs
description: Data Factory によってサポートされている制御フロー アクティビティの 1 つである Web アクティビティを使用して、パイプラインから REST エンドポイントを呼び出す方法について説明します。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 73770e559af8a999c17fff5ea1aa6ee53ac17e83
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141583"
---
# <a name="web-activity-in-azure-data-factory"></a>Azure Data Factory の Web アクティビティ
Web アクティビティを使用すると、Data Factory パイプラインからカスタム REST エンドポイントを呼び出すことができます。 このアクティビティで使用したり、アクセスしたりするデータセットやリンクされたサービスを渡すことができます。

> [!NOTE]
> Web アクティビティでは、公開されている URL のみを呼び出すことができます。 プライベート仮想ネットワークでホストされている URL についてはサポートされていません。

## <a name="syntax"></a>構文

```json
{
   "name":"MyWebActivity",
   "type":"WebActivity",
   "typeProperties":{
      "method":"Post",
      "url":"<URLEndpoint>",
      "headers":{
         "Content-Type":"application/json"
      },
      "authentication":{
         "type":"ClientCertificate",
         "pfx":"****",
         "password":"****"
      },
      "datasets":[
         {
            "referenceName":"<ConsumedDatasetName>",
            "type":"DatasetReference",
            "parameters":{
               ...
            }
         }
      ],
      "linkedServices":[
         {
            "referenceName":"<ConsumedLinkedServiceName>",
            "type":"LinkedServiceReference"
         }
      ]
   }
}

```

## <a name="type-properties"></a>型のプロパティ

プロパティ | 説明 | 使用できる値 | 必須
-------- | ----------- | -------------- | --------
名前 | Web アクティビティの名前 | String | はい
type | **WebActivity** に設定する必要があります。 | String | はい
method | ターゲット エンドポイント用の Rest API メソッド。 | 文字列 をオンにします。 <br/><br/>サポートされている型"GET"、"POST"、"PUT" | はい
url | ターゲット エンドポイントおよびパス | 文字列 (または文字列の resultType を含む式)。 エンドポイントからの応答がない場合、アクティビティは 1 分でタイムアウトになり、エラーが発生します。 | はい
headers | 要求に送信されるヘッダー。 たとえば、要求で言語と種類を設定するには、次のようにします。`"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 文字列 (または文字列の resultType を含む式) | あり。Content-type ヘッダーが必要です。 `"headers":{ "Content-Type":"application/json"}`
body | エンドポイントに送信されるペイロードを表します。  | 文字列 (または文字列の resultType を含む式)。 <br/><br/>「[要求ペイロードのスキーマ](#request-payload-schema)」セクションにある要求ペイロードのスキーマを参照してください。 | POST/PUT メソッドには必須です。
認証 | エンドポイントを呼び出すために使用される認証方法。 サポートされるタイプは "Basic" または "ClientCertificate" です。 詳細については、「[認証](#authentication)」セクションを参照してください。 認証が必要ない場合は、このプロパティを除外します。 | 文字列 (または文字列の resultType を含む式) | いいえ
datasets | エンドポイントに渡されるデータセットの一覧。 | データセット参照の配列。 空の配列にすることができます。 | はい
linkedServices | エンドポイントに渡されるリンクされたサービスの一覧。 | リンクされたサービスの参照の配列。 空の配列にすることができます。 | はい

> [!NOTE]
> Web アクティビティが呼び出す REST エンドポイントは、型 JSON の応答を返す必要があります。 エンドポイントからの応答がない場合、アクティビティは 1 分でタイムアウトになり、エラーが発生します。

次の表に、JSON コンテンツの要件を示します。

| 値の型 | 要求本文 | 応答本文 |
|---|---|---|
|JSON オブジェクト | サポートされています | サポートされています |
|JSON 配列 | サポートされています <br/>(バグがあるため、現在、JSON 配列は動作していません。 修正が進行中です)。 | サポートされていません |
| JSON 値 | サポートされています | サポートされていません |
| 非 JSON 型 | サポートされていません | サポートされていません |
||||

## <a name="authentication"></a>Authentication

### <a name="none"></a>なし
認証が必要ない場合は、"authentication" プロパティを含めないでください。

### <a name="basic"></a>Basic
基本認証で使用するユーザー名とパスワードを指定します。

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>クライアント証明書
PFX ファイルの Base64 でエンコードされたコンテンツとパスワードを指定します。

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>マネージド ID

データ ファクトリのマネージド ID を使用してアクセス トークンの要求対象となるリソース URI を指定します。 Azure Resource Management API を呼び出すには、`https://management.azure.com/` を使用します。 マネージド ID が機能する方法について詳しくは、[Azure リソースのマネージド ID の概要](/azure/active-directory/managed-identities-azure-resources/overview)に関するページを参照してください。

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

## <a name="request-payload-schema"></a>要求ペイロードのスキーマ
POST/PUT メソッドを使用する場合、body プロパティは、エンドポイントに送信されるペイロードを表します。 そのペイロードの一部としてリンクされたサービスやデータセットを渡すことができます。 ペイロードのスキーマを次に示します。

```json
{
    "body": {
        "myMessage": "Sample",
        "datasets": [{
            "name": "MyDataset1",
            "properties": {
                ...
            }
        }],
        "linkedServices": [{
            "name": "MyStorageLinkedService1",
            "properties": {
                ...
            }
        }]
    }
}
```

## <a name="example"></a>例
この例では、パイプライン内の Web アクティビティが REST エンドポイントを呼び出します。 Azure SQL リンクされたサービスと Azure SQL データセットがエンドポイントに渡されます。 REST エンドポイントは、Azure SQL 接続文字列を使用して Azure SQL サーバーに接続し、SQL サーバーのインスタンスの名前を返します。

### <a name="pipeline-definition"></a>パイプラインの定義

```json
{
    "name": "<MyWebActivityPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyWebActivity>",
                "type": "WebActivity",
                "typeProperties": {
                    "method": "Post",
                    "url": "@pipeline().parameters.url",
                    "headers": {
                        "Content-Type": "application/json"
                    },
                    "authentication": {
                        "type": "ClientCertificate",
                        "pfx": "*****",
                        "password": "*****"
                    },
                    "datasets": [
                        {
                            "referenceName": "MySQLDataset",
                            "type": "DatasetReference",
                            "parameters": {
                                "SqlTableName": "@pipeline().parameters.sqlTableName"
                            }
                        }
                    ],
                    "linkedServices": [
                        {
                            "referenceName": "SqlLinkedService",
                            "type": "LinkedServiceReference"
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "sqlTableName": {
                "type": "String"
            },
            "url": {
                "type": "String"
            }
        }
    }
}

```

### <a name="pipeline-parameter-values"></a>パイプライン パラメーターの値

```json
{
    "sqlTableName": "department",
    "url": "https://adftes.azurewebsites.net/api/execute/running"
}

```

### <a name="web-service-endpoint-code"></a>Web サービスのエンドポイント コード

```csharp

[HttpPost]
public HttpResponseMessage Execute(JObject payload)
{
    Trace.TraceInformation("Start Execute");

    JObject result = new JObject();
    result.Add("status", "complete");

    JArray datasets = payload.GetValue("datasets") as JArray;
    result.Add("sinktable", datasets[0]["properties"]["typeProperties"]["tableName"].ToString());

    JArray linkedServices = payload.GetValue("linkedServices") as JArray;
    string connString = linkedServices[0]["properties"]["typeProperties"]["connectionString"].ToString();

    System.Data.SqlClient.SqlConnection sqlConn = new System.Data.SqlClient.SqlConnection(connString);

    result.Add("sinkServer", sqlConn.DataSource);

    Trace.TraceInformation("Stop Execute");

    return this.Request.CreateResponse(HttpStatusCode.OK, result);
}

```

## <a name="next-steps"></a>次の手順
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。

- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
