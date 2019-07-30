---
title: カスタム コグニティブ検索スキル - Azure Search
description: Web API を呼び出すことによって、コグニティブ検索スキルセットの機能を拡張します
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.custom: seojan2018
ms.openlocfilehash: e1ca8a5ce7b615ed8d84c91d8a0d72098c175c44
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672126"
---
# <a name="custom-web-api-skill"></a>カスタム Web API スキル

**カスタム Web API** スキルを使用すると、Web API エンドポイントを呼び出してカスタム操作を提供することで、Cognitive Search を拡張できます。 組み込みのスキルと同様、**カスタム Web API** スキルには入力と出力があります。 入力に応じて、Web API はインデクサーの実行時に JSON ペイロードを受信し、応答としての JSON ペイロードと成功の状態コードを出力します。 正常な応答には、カスタム スキルによって指定された出力が含まれます。 その他の応答はエラーと見なされ、エンリッチメントは実行されません。

JSON ペイロードの構造については、このドキュメントの後のほうで説明します。

> [!NOTE]
> インデクサーは、Web API から返された特定の標準 HTTP 状態コードに対して、再試行を 2 回実行します。 これらの HTTP 状態コードは次のとおりです。 
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| uri | _JSON_ ペイロードの送信先となる Web API の URI です。 **https** URI スキームのみが許可されます |
| httpMethod | ペイロードの送信時に使用されるメソッドです。 許可されるメソッドは `PUT` または `POST` です |
| httpHeaders | キー/値ペアのコレクションです。キーはヘッダーの名前と値を表し、値はペイロードと共に Web API に送信されるヘッダー値を表します。 次のヘッダーは、このコレクションに含めることはできません: `Accept`、`Accept-Charset`、`Accept-Encoding`、`Content-Length`、`Content-Type`、`Cookie`、`Host`、`TE`、`Upgrade`、`Via` |
| timeout | (省略可能) 指定した場合は、API 呼び出しを行う http クライアントのタイムアウト値を示します。 XSD "dayTimeDuration" 値 ([ISO 8601 期間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration)値の制限されたサブセット) として書式設定する必要があります。 たとえば、60 秒の場合は `PT60S` とします。 設定しなかった場合は、既定値の 30 秒が選択されます。 タイムアウトは、最大で 90 秒、最小で 1 秒に設定できます。 |
| batchSize | (省略可能) 1 回の API 呼び出しにつき、どれだけの "データ レコード" が送信されるかを示します (後述の _JSON_ ペイロードの構造を参照してください)。 設定しなかった場合は、既定値の 1000 が選択されます。 このパラメーターを使用して、インデックス作成のスループットと API への負荷の適切なトレードオフを確保することをお勧めします |

## <a name="skill-inputs"></a>スキルの入力

このスキルの "定義済みの" 入力はありません。 このスキルの実行時に既に利用可能な 1 つ以上のフィールドを入力として選択できます。Web API に送信される _JSON_ ペイロードは、さまざまなフィールドを持つことになります。

## <a name="skill-outputs"></a>スキルの出力

このスキルの "定義済みの" 出力はありません。 Web API が返す応答に応じて、出力フィールドを追加し、_JSON_ 応答からそれらを選択できるようにしてください。


## <a name="sample-definition"></a>定義例

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>入力の JSON 構造体のサンプル

この _JSON_ 構造体は、Web API に送信されるペイロードを表します。
これは常に次の制約に従います。

* 最上位レベルのエンティティは `values` と呼ばれます。これはオブジェクトの配列になります。 これらのオブジェクトの数は、最大でも `batchSize` になります
* `values` 配列内の各オブジェクトには、次のプロパティが含まれます
    * `recordId` プロパティ。これは、そのレコードを識別するために使用される**一意の**文字列です。
    * `data` プロパティ。これは _JSON_ オブジェクトです。 `data` プロパティのフィールドは、スキル定義の `inputs` セクションで指定された "名前" に対応します。 これらのフィールドの値は、それらのフィールドの `source` からの値になります (これは、ドキュメント内のフィールドからの値になる可能性もありますし、他のスキルからの値になる可能性もあります)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>出力の JSON 構造体のサンプル

"出力" は、Web API から返された応答に対応します。 Web API は _JSON_ ペイロードのみを返す必要があります (`Content-Type` 応答ヘッダーを参照することで確認されます)。また、次の制約を満たす必要があります。

* `values` という最上位レベルのエンティティが存在する必要があります。これはオブジェクトの配列である必要があります。
* 配列内のオブジェクトの数は、Web API に送信されるオブジェクトの数と同じになっている必要があります。
* 各オブジェクトには次のプロパティが必要です。
   * `recordId` プロパティ
   * `data` プロパティ。このオブジェクトでは、フィールドは `output` 内の "名前" に一致するエンリッチメントであり、その値はエンリッチメントと見なされます。
   * `errors` プロパティ。これは、遭遇したエラーの一覧を示す配列です。これはインデクサーの実行履歴に追加されます。 このプロパティは必須ですが、`null` 値にすることもできます。
   * `warnings` プロパティ。これは、遭遇した警告の一覧を示す配列です。これはインデクサーの実行履歴に追加されます。 このプロパティは必須ですが、`null` 値にすることもできます。
* `values` 配列内のオブジェクトは、要求として Web API に送信される `values` 配列内のオブジェクトと同じ順序である必要はありません。 ただし、`recordId` は関連付けに使用されるため、`recordId` を含んでいる応答内のレコードのうち、Web API への元の要求に含まれないものは破棄されます。

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>エラーになる場合
Web API が利用できない場合や、成功以外の状態コードが送信された場合に加え、次の場合もエラーと見なされます。

* Web API からは成功の状態コードが返されたものの、応答ではそれが `application/json` でないと示されている場合、その応答は無効と見なされ、エンリッチメントは実行されません。
* 応答の `values` 配列内に**無効な**レコードがある場合 (元の要求に `recordId` がないか、重複する値がある場合)、**それらの**レコードに対してエンリッチメントは実行されません。

Web API が利用できないか、HTTP エラーが返された場合は、HTTP エラーに関する入手可能な詳細情報を含んだわかりやすいエラーが、インデクサーの実行履歴に追加されます。

## <a name="see-also"></a>関連項目

+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [コグニティブ検索にカスタム スキルを追加する](cognitive-search-custom-skill-interface.md)
+ [例:コグニティブ検索用のカスタム スキルを作成する](cognitive-search-create-custom-skill-example.md)