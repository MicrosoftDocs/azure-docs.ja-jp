---
title: Translator Text API の BreakSentence メソッド
titlesuffix: Azure Cognitive Services
description: Translator Text API の BreakSentence メソッドを使用します。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 59f6957f1781852e4ab4c0713742581b5c884f25
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2019
ms.locfileid: "67357874"
---
# <a name="translator-text-api-30-breaksentence"></a>Translator Text API 3.0: BreakSentence

文章内で文の境界の位置を識別します。

## <a name="request-url"></a>要求 URL

`POST` 要求の送信先は次のとおりです。

```HTTP
https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

<table width="100%">
  <th width="20%">Query parameter (クエリ パラメーター)</th>
  <th>説明</th>
  <tr>
    <td>api-version</td>
    <td>"*必須のクエリ パラメーター*" です。<br/>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。</td>
  </tr>
  <tr>
    <td>language</td>
    <td>"*省略可能なクエリ パラメーター*" です。<br/>入力テキストの言語を示す言語タグ。 コードを指定しないと、言語の自動検出が適用されます。</td>
  </tr>
  <tr>
    <td>script</td>
    <td>"*省略可能なクエリ パラメーター*" です。<br/>入力テキストで使われているスクリプトを示すスクリプト タグ。 スクリプトを指定しないと、言語の既定のスクリプトと見なされます。</td>
  </tr>
</table> 

要求ヘッダーには次のものがあります。

<table width="100%">
  <th width="20%">headers</th>
  <th>説明</th>
  <tr>
    <td>認証ヘッダー</td>
    <td>"<em>必須の要求ヘッダー</em>" です。<br/><a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">認証に使用できるオプション</a>に関するページをご覧ください。</td>
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
    <td>*省略可能*。<br/>要求を一意に識別する、クライアントで生成された GUID。 クエリ パラメーター `ClientTraceId` を使ってクエリ文字列内にトレース ID を含める場合、このヘッダーを省略できることに注意してください。</td>
  </tr>
</table> 

## <a name="request-body"></a>要求本文

要求の本文は JSON 配列です。 各配列要素は、`Text` という名前の文字列プロパティを持つ JSON オブジェクトです。 文の境界は、`Text` プロパティの値に対して計算されます。 1 つのテキストを含む要求本文の例を次に示します。

```json
[
    { "Text": "How are you? I am fine. What did you do today?" }
]
```

次の制限事項が適用されます。

* 配列に含めることができる要素は、最大でも 100 個です。
* 配列要素のテキスト値は、スペースも含めて 10,000 文字を超えてはなりません。
* 要求に含めるテキスト全体では、スペースも含めて 50,000 文字を超えてはなりません。
* `language` クエリ パラメーターを指定する場合は、すべての配列要素が同じ言語である必要があります。 指定しないと、言語の自動検出が配列の各要素に個別に適用されます。

## <a name="response-body"></a>応答本文

正常な応答は、入力配列内の文字列ごとに 1 つの結果が含まれる JSON 配列となります。 結果オブジェクトには次のプロパティが含まれています。

  * `sentLen`:テキスト要素内の文の長さを表す整数の配列です。 配列の長さは文の数であり、値は各文の長さです。 

  * `detectedLanguage`:次のプロパティによって、検出された言語を説明するオブジェクトです。

     * `language`:検出された言語のコードです。

     * `score`:結果内の信頼度を示す浮動小数点値です。 スコアは 0 から 1 の範囲であり、低いスコアは低い信頼度を示します。
     
    `detectedLanguage` プロパティは、言語の自動検出が要求された場合に限り、結果オブジェクト内に存在することに注意してください。

JSON 応答の例を次に示します。

```json
[
  {
    "sentenceLengths": [ 13, 11, 22 ]
    "detectedLanguage": {
      "language": "en",
      "score": 401
    },
  }
]
```

## <a name="response-headers"></a>応答ヘッダー

<table width="100%">
  <th width="20%">headers</th>
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
    <td>成功。</td>
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

エラーが発生した場合は、要求の結果として JSON エラー応答も返されます。 このエラーコードは 3 桁の HTTP ステータス コードの後に､エラーをさらに分類するための 3 桁の数字を続けた 6 桁の数字です｡ 一般的なエラー コードは、[v3 Translator Text API のリファレンス ページ](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)で確認できます。 

## <a name="examples"></a>例

次の例では、1 つの文に対する文の境界を取得する方法を示します。 文の言語は、サービスによって自動的に検出されます。

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/breaksentence?api-version=3.0" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'How are you? I am fine. What did you do today?'}]"
```

---

