---
title: 言語サポート - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS はサービス内にさまざまな機能を備えています。 すべての機能の言語パリティが同じであるわけではありません。 関心のある機能が、自分の対象とする言語カルチャでサポートされていることをご確認ください。 LUIS アプリはカルチャ固有で、一度設定したら変更できません。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 3b1b31da68d821ff27fa30e75ec1522b9d4875c8
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307429"
---
# <a name="language-and-region-support-for-luis"></a>LUIS の言語と地域のサポート

LUIS はサービス内にさまざまな機能を備えています。 すべての機能の言語パリティが同じであるわけではありません。 関心のある機能が、自分の対象とする言語カルチャでサポートされていることをご確認ください。 LUIS アプリはカルチャ固有で、一度設定したら変更できません。

## <a name="multi-language-luis-apps"></a>複数言語の LUIS アプリ

チャットボットなどの多言語 LUIS クライアント アプリケーションが必要な場合は、オプションがいくつかあります。 LUIS によってすべての言語がサポートされている場合は、言語ごとに LUIS アプリを開発します。 各 LUIS アプリに一意のアプリ ID と、エンドポイント ログがあります。 LUIS でサポートされていない言語に対して、言語の解釈を提供する必要がある場合は、[Microsoft Translator API](../Translator/translator-info-overview.md) を使用して、サポートされている言語に発話を翻訳し、LUIS エンドポイントに送信して、結果スコアを受け取ることができます。

## <a name="languages-supported"></a>サポートされている言語

LUIS が理解する発話の言語を次に示します。

