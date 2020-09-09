---
title: Translator の Languages メソッド
titleSuffix: Azure Cognitive Services
description: Languages メソッドでは、Translator の他の操作で現在サポートされている一連の言語が取得されます。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: 1f4dfc4b80aff01e4b7fe7ebae4850b28cd6a498
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588600"
---
# <a name="translator-30-languages"></a>Translator 3.0:Languages

Translator の他の操作で現在サポートされている一連の言語を取得します。 

## <a name="request-url"></a>要求 URL

`GET` 要求の送信先は次のとおりです。
```HTTP
https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
```

## <a name="request-parameters"></a>要求パラメーター

クエリ文字列に渡される要求パラメーターを次に示します。

<table width="100%">
  <th width="20%">Query parameter (クエリ パラメーター)</th>
  <th>説明</th>
  <tr>
    <td>api-version</td>
    <td>"<em>必須のパラメーター</em>"。<br/>クライアントによって要求される API のバージョン。 値は `3.0` とする必要があります。</td>
  </tr>
  <tr>
    <td>scope</td>
    <td>"*省略可能なパラメーター*"。<br/>取得する言語のグループを定義する名前のコンマ区切りのリスト。 許可されるグループ名は、`translation`、`transliteration`、および `dictionary` です。 スコープを指定しない場合は、すべてのグループが返されます。これは、`scope=translation,transliteration,dictionary` を渡すのと等価です。 サポートされている言語のどのセットが自分のシナリオに適しているかを判断するには、[応答オブジェクト](#response-body)の説明を参照してください。</td>
  </tr>
</table> 

要求ヘッダーを次に示します。

<table width="100%">
  <th width="20%">ヘッダー</th>
  <th>説明</th>
  <tr>
    <td>Accept-Language</td>
    <td>"*省略可能な要求ヘッダー*"。<br/>ユーザー インターフェイス文字列に使用する言語。 応答内のフィールドには、言語の名前やリージョンの名前などがあります。 これらの名前が返される言語を定義するには、このパラメーターを使用します。 言語は、整形式の BCP 47 言語タグを使用して指定します。 たとえば、フランス語の名前を要求するには値 `fr` を使用し、繁体字中国語の名前を要求するには値 `zh-Hant` を使用します。<br/>ターゲット言語が指定されていない場合、またはローカライズされた言語を利用できない場合、名前は英語で提供されます。
    </td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>"*省略可能な要求ヘッダー*"。<br/>要求を一意に識別する、クライアントで生成された GUID。</td>
  </tr>
</table> 

言語リソースを取得するのに認証は必要ありません。

## <a name="response-body"></a>応答本文

クライアントは、`scope` クエリ パラメーターを使用して、目的の言語のグループを定義します。

* `scope=translation` は、ある言語から別の言語にテキストを翻訳するためにサポートされる言語を提供します。

* `scope=transliteration` は、ある言語のテキストを、ある書記体系から別の書記体系に変換する機能を提供します。

* `scope=dictionary` は、`Dictionary` 操作がデータを返す言語ペアを提供します。

クライアントは、コンマ区切りの名前のリストを指定することによって、複数のグループを同時に取得できます。 たとえば、`scope=translation,transliteration,dictionary` は、すべてのグループに対してサポートされている言語を返します。

正常な応答は、要求されたグループごとに 1 つのプロパティを持つ JSON オブジェクトです。

```json
{
    "translation": {
        //... set of languages supported to translate text (scope=translation)
    },
    "transliteration": {
        //... set of languages supported to convert between scripts (scope=transliteration)
    },
    "dictionary": {
        //... set of languages supported for alternative translations and examples (scope=dictionary)
    }
}
```

各プロパティの値は次のとおりです。

* `translation` プロパティ

  `translation` プロパティの値は、キーと値のペアの辞書です。 各キーは BCP 47 言語タグです。 キーは、テキストの翻訳先の言語または元の言語を識別します。 キーに関連付けられる値は、言語を表すプロパティを持つ JSON オブジェクトです。

  * `name`:`Accept-Language` ヘッダー経由で要求されたロケールの言語の表示名。

  * `nativeName`:この言語にネイティブなロケールの言語の表示名。

  * `dir`:方向性。右から左に記述する言語の場合は `rtl`、左から右に記述する言語の場合は `ltr`。

  例を示します。
          
  ```json
  {
    "translation": {
      ...
      "fr": {
        "name": "French",
        "nativeName": "Français",
        "dir": "ltr"
      },
      ...
    }
  }
  ```

* `transliteration` プロパティ

  `transliteration` プロパティの値は、キーと値のペアの辞書です。 各キーは BCP 47 言語タグです。 キーは、テキストをある書記体系から別の書記体系に変換できる言語を示します。 キーに関連付けられる値は、言語とそのサポートされる書記体系を表すプロパティを持つ JSON オブジェクトです。

  * `name`:`Accept-Language` ヘッダー経由で要求されたロケールの言語の表示名。

  * `nativeName`:この言語にネイティブなロケールの言語の表示名。

  * `scripts`:変換元の書記体系の一覧。 `scripts` の一覧の各要素には、次のプロパティがあります。

    * `code`:書記体系を識別するコード。

    * `name`:`Accept-Language` ヘッダー経由で要求されたロケールの書記体系の表示名。

    * `nativeName`:この言語にネイティブなロケールの言語の表示名。

    * `dir`:方向性。右から左に記述する言語の場合は `rtl`、左から右に記述する言語の場合は `ltr`。

    * `toScripts`:テキストの変換先として使用できる書記体系の一覧。 `toScripts` の一覧の各要素には、前述したとおり `code`、`name`、`nativeName`、`dir` の各プロパティがあります。

  例を示します。

  ```json
  {
    "transliteration": {
      ...
      "ja": {
        "name": "Japanese",
        "nativeName": "日本語",
        "scripts": [
          {
            "code": "Jpan",
            "name": "Japanese",
            "nativeName": "日本語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Latn",
                "name": "Latin",
                "nativeName": "ラテン語",
                "dir": "ltr"
              }
            ]
          },
          {
            "code": "Latn",
            "name": "Latin",
            "nativeName": "ラテン語",
            "dir": "ltr",
            "toScripts": [
              {
                "code": "Jpan",
                "name": "Japanese",
                "nativeName": "日本語",
                "dir": "ltr"
              }
            ]
          }
        ]
      },
      ...
    }
  }
  ```

* `dictionary` プロパティ

  `dictionary` プロパティの値は、キーと値のペアの辞書です。 各キーは BCP 47 言語タグです。 キーは、代替翻訳と逆翻訳が利用可能な言語を識別します。 値は、ソース言語と利用可能な翻訳があるターゲット言語を記述する JSON オブジェクトです。

  * `name`:`Accept-Language` ヘッダー経由で要求されたロケールのソース言語の表示名。

  * `nativeName`:この言語にネイティブなロケールの言語の表示名。

  * `dir`:方向性。右から左に記述する言語の場合は `rtl`、左から右に記述する言語の場合は `ltr`。

  * `translations`:代替翻訳とソース言語で表現されたクエリの例を含む言語の一覧。 `translations` の一覧の各要素には、次のプロパティがあります。

    * `name`:`Accept-Language` ヘッダー経由で要求されたロケールのターゲット言語の表示名。

    * `nativeName`:ターゲット言語にネイティブなロケールのターゲット言語の表示名。

    * `dir`:方向性。右から左に記述する言語の場合は `rtl`、左から右に記述する言語の場合は `ltr`。
    
    * `code`:ターゲット言語を識別する言語コード。

  例を示します。

  ```json
  "es": {
    "name": "Spanish",
    "nativeName": "Español",
    "dir": "ltr",
    "translations": [
      {
        "name": "English",
        "nativeName": "English",
        "dir": "ltr",
        "code": "en"
      }
    ]
  },
  ```

応答オブジェクトの構造は、API のバージョンを変更しなければ変更されません。 同じバージョンの API の場合、使用可能な言語の一覧が時間の経過と共に変更される可能性があります。これは、Microsoft Translator のサービスでサポートされる言語の一覧が絶えず拡張されているためです。

サポートされている言語の一覧は頻繁には変更されません。 ネットワーク帯域幅を節約し、応答性を向上させるために、クライアント アプリケーションでは、言語リソースと対応するエンティティ タグ (`ETag`) をキャッシュすることを検討する必要があります。 次に、クライアント アプリケーションは、定期的に (たとえば、24 時間に 1 回) サービスに対するクエリを実行して、サポートされている言語の最新のセットをフェッチすることができます。 現在の `ETag` 値を `If-None-Match` ヘッダー フィールドで渡すことで、サービスは応答を最適化できます。 リソースが変更されていない場合、サービスは状態コード 304 と空の応答本文を返します。

## <a name="response-headers"></a>応答ヘッダー

<table width="100%">
  <th width="20%">ヘッダー</th>
  <th>説明</th>
  <tr>
    <td>ETag</td>
    <td>要求されたサポートされる言語のグループのエンティティ タグの現在の値。 後続の要求をより効率的にするために、クライアントは、`If-None-Match` ヘッダー フィールドで `ETag` 値を送信することができます。
    </td>
  </tr>
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
    <td>304</td>
    <td>リソースは、要求ヘッダー `If-None-Match` で指定されたバージョン以降変更されていません。</td>
  </tr>
  <tr>
    <td>400</td>
    <td>クエリ パラメーターの 1 つが欠落しているか無効です。 再試行する前に要求パラメーターを修正してください。</td>
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

エラーが発生した場合は、要求の結果として JSON エラー応答も返されます。 このエラーコードは 3 桁の HTTP ステータス コードの後に､エラーをさらに分類するための 3 桁の数字を続けた 6 桁の数字です｡ 一般的なエラー コードは、[v3 Translator のリファレンス ページ](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#errors)で確認できます。 

## <a name="examples"></a>例

次の例では、テキスト翻訳でサポートされている言語を取得する方法を示します。

```curl
curl "https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation"
```
