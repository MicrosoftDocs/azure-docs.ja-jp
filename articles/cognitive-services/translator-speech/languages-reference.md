---
title: Translator Speech API 言語メソッド
titleSuffix: Azure Cognitive Services
description: Translator Speech API 言語メソッドを使用します。
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-speech
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: v-jansko
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7498ba08b9ce7b6aae10f38a393eb8cba37f3f4e
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435764"
---
# <a name="translator-speech-api-languages"></a>Translator Speech API: Languages

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

Translator Speech は、そのサービスでサポートされる言語の一覧を継続的に拡張しています。 Translator Speech サービスで現在使用可能な言語のセットを取得するには、この API を使います。

使用可能な言語を取得するための API の使用例を示すコード サンプルは、[Microsoft Translator GitHub サイト](https://github.com/MicrosoftTranslator)から入手できます。

## <a name="implementation-notes"></a>実装に関するメモ

### <a name="get-languages"></a>GET /languages

音声の文字起こし、文字起こししたテキストの翻訳、翻訳の合成音声の生成には、広範な言語セットを使用できます。

クライアントは、`scope` クエリ パラメーターを使用して、目的の言語のセットを定義します。

* **音声テキスト変換:** 音声からテキストを文字起こしするために使用できる言語のセットを取得するには、クエリ パラメーター `scope=speech` を使用します。
* **テキスト翻訳:** 文字起こししたテキストを翻訳するために使用できる言語のセットを取得するには、クエリ パラメーター `scope=text` を使用します。
* **テキスト読み上げ:** 翻訳されたテキストから音声を合成するために使用できる言語と音声のセットを取得するには、クエリ パラメーター `scope=tts` を使用します。

クライアントは、コンマ区切りの選択肢リストを指定することによって、複数のセットを同時に取得できます。 たとえば、「 `scope=speech,text,tts` 」のように入力します。

正常な応答は、要求されたセットごとに 1 つのプロパティを持つ JSON オブジェクトです。

```
{
    "speech": {
        //... Set of languages supported for speech-to-text
    },
    "text": {
        //... Set of languages supported for text translation
    },
    "tts": {
        //... Set of languages supported for text-to-speech
    }
}
```

クライアントは `scope` クエリ パラメーターを使用して、取得する必要のあるサポート対象言語のセットを選択できるので、実際の応答には前に示したすべてのプロパティのサブセットのみが含まれることがあります。

各プロパティで提供される値は次のとおりです。

### <a name="speech-to-text-speech"></a>音声テキスト変換 (speech)

音声テキスト変換プロパティ `speech` に関連付けられている値は、(キー、値) ペアのディクショナリです。 各キーは、音声テキスト変換でサポートされている言語を示します。 キーは、クライアントが API に渡す識別子です。 キーに関連付けられている値は、次のプロパティを持つオブジェクトです。

* `name`:言語の表示名。
* `language`:関連付けられている記述言語の言語タグ。 後の "テキスト翻訳" をご覧ください。
例を示します。

```
{
    "speech": {
        "en-US": { name: "English", language: "en" }
    }
}
```

### <a name="text-translation-text"></a>テキスト翻訳 (text)

`text` プロパティに関連付けられている値も、各キーがテキスト翻訳でサポートされている言語を示すディクショナリです。 キーに関連付けられた値では、言語について記述されています。

* `name`:言語の表示名。
* `dir`:方向性。右から左に記述する言語の場合は `rtl`、左から右に記述する言語の場合は `ltr`。

例を示します。

```
{
    "text": {
        "en": { name: "English", dir: "ltr" }
    }
}
```

### <a name="text-to-speech-tts"></a>テキスト読み上げ (tts)

テキスト読み上げプロパティ tts に関連付けられている値も、各キーがサポートされている音声を示すディクショナリです。 音声オブジェクトの属性は次のとおりです。

* `displayName`:音声の表示名。
* `gender`:音声の性別 (男性または女性)。
* `locale`:第一言語サブタグと地域サブタグを含む音声の言語タグ。
* `language`:関連付けられている記述言語の言語タグ。
* `languageName`:言語の表示名。
* `regionName`:この言語の地域の表示名。

例を示します。

```
{
    "tts": {
        "en-US-Zira": {
            "displayName": "Zira",
            "gender": "female",
            "locale": "en-US",
            "languageName": "English",
            "regionName": "United States",
            "language": "en"
        }
}
```

### <a name="localization"></a>ローカライズ
サービスは、テキスト翻訳でサポートされているすべての言語について、"Accept-Language" ヘッダーの言語ですべての名前を返します。

### <a name="response-class-status-200"></a>応答クラス (ステータス 200)
サポート対象言語のセットが記述されているオブジェクト。

ModelExample の値:

Langagues { speech (object, optional), text (object, optional), tts (object, optional) }

### <a name="headers"></a>headers

|ヘッダー|説明|type|
:--|:--|:--|
X-RequestId|要求を識別するためにサーバーによって生成され、トラブルシューティングのために使用される値。|文字列|

### <a name="parameters"></a>parameters

|パラメーター|説明|パラメーターのタイプ|データ型|
|:--|:--|:--|:--|
|api-version    |クライアントによって要求される API のバージョン。 使用できる値: `1.0`。|クエリ|文字列|
|scope  |クライアントに返す、サポートされている言語または音声のセット。 このパラメーターは、キーワードのコンマ区切り一覧として指定されます。 次のキーワードを使用できます。<ul><li>`speech`:音声の文字起こしにサポートされている言語のセットを提供します。</li><li>`tts`:テキスト音声変換にサポートされている音声のセットを提供します。</li><li>`text`:テキストの翻訳にサポートされている言語のセットを提供します。</li></ul>値が指定されていない場合、`scope` の既定値は `text` です。|query|文字列|
|X-ClientTraceId    |クライアントによって生成される GUID であり、要求を追跡するのに使用されます。 問題のトラブルシューティングを容易にするため、クライアントは要求のたびに新しい値を指定し、それを記録する必要があります。|header|文字列|
|Accept-Language    |応答内のフィールドには、言語や地域の名前などがあります。 名前が返される言語を定義するには、このパラメーターを使用します。 言語は、整形式の BCP 47 言語タグを使用して指定します。 `text` スコープで返される言語識別子の一覧からタグを選択します。 サポートされていない言語の場合、名前は英語で提供されます。<br/>たとえば、フランス語の名前を要求するには値 `fr` を使用し、繁体字中国語の名前を要求するには値 `zh-Hant` を使用します。|header|文字列|

### <a name="response-messages"></a>応答メッセージ

|HTTP 状態コード|理由|
|:--|:--|
|400|要求が正しくありません。 入力パラメーターを調べて正しいことを確認します。 応答オブジェクトには、エラーに関する詳細な説明が含まれています。|
|429|要求が多すぎます。|
|500|エラーが発生しました。 エラーが解決しない場合は、それをクライアント トレース識別子 (X-ClientTraceId) または要求識別子 (X-RequestId) と一緒にご報告ください。|
|503|サーバーが一時的に使用できません。 要求を再試行してください。 エラーが解決しない場合は、それをクライアント トレース識別子 (X-ClientTraceId) または要求識別子 (X-RequestId) と一緒にご報告ください。|
