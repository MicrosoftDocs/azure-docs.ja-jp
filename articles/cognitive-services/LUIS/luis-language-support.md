---
title: 言語サポート - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS はサービス内にさまざまな機能を備えています。 すべての機能の言語パリティが同じであるわけではありません。 関心のある機能が、自分の対象とする言語カルチャでサポートされていることをご確認ください。 LUIS アプリはカルチャ固有で、一度設定したら変更できません。
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/09/2019
ms.openlocfilehash: 724b94a44ae289063bbb1c6ebf53b38c0a617b88
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799329"
---
# <a name="language-and-region-support-for-luis"></a>LUIS の言語と地域のサポート

LUIS はサービス内にさまざまな機能を備えています。 すべての機能の言語パリティが同じであるわけではありません。 関心のある機能が、自分の対象とする言語カルチャでサポートされていることをご確認ください。 LUIS アプリはカルチャ固有で、一度設定したら変更できません。

## <a name="multi-language-luis-apps"></a>複数言語の LUIS アプリ

チャットボットなどの多言語 LUIS クライアント アプリケーションが必要な場合は、オプションがいくつかあります。 LUIS によってすべての言語がサポートされている場合は、言語ごとに LUIS アプリを開発します。 各 LUIS アプリに一意のアプリ ID と、エンドポイント ログがあります。 LUIS でサポートされていない言語に対して、言語の解釈を提供する必要がある場合は、[Translator サービス](../Translator/translator-info-overview.md)を使用して、サポートされている言語に発話を翻訳し、LUIS エンドポイントに送信して、結果スコアを受け取ることができます。

## <a name="languages-supported"></a>サポートされている言語

LUIS が理解する発話の言語を次に示します。

