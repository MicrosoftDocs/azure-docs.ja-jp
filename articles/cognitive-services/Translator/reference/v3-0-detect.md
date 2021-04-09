---
title: Translator Detect メソッド
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services Translator Detect メソッドを利用し、一節のテキストの言語を特定します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: cb6660585b5f2b9ab56eaf863f1ec431e5e85109
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98895528"
---
# <a name="translator-30-detect"></a>Translator 3.0:Detect

テキストの一部の言語を識別します。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/detect?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

<table width="100%">
  <th width="20%">Query parameter (クエリ パラメーター)</th>
  <th>説明</th>
  <tr>
    <td>api-version</td>
    <td>"*必須のパラメーター*"。<br/>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。</td>
  </tr>
</table> 

要求ヘッダーには次のものがあります。

<table width="100%">
  <th width="20%">ヘッダー</th>
  <th>説明</th>
  <tr>
    <td>認証ヘッダー</td>
    <td>"<em>必須の要求ヘッダー</em>" です。<br/><a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">認証に使用できるオプション</a>に関するページをご覧ください。</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>"*必須の要求ヘッダー*" です。<br/>ペイロードのコンテンツ タイプを指定します。 次のいずれかの値になります。`application/json`</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>"*必須の要求ヘッダー*" です。<br/>要求本文の長さです。</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*オプション*。<br/>要求を一意に識別する、クライアントで生成された GUID。 クエリ パラメーター `ClientTraceId` を使ってクエリ文字列内にトレース ID を含める場合、このヘッダーを省略できることに注意してください。</td>
  </tr>
</table> 

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティを持つ JSON オブジェクトです。 言語の検出は、`Text` プロパティの値に適用されます。 言語自動検出は、入力テキストが長いほど、うまく機能します。 サンプルの要求本文は次のようになります。

```json
[
    { "Text": "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal." }
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 100 個です。
* 要求に含めるテキスト全体では、スペースも含めて 50,000 文字を超えてはなりません。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の文字列ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `language`:検出された言語のコードです。

  * `score`:結果内の信頼度を示す浮動小数点値です。 スコアは 0 から 1 の範囲であり、低いスコアは低い信頼度を示します。

  * `isTranslationSupported`:検出された言語がテキスト翻訳でサポートされている言語の 1 つである場合に true になるブール値です。

  * `isTransliterationSupported`:検出された言語が音訳でサポートされている言語の 1 つである場合に true になるブール値です。
  
  * `alternatives`:利用可能な他の言語の配列です。 配列の各要素は、上記にリストしたのと同じプロパティ (`language`、`score`、`isTranslationSupported`、`isTransliterationSupported`) を持つ別のオブジェクトです。

JSON 応答の例を次に示します。

```json
[
  {
    "language": "de",
    "score": 0.92,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "sk",
        "score": 0.23,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="response-headers"></a>応答ヘッダー

<table width="100%">
  <th width="20%">ヘッダー</th>
  <th>説明</th>
  <tr>
    <td>X-RequestId</td>
    <td>要求を識別するためにサービスによって生成される値。 トラブルシューティングの目的で使用されます。</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>応答状態コード

要求によって返される可能性のある HTTP 状態コードを次に示します。 

<table width="100%">
  <th width="20%">状態コード</th>
  <th>説明</th>
  <tr>
    <td>200</td>
    <td>正常終了しました。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>クエリ パラメーターの 1 つが欠落しているか無効です。 再試行する前に要求パラメーターを修正してください。</td>
  </tr>
  <tr>
    <td>401</td>
    <td>要求を認証できませんでした。 資格情報が指定され、有効であることを確認してください。</td>
  </tr>
  <tr>
    <td>403</td>
    <td>要求が承認されていません。 詳細なエラー メッセージを確認してください。 これは、多くの場合、試用版サブスクリプションで提供されるすべての無料翻訳が使い果たされたことを示します。</td>
  </tr>
  <tr>
    <td>429</td>
    <td>クライアントが要求の制限を超えたため、サーバーは要求を拒否しました。</td>
  </tr>
  <tr>
    <td>500</td>
    <td>予期しないエラーが発生しました。 エラーが解決しない場合は、エラー発生の日時、応答ヘッダー `X-RequestId` からの要求識別子、要求ヘッダー `X-ClientTraceId` からのクライアント識別子を添えてその旨をご報告ください。</td>
  </tr>
  <tr>
    <td>503</td>
    <td>サーバーが一時的に使用できません。 要求をやり直してください。 エラーが解決しない場合は、エラー発生の日時、応答ヘッダー `X-RequestId` からの要求識別子、要求ヘッダー `X-ClientTraceId` からのクライアント識別子を添えてその旨をご報告ください。</td>
  </tr>
</table> 

エラーが発生した場合は、要求の結果として JSON エラー応答も返されます。 このエラーコードは 3 桁の HTTP ステータス コードの後に､エラーをさらに分類するための 3 桁の数字を続けた 6 桁の数字です｡ 一般的なエラー コードは、[v3 Translator のリファレンス ページ](./v3-0-reference.md#errors)で確認できます。 

## <a name="examples"></a>例

次の例では、テキスト翻訳でサポートされている言語を取得する方法を示します。

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/detect?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'What language is this text written in?'}]"
```