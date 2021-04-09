---
title: コンテンツ タイプを処理する
description: Azure Logic Apps の設計時および実行時にワークフロー内でさまざまなコンテンツの種類を処理する方法について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/20/2018
ms.openlocfilehash: 04642e69adba82cf33b933829d72c51109ad2ee1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91578186"
---
# <a name="handle-content-types-in-azure-logic-apps"></a>Azure Logic Apps における各種コンテンツの扱い

ロジック アプリには、JSON、XML、フラット ファイル、バイナリ データなど、さまざまな種類のコンテンツを渡すことができます。 Logic Apps はあらゆるコンテンツ タイプをサポートしています。ネイティブでサポートされるコンテンツ タイプもあり、その場合は、ロジック アプリ側でのキャストや変換は不要です。 一方、必要に応じてキャストまたは変換が必要なコンテンツ タイプもあります。 この記事では、Logic Apps による各種コンテンツの扱いと、それらを必要に応じて正しくキャストまたは変換する方法について説明します。

Logic Apps では各種コンテンツの適切な処理方法を判断するために、HTTP 呼び出しに含まれる `Content-Type` ヘッダーの値が使用されます。その例を次に示します。

* [application/json](#application-json) (ネイティブ タイプ)
* [text/plain](#text-plain) (ネイティブ タイプ)
* [application/xml および application/octet-stream](#application-xml-octet-stream)
* [その他のコンテンツ タイプ](#other-content-types)

<a name="application-json"></a>

## <a name="applicationjson"></a>application/json

Logic Apps は、コンテンツ タイプが *application/json* であるすべての要求を JavaScript Notation (JSON) オブジェクトとして格納して処理します。 JSON コンテンツは、キャストせずに既定で解析することができます。 "application/json" コンテンツ タイプのヘッダーを含む要求は、式を使って解析できます。 次の例では、`animal-type` 配列から、キャストなしで `dog` という値が返されます。 
 
`@body('myAction')['animal-type'][0]` 
  
  ```json
  {
    "client": {
       "name": "Fido",
       "animal-type": [ "dog", "cat", "rabbit", "snake" ]
    }
  }
  ```

ヘッダーが指定されない JSON データを扱う場合は、次の例のように [json() 関数](../logic-apps/workflow-definition-language-functions-reference.md#json)を使って、データを手動で JSON にキャストすることができます。 
  
`@json(triggerBody())['animal-type']`

### <a name="create-tokens-for-json-properties"></a>JSON のプロパティに使用するトークンの作成

Logic Apps には、JSON コンテンツのプロパティをロジック アプリのワークフローの中で簡単に参照して使用できるように、そうしたプロパティを表すユーザー フレンドリなトークンを生成する機能が用意されています。

* **要求トリガー**

  ロジック アプリ デザイナーでは、このトリガーを使って、受け取りたいペイロードを表す JSON スキーマを指定することができます。 
  デザイナーは、このスキーマを使って JSON コンテンツを解析することにより、JSON コンテンツ内の各種プロパティを表すユーザー フレンドリなトークンを生成します。 
  その後、ロジック アプリのワークフローの至る所で、これらのプロパティを簡単に参照して使用することができます。 
  
  スキーマがない場合は、自分で生成することができます。 
  
  1. 要求トリガーで **[サンプルのペイロードを使用してスキーマを生成する]** を選択します。  
  
  2. **[サンプルの JSON ペイロードを入力するか、貼り付けます]** でサンプル ペイロードを指定し、 **[完了]** を選択します。 次に例を示します。 

     ![[HTTP 要求の受信時] アクションとサンプルの JSON ペイロードを示すスクリーンショット。](./media/logic-apps-content-type/request-trigger.png)

     生成されたスキーマがトリガーに表示されます。

     ![サンプル JSON ペイロードの指定](./media/logic-apps-content-type/generated-schema.png)

     コード ビュー エディターでは、この要求トリガーの基になる定義が次のように表示されます。

     ```json
     "triggers": { 
        "manual": {
           "type": "Request",
           "kind": "Http",
           "inputs": { 
              "schema": {
                 "type": "object",
                 "properties": {
                    "client": {
                       "type": "object",
                       "properties": {
                          "animal-type": {
                             "type": "array",
                             "items": {
                                "type": "string"
                             },
                          },
                          "name": {
                             "type": "string"
                          }
                       }
                    }
                 }
              }
           }
        }
     }
     ```

  3. 要求には必ず `Content-Type` ヘッダーを追加し、その値を `application/json` に設定してください。

* **Parse JSON アクション**

  ロジック アプリ デザイナーでは、このアクションを使って JSON 出力を解析し、JSON コンテンツ内の各種プロパティを表すユーザー フレンドリなトークンを生成することができます。 
  その後、ロジック アプリのワークフローの至る所で、これらのプロパティを簡単に参照して使用することができます。 要求トリガーと同様、解析したい JSON コンテンツの JSON スキーマを指定または生成することができます。 
  これにより、Azure Service Bus や Azure Cosmos DB などのデータが利用しやすくなります。

  ![JSON の解析](./media/logic-apps-content-type/parse-json.png)

<a name="text-plain"></a>

## <a name="textplain"></a>text/plain

ロジック アプリは、`Content-Type` ヘッダーが `text/plain` に設定された HTTP メッセージを受信すると、それらのメッセージを生の形式で格納します。 それらのメッセージをキャストせずに後続のアクションに追加した場合、`Content-Type` ヘッダーが `text/plain` に設定された状態で要求が送信されます。 

たとえばフラット ファイルを扱っているときに、`Content-Type` ヘッダーが `text/plain` コンテンツ タイプに設定された次のような HTTP 要求を受け取ったとします。

`Date,Name,Address`</br>
`Oct-1,Frank,123 Ave`

その後、この要求を後続のアクションの中で、別の要求の本文として送信した場合 (`@body('flatfile')` など)、その 2 つ目の要求の `Content-Type` ヘッダーも `text/plain` に設定されます。 プレーンテキストではあるもののヘッダーが指定されていないデータを扱う場合、次の式のように、[string() 関数](../logic-apps/workflow-definition-language-functions-reference.md#string)を使えば、そのデータを手動でテキストにキャストすることができます。 

`@string(triggerBody())`

<a name="application-xml-octet-stream"></a>

## <a name="applicationxml-and-applicationoctet-stream"></a>application/xml と application/octet-stream

Logic Apps は、HTTP 要求または HTTP 応答で受信した `Content-Type` を常に維持します。 そのため、`Content-Type` を `application/octet-stream` に設定したコンテンツがロジック アプリによって受信されたとき、そのコンテンツをキャストせずに後続のアクションに渡した場合、そこから送信される要求の `Content-Type` も `application/octet-stream` に設定されます。 こうすることで、ワークフロー内を移動する過程でデータが失われないことを Logic Apps は保証することができます。 ただし、アクションの状態 (入力と出力) は、ワークフローを移動する間、JSON オブジェクトに格納されます。 

## <a name="converter-functions"></a>コンバーター関数

Logic Apps は一部のデータ型について、その型を維持するために、`$content` ペイロードと `$content-type` の両方を保持する適切なメタデータを含む、Base64 でエンコードされたバイナリ文字列にコンテンツを変換します (自動的に変換される)。 

以下に示したのは、変換[関数](../logic-apps/workflow-definition-language-functions-reference.md)を使ったときに、Logic Apps によってコンテンツがどのように変換されるかを箇条書きにしたものです。

* `json()`:データを `application/json` にキャストします
* `xml()`:データを `application/xml` にキャストします
* `binary()`:データを `application/octet-stream` にキャストします
* `string()`:データを `text/plain` にキャストします
* `base64()`:コンテンツを Base64 でエンコードされた文字列に変換します
* `base64toString()`:Base64 でエンコードされた文字列を `text/plain` に変換します
* `base64toBinary()`:Base64 でエンコードされた文字列を `application/octet-stream` に変換します
* `dataUri()`:文字列をデータ URI に変換します
* `dataUriToBinary()`:データ URI をバイナリ文字列に変換します
* `dataUriToString()`:データ URI を文字列に変換します

たとえば `Content-Type` が `application/xml` に設定された、次のような内容の HTTP 要求を受信したとします。

```html
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

このコンテンツは、`xml()` 関数と `triggerBody()` 関数から成る `@xml(triggerBody())` 式を使ってキャストしてから、後で使用することができます。 または、`xpath()` 関数と `xml()` 関数を組み合わせた `@xpath(xml(triggerBody()), '/CustomerName')` 式を使用することもできます。 

## <a name="other-content-types"></a>他のコンテンツ タイプ

その他のコンテンツ タイプについても Logic Apps で扱うことができ、サポートもされていますが、`$content` 変数をデコードすることによってメッセージ本文を手動で取得しなければならない場合があります。

たとえば、コンテンツ タイプが `application/x-www-url-formencoded` である要求によってロジック アプリがトリガーされるとします。 すべてのデータを維持するために、要求本文の `$content` 変数には、base64 文字列としてエンコードされたペイロードが含まれています。

`CustomerName=Frank&Address=123+Avenue`

この要求はプレーン テキストでも JSON でもないので、アクションには次のように格納されます。

```json
"body": {
   "$content-type": "application/x-www-url-formencoded",
   "$content": "AAB1241BACDFA=="
}
```

Logic Apps には、形式データを処理するためのネイティブ関数が用意されています。以下に示したのは、その例です。 

* [triggerFormDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue)
* [triggerFormDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues)
* [formDataValue()](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) 
* [formDataMultiValues()](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues)

または次のような式を使って、データに手動でアクセスすることもできます。

`@string(body('formdataAction'))` 

送信要求に同じ `application/x-www-url-formencoded` コンテンツ タイプ ヘッダーを適用したい場合は、`@body('formdataAction')` などの式を使って、その要求をアクション本体に追加すればよく、キャストは必要ありません。 ただし、この方法が有効なのは、本体が `body` 入力内で唯一のパラメーターであるときのみです。 `application/json` 要求内で `@body('formdataAction')` 式を使おうとすると、本体がエンコードされて送信されるためにランタイム エラーが発生します。