| 言語 |Locale  |  事前構築済みのドメイン | 事前構築済みのエンティティ | フレーズ リストのレコメンデーション | \**[テキスト分析](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(センチメントと<br>キーワード)|
|--|--|:--:|:--:|:--:|:--:|
| 英語 (米国) |`en-US` | ✔ | ✔  |✔|✔|
| *[中国語](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| オランダ語 |`nl-NL` |-|  -   |-|✔|
| フランス語 (フランス) |`fr-FR` |-| ✔ |✔ |✔|
| フランス語 (カナダ) |`fr-CA` |-|   -   |-|✔|
| ドイツ語 |`de-DE` |-| ✔ |✔ |✔|
| ヒンディー語 | `hi-IN`|-|-|-|-|
| イタリア語 |`it-IT` |-| ✔ |✔|✔|
| *[日本語](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|キー フレーズのみ|
| 韓国語 |`ko-KR` |-|   -   |-|キー フレーズのみ|
| ポルトガル語 (ブラジル) |`pt-BR` |-| ✔ |✔ |一部のサブカルチャのみ|
| スペイン語 (スペイン) |`es-ES` |-| ✔ |✔|✔|
| スペイン語 (メキシコ)|`es-MX` |-|  -   |✔|✔|
| トルコ語 | `tr-TR` |-|-|-|センチメントのみ|


言語サポートは、[事前構築済みのエンティティ](luis-reference-prebuilt-entities.md)および[事前構築済みのドメイン](luis-reference-prebuilt-domains.md)によって異なります。

### <a name="chinese-support-notes"></a>*中国語サポートに関するメモ

 - `zh-cn` カルチャで LUIS に必要なのは、繁体字中国語ではなく簡体字中国語の文字セットです。
 - 意図、エンティティ、機能、および正規表現の名前には、中国語の文字またはローマ字を使用できます。
 - `zh-cn` カルチャでサポートされているあらかじめ構築されたドメインについては、[あらかじめ構築されたドメインのリファレンス](luis-reference-prebuilt-domains.md)を参照してください。
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*日本語サポートに関するメモ

 - LUIS では構文解析が行われず、日本語の敬語とくだけた言葉遣いの違いは解釈されないため、ご自身のアプリケーションについては、トレーニング例としてさまざまなレベルの文体を組み込む必要があります。
     - "でございます" は、"です" と同じではありません。
     - "です" は、"だ" と同じではありません。

### <a name="text-analytics-support-notes"></a>\*\*テキスト分析サポートに関するメモ
テキスト分析には、keyPhrase 事前構築済みエンティティと感情分析が含まれます。 サブカルチャ `pt-PT` および `pt-BR` については、ポルトガル語のみがサポートされています。 その他のカルチャはすべて、プライマリ カルチャ レベルでサポートされています。 Text Analytics の[サポートされている言語](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)の詳細を確認してください。

### <a name="speech-api-supported-languages"></a>Speech API のサポートされている言語
Speech ディクテーション モードの言語については、Speech の「[Supported language (サポートされている言語)](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode)」を参照してください。

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check のサポートされている言語
サポートされている言語と状態の一覧については、Bing Spell Check の[サポートされている言語](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages)に関するページをご覧ください。

## <a name="rare-or-foreign-words-in-an-application"></a>アプリケーションの珍しい単語または外国の単語
`en-us` カルチャの LUIS では、学習によって英語の単語のほとんど (スラングを含む) が区別されます。 `zh-cn` カルチャの LUIS では、学習によってほとんどの中国語の文字が区別されます。 `en-us` で珍しい単語を使用するか、`zh-cn` で珍しい文字を使用する場合、LUIS で単語または文字を区別できない可能性があるときは、その単語または文字を[フレーズ リスト機能](luis-how-to-add-features.md)に追加できます。 たとえば、アプリケーションのカルチャ以外の単語、つまり外国の単語は、フレーズ リスト機能に追加する必要があります。 このフレーズ リストは、交換不可能としてマークする必要があります。これにより、一連の珍しい単語によって、LUIS が学習によって認識する必要があるクラスが形成されていること、ただし、それらはシノニムではなく、相互に交換できないことが示されます。

### <a name="hybrid-languages"></a>ハイブリッド言語
ハイブリッド言語では、英語、中国語など、2 つのカルチャの単語が結合されます。 アプリが基づくカルチャは 1 つであるため、これらの言語は LUIS ではサポートされていません。

## <a name="tokenization"></a>トークン化
機械学習を実行するために、LUIS では、発話がカルチャに基づいて[トークン](luis-glossary.md#token)に分割されます。

|言語|  すべてのスペースまたは特殊文字 | 文字レベル|複合語|[返されるトークン化されたエンティティ](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|中国語||✔||✔|
|オランダ語|||✔|✔|
|英語 (en-us)|✔ ||||
|フランス語 (fr-FR)|✔||||
|フランス語 (fr-CA)|✔||||
|ドイツ語|||✔|✔|
| ヒンディー語 |✔|-|-|-|-|
|イタリア語|✔||||
|日本語||||✔|
|韓国語||✔||✔|
|ポルトガル語 (ブラジル)|✔||||
|スペイン語 (es-ES)|✔||||
|スペイン語 (es-MX)|✔||||

### <a name="custom-tokenizer-versions"></a>カスタム トークナイザーのバージョン

次のカルチャには、カスタム トークナイザーのバージョンがあります。

|カルチャ|Version|目的|
|--|--|--|
|ドイツ語<br>`de-de`|1.0.0|複合語をその単一コンポーネントに分解しようとする機械学習ベースのトークナイザーを使用して分割することによって、単語をトークン化します。<br>ユーザーが発話として `Ich fahre einen krankenwagen` を入力した場合、それは `Ich fahre einen kranken wagen` に変換されます。 `kranken` と `wagen` を別のエンティティとして独立にマークすることが可能になります。|
|ドイツ語<br>`de-de`|1.0.2|スペースで分割することによって、単語をトークン化します。<br> ユーザーが発話として `Ich fahre einen krankenwagen` を入力した場合、それは 1 つのトークンのままになります。 そのため、`krankenwagen` は 1 つのエンティティとしてマークされます。 |

### <a name="migrating-between-tokenizer-versions"></a>トークナイザーのバージョン間の移行
<!--
Your first choice is to change the tokenizer version in the app file, then import the version. This action changes how the utterances are tokenized but allows you to keep the same app ID. 

Tokenizer JSON for 1.0.0. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.0",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.0",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 23,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```

Tokenizer JSON for version 1.0.1. Notice the property value for  `tokenizerVersion`. 

```JSON
{
    "luis_schema_version": "3.2.0",
    "versionId": "0.1",
    "name": "german_app_1.0.1",
    "desc": "",
    "culture": "de-de",
    "tokenizerVersion": "1.0.1",
    "intents": [
        {
            "name": "i1"
        },
        {
            "name": "None"
        }
    ],
    "entities": [
        {
            "name": "Fahrzeug",
            "roles": []
        }
    ],
    "composites": [],
    "closedLists": [],
    "patternAnyEntities": [],
    "regex_entities": [],
    "prebuiltEntities": [],
    "model_features": [],
    "regex_features": [],
    "patterns": [],
    "utterances": [
        {
            "text": "ich fahre einen krankenwagen",
            "intent": "i1",
            "entities": [
                {
                    "entity": "Fahrzeug",
                    "startPos": 16,
                    "endPos": 27
                }
            ]
        }
    ],
    "settings": []
}
```
-->

トークン化は、アプリ レベルで発生します。 バージョン レベルのトークン化に対するサポートはありません。 

バージョンの代わりに、[新しいアプリとしてファイルをインポート](luis-how-to-start-new-app.md#import-an-app-from-file)します。 このアクションは、新しいアプリはアプリ ID が異なるが、ファイルで指定されたトークナイザーのバージョンを使用することを示します。 