| Language |Locale  |  事前構築済みのドメイン | 事前構築済みのエンティティ | フレーズ リストのレコメンデーション | \**[テキスト分析](../text-analytics/language-support.md)<br>(センチメントと<br>キーワード)|
|--|--|:--:|:--:|:--:|:--:|
| アラビア語 (プレビュー - 現代標準アラビア語) |`ar-AR`|-|-|-|-|
| *[中国語](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| オランダ語 |`nl-NL` |✔|-|-|✔|
| 英語 (米国) |`en-US` | ✔ | ✔  |✔|✔|
| フランス語 (カナダ) |`fr-CA` |-|-|-|✔|
| フランス語 (フランス) |`fr-FR` |✔| ✔ |✔ |✔|
| ドイツ語 |`de-DE` |✔| ✔ |✔ |✔|
| グジャラート語 | `gu-IN`|-|-|-|-|
| ヒンディー語 | `hi-IN`|-|✔|-|-|
| イタリア語 |`it-IT` |✔| ✔ |✔|✔|
| *[日本語](#japanese-support-notes) |`ja-JP` |✔| ✔ |✔|キー フレーズのみ|
| 韓国語 |`ko-KR` |✔|-|-|キー フレーズのみ|
| マラーティー語 | `mr-IN`|-|-|-|-|
| ポルトガル語 (ブラジル) |`pt-BR` |✔| ✔ |✔ |一部のサブカルチャのみ|
| スペイン語 (メキシコ)|`es-MX` |-|✔|✔|✔|
| スペイン語 (スペイン) |`es-ES` |✔| ✔ |✔|✔|
| タミル語 | `ta-IN`|-|-|-|-|
| テルグ語 | `te-IN`|-|-|-|-|
| トルコ語 | `tr-TR` |✔|✔|-|センチメントのみ|




言語サポートは、[事前構築済みのエンティティ](luis-reference-prebuilt-entities.md)および[事前構築済みのドメイン](luis-reference-prebuilt-domains.md)によって異なります。

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

### <a name="japanese-support-notes"></a>*日本語サポートに関するメモ

 - LUIS では構文解析が行われず、日本語の敬語とくだけた言葉遣いの違いは解釈されないため、ご自身のアプリケーションについては、トレーニング例としてさまざまなレベルの文体を組み込む必要があります。
     - "でございます" は、"です" と同じではありません。
     - "です" は、"だ" と同じではありません。

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]

### <a name="speech-api-supported-languages"></a>Speech API のサポートされている言語
Speech ディクテーション モードの言語については、Speech の「[Supported language (サポートされている言語)](../speech-service/speech-to-text.md)」を参照してください。

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check のサポートされている言語
サポートされている言語と状態の一覧については、Bing Spell Check の[サポートされている言語](../bing-spell-check/language-support.md)に関するページをご覧ください。

## <a name="rare-or-foreign-words-in-an-application"></a>アプリケーションの珍しい単語または外国の単語
`en-us` カルチャの LUIS では、学習によって英語の単語のほとんど (スラングを含む) が区別されます。 `zh-cn` カルチャの LUIS では、学習によってほとんどの中国語の文字が区別されます。 `en-us` で珍しい単語を使用するか、`zh-cn` で珍しい文字を使用する場合、LUIS で単語または文字を区別できない可能性があるときは、その単語または文字を[フレーズ リスト機能](luis-how-to-add-features.md)に追加できます。 たとえば、アプリケーションのカルチャ以外の単語、つまり外国の単語は、フレーズ リスト機能に追加する必要があります。

<!--This phrase list should be marked non-interchangeable, to indicate that the set of rare words forms a class that LUIS should learn to recognize, but they are not synonyms or interchangeable with each other.-->

### <a name="hybrid-languages"></a>ハイブリッド言語
ハイブリッド言語では、英語、中国語など、2 つのカルチャの単語が結合されます。 アプリが基づくカルチャは 1 つであるため、これらの言語は LUIS ではサポートされていません。

## <a name="tokenization"></a>トークン化
機械学習を実行するために、LUIS では、発話がカルチャに基づいて[トークン](luis-glossary.md#token)に分割されます。

|Language|  すべてのスペースまたは特殊文字 | 文字レベル|複合語
|--|:--:|:--:|:--:|
|アラビア語|✔|||
|Chinese||✔||
|オランダ語|✔||✔|
|英語 (en-us)|✔ |||
|フランス語 (fr-FR)|✔|||
|フランス語 (fr-CA)|✔|||
|ドイツ語|✔||✔|
|グジャラート語|✔|||
|ヒンディー語|✔|||
|イタリア語|✔|||
|日本語|||✔
|韓国語||✔||
|マラーティー語|✔|||
|ポルトガル語 (ブラジル)|✔|||
|スペイン語 (es-ES)|✔|||
|スペイン語 (es-MX)|✔|||
|タミル語|✔|||
|テルグ語|✔|||
|トルコ語|✔|||


### <a name="custom-tokenizer-versions"></a>カスタム トークナイザーのバージョン

次のカルチャには、カスタム トークナイザーのバージョンがあります。

|カルチャ|Version|目的|
|--|--|--|
|ドイツ語<br>`de-de`|1.0.0|複合語をその単一コンポーネントに分解しようとする機械学習ベースのトークナイザーを使用して分割することによって、単語をトークン化します。<br>ユーザーが発話として `Ich fahre einen krankenwagen` を入力した場合、それは `Ich fahre einen kranken wagen` に変換されます。 `kranken` と `wagen` を別のエンティティとして独立にマークすることが可能になります。|
|ドイツ語<br>`de-de`|1.0.2|スペースで分割することによって、単語をトークン化します。<br> ユーザーが発話として `Ich fahre einen krankenwagen` を入力した場合、それは 1つのトークンとして保持されます。 そのため、`krankenwagen` は 1 つのエンティティとしてマークされます。 |
|オランダ語<br>`nl-nl`|1.0.0|複合語をその単一コンポーネントに分解しようとする機械学習ベースのトークナイザーを使用して分割することによって、単語をトークン化します。<br>ユーザーが発話として `Ik ga naar de kleuterschool` を入力した場合、それは `Ik ga naar de kleuter school` に変換されます。 `kleuter` と `school` を別のエンティティとして独立にマークすることが可能になります。|
|オランダ語<br>`nl-nl`|1.0.1|スペースで分割することによって、単語をトークン化します。<br> ユーザーが発話として `Ik ga naar de kleuterschool` を入力した場合、それは 1つのトークンとして保持されます。 そのため、`kleuterschool` は 1 つのエンティティとしてマークされます。 |


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

バージョンの代わりに、[新しいアプリとしてファイルをインポート](luis-how-to-start-new-app.md)します。 このアクションは、新しいアプリはアプリ ID が異なるが、ファイルで指定されたトークナイザーのバージョンを使用することを示します。
