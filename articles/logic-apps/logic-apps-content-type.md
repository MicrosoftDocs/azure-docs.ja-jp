---
title: "各種コンテンツの扱い - Azure Logic Apps | Microsoft Docs"
description: "Azure Logic Apps の設計時と実行時における各種コンテンツの扱いについて説明します。"
services: logic-apps
documentationcenter: .net,nodejs,java
author: jeffhollan
manager: anneta
editor: 
ms.assetid: cd1f08fd-8cde-4afc-86ff-2e5738cc8288
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: d0d283f21caf53406c51670e75bf2796a175be51
ms.lasthandoff: 03/25/2017


---
# <a name="handle-content-types-in-logic-apps"></a>ロジック アプリにおける各種コンテンツの扱い

ロジック アプリには、JSON、XML、フラット ファイル、バイナリ データなど、さまざまな種類のコンテンツを渡すことができます。 Logic Apps エンジンはあらゆる種類のコンテンツに対応していますが、Logic Apps エンジンでネイティブに認識されるコンテンツもあれば、 適宜キャストや変換が必要なコンテンツもあります。 この記事では、Logic Apps エンジンで各種コンテンツがどのように扱われるか、また、必要に応じて各種コンテンツを正しく処理するにはどうすればよいかについて説明します。

## <a name="content-type-header"></a>Content-Type ヘッダー

まずは簡単なものから見ていきましょう。ロジック アプリでの使用時に変換やキャストを必要としない 2 つの `Content-Types`、つまり `application/json` と `text/plain` を確認します。

## <a name="applicationjson"></a>Application/JSON

ワークフロー エンジンは、HTTP 呼び出しの `Content-Type` ヘッダーに基づいて適切な処理を決定します。 コンテンツ タイプが `application/json` の要求は、すべて JSON オブジェクトとして格納、処理されます。 また、JSON コンテンツはキャストしなくても既定で解析できます。 

たとえば、Content-Type ヘッダーとして `application/json ` が指定されている要求は、`@body('myAction')['foo'][0]` のような式を使ってワークフローで解析することができます。この場合、`bar` という値が取得されます。

```
{
    "data": "a",
    "foo": [
        "bar"
    ]
}
```

追加のキャストは不要です。 取り扱っているデータが JSON 形式ではあるもののヘッダーが指定されていない場合は、`@json()` 関数を使って手動で JSON 形式にキャストすることができます (例: `@json(triggerBody())['foo']`)。

### <a name="schema-and-schema-generator"></a>スキーマとスキーマ ジェネレーター

要求トリガーでは、受信するペイロードの JSON スキーマを入力することができます。 利用者が要求の内容を取り出すことができるよう、このスキーマに基づくトークンがデザイナーによって生成されます。 スキーマが準備できていない場合は、**[サンプルのペイロードを使用してスキーマを生成する]** を選択すると、サンプル ペイロードから JSON スキーマを生成できます。

![スキーマ](./media/logic-apps-http-endpoint/manualtrigger.png)

### <a name="parse-json-action"></a>"Parse JSON" アクション

`Parse JSON` アクションを使用すると、JSON コンテンツを解析して、ロジック アプリが利用しやすいトークンにすることができます。 要求トリガーと同様に、解析するコンテンツの JSON スキーマを入力または生成することができます。 Service Bus や DocumentDB などからデータを取り出す手段としては、こちらの方がはるかに簡単です。

![Parse JSON](./media/logic-apps-content-type/ParseJSON.png)

## <a name="textplain"></a>text/plain

`application/json` と同じように、`text/plain` の `Content-Type` ヘッダー付きで受信した HTTP メッセージは、未加工の形式で格納されます。 また、それらのメッセージをキャストせずに後続のアクションに渡した場合、それらの要求には `Content-Type`: `text/plain` ヘッダーが適用されます。 たとえばフラット ファイルを扱っているときに、次の HTTP コンテンツを `text/plain` として受け取ったとします。

```
Date,Name,Address
Oct-1,Frank,123 Ave.
```

この要求を後続のアクションで別の要求の本文 (`@body('flatfile')`) として送信した場合、その要求には `text/plain` Content-Type ヘッダーが適用されます。 プレーン テキストではあるものの指定のヘッダーがないデータを扱っている場合は、そのデータを `@string()` 関数 (例: `@string(triggerBody())`) を使って手動でテキストにキャストできます。

## <a name="applicationxml-and-applicationoctet-stream-and-converter-functions"></a>Application/xml、Application/octet-stream、変換関数

Logic Apps エンジンは、HTTP 要求または HTTP 応答で受信した `Content-Type` を常に維持します。 そのため、`Content-Type` が `application/octet-stream` であるコンテンツがエンジンによって受信されたとき、そのコンテンツをキャストせずに後続のアクションに渡した場合、そこから送信される要求には `Content-Type`: `application/octet-stream` が適用されます。 こうすることで、ワークフロー内を移動する過程でデータが失われないことをエンジンは保証することができます。 ただし、アクションの状態 (入力と出力) は、ワークフローを移動する際に JSON オブジェクトに格納されます。 つまり、エンジンは一部のデータ型を維持するために、`$content` と `$content-type` の両方を保持する適切なメタデータ (自動的に変換される) を含む、Base64 でエンコードされたバイナリ文字列にコンテンツを変換します。 

* `@json()` - データを `application/json` にキャスト
* `@xml()` - データを `application/xml` にキャスト
* `@binary()` - データを `application/octet-stream` にキャスト
* `@string()` - データを `text/plain` にキャスト
* `@base64()` - コンテンツを Base64 文字列に変換
* `@base64toString()` - Base64 でエンコードされた文字列を `text/plain` に変換
* `@base64toBinary()` - Base64 でエンコードされた文字列を `application/octet-stream` に変換
* `@encodeDataUri()` - 文字列を Data URI のバイト配列としてエンコード
* `@decodeDataUri()` - Data URI をバイト配列にデコード

たとえば、`Content-Type`: `application/xml` の HTTP 要求を受信したとします。

```
<?xml version="1.0" encoding="UTF-8" ?>
<CustomerName>Frank</CustomerName>
```

これは、`@xml(triggerBody())` などの方法、つまり `@xpath(xml(triggerBody()), '/CustomerName')` などの関数でキャストして使用することができます。

## <a name="other-content-types"></a>他のコンテンツ タイプ

他のコンテンツ タイプもサポートされており、ロジック アプリで使用できますが、`$content` をデコードして手動でメッセージ本文を取得しなければならない場合があります。 たとえば `application/x-www-url-formencoded` 要求をトリガーするとします。このときの `$content` は、すべてのデータを保持するために Base64 文字列としてエンコードされたペイロードです。

```
CustomerName=Frank&Address=123+Avenue
```

この要求はプレーン テキストでも JSON でもないので、アクションには次のように格納されます。

```
...
"body": {
    "$content-type": "application/x-www-url-formencoded",
    "$content": "AAB1241BACDFA=="
}
```

現時点ではフォーム データ用のネイティブ関数は存在しないので、`@string(body('formdataAction'))` のような関数を使用して手動でデータにアクセスすれば、このデータをワークフロー内で使用できます。 送信要求に `application/x-www-url-formencoded` という Content-Type ヘッダーも加えたい場合は、その要求をアクション本体に追加すれば済みます。`@body('formdataAction')` のようなキャストは必要ありません。 ただし、この方法が有効なのは、本体が `body` 入力内で唯一のパラメーターであるときのみです。 `application/json` 要求内で `@body('formdataAction')` を使おうとすると、エンコードされた本体が送信されるためにランタイム エラーが発生します。


