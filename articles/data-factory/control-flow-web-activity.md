---
title: "Azure Data Factory の Web アクティビティ | Microsoft Docs"
description: "Data Factory によってサポートされている制御フロー アクティビティの 1 つである Web アクティビティを使用して、パイプラインから REST エンドポイントを呼び出す方法について説明します。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: shlo
ms.openlocfilehash: dec7a446251545461f32cddea4d8c3e433dc21e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="web-activity-in-azure-data-factory"></a>Azure Data Factory の Web アクティビティ
Web アクティビティを使用すると、Data Factory パイプラインからカスタム REST エンドポイントを呼び出すことができます。 このアクティビティで使用したり、アクセスしたりするデータセットやリンクされたサービスを渡すことができます。 

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
name | Web アクティビティの名前 | String | あり
type | **WebActivity** に設定する必要があります。 | String | あり
method | ターゲット エンドポイント用の Rest API メソッド。 | 文字列 をオンにします。 <br/><br/>サポートされるタイプ: "GET"、"POST"、"PUT" | あり
url | ターゲット エンドポイントおよびパス | 文字列 (または文字列の resultType を含む式) | あり
headers | 要求に送信されるヘッダー。 たとえば、要求で言語と種類を設定するには、次のようにします。`"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 文字列 (または文字列の resultType を含む式) | あり。Content-type ヘッダーが必要です。 `"headers":{ "Content-Type":"application/json"}`
body | エンドポイントに送信されるペイロードを表します。 POST/PUT メソッドには必須です。  | 文字列 (または文字列の resultType を含む式)。 <br/><br/>「[要求ペイロードのスキーマ](#request-payload-schema)」セクションにある要求ペイロードのスキーマを参照してください。 | いいえ
[認証] | エンドポイントを呼び出すために使用される認証方法。 サポートされるタイプは "Basic" または "ClientCertificate" です。 詳細については、「[認証](#authentication)」セクションを参照してください。 認証が必要ない場合は、このプロパティを除外します。 | 文字列 (または文字列の resultType を含む式) | いいえ
データセット | エンドポイントに渡されるデータセットの一覧。 | データセット参照の配列。 空の配列にすることができます。 | あり
linkedServices | エンドポイントに渡されるリンクされたサービスの一覧。 | リンクされたサービスの参照の配列。 空の配列にすることができます。 | あり

> [!NOTE]
> Web アクティビティが呼び出す REST エンドポイントは、型 JSON の応答を返す必要があります。

## <a name="authentication"></a>認証

### <a name="none"></a>なし
認証が必要ない場合は、"authentication" プロパティを含めないでください。

### <a name="basic"></a>基本
基本認証で使用するユーザー名とパスワードを指定します。 

```json
"authentication":{  
   "type":"Basic,
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

## <a name="next-steps"></a>次のステップ
Data Factory でサポートされている他の制御フロー アクティビティを参照してください。 

- [ExecutePipeline アクティビティ](control-flow-execute-pipeline-activity.md)
- [ForEach アクティビティ](control-flow-for-each-activity.md)
- [GetMetadata アクティビティ](control-flow-get-metadata-activity.md)
- [ルックアップ アクティビティ](control-flow-lookup-activity.md)
