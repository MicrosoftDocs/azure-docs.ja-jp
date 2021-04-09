---
title: V3 に移行する - Translator
titleSuffix: Azure Cognitive Services
description: この記事では、Azure Cognitive Services Translator を V2 から V3 に移行する手順について説明します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 13c4d39284fad293c945f8b7e31076dccee84fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896835"
---
# <a name="translator-v2-to-v3-migration"></a>Translator V2 から V3 への移行

> [!NOTE]
> V2 は、2018 年 4 月 30 日に非推奨となりました。 V3 で独占的に使用できる新しい機能を活用するためにアプリケーションを V3 に移行してください。 V2 は 2021 年 5 月 24 日に廃止される予定です。 

Microsoft Translator チームは Translator のバージョン 3 (V3) をリリースしました。 このリリースには、新しい機能、非推奨になったメソッド、Microsoft Translator Service との間でデータを送受信するための新しい形式が含まれています。 このドキュメントでは、V3 を使用するためにアプリケーションを変更する方法に関する情報を提供します。 

このドキュメントの終わりには、役に立つ詳細ページのリンクが記載されています。

## <a name="summary-of-features"></a>機能のまとめ

* トレースなし - V3 では、Azure Portal の全価格レベルにトレースが適用されません。 つまり、この機能では、V3 API に送信されたテキストを Microsoft が保存することはありません。
* JSON - XML が JSON に取って代わられます。 サービスに届くデータとサービスから届くデータはすべて JSON 形式になります。
* 1 回の要求で複数のターゲット言語 - 翻訳メソッドは、1 回の要求で複数の "翻訳先" 言語に応じます。 たとえば、1 回の要求で "翻訳元" を英語とし、"翻訳先" にドイツ語、スペイン語、日本語、その他の言語グループを指定できます。
* 2 か国語辞書 - 2 か国語辞書メソッドが API に追加されました。 このメソッドには "lookup" と "examples" が含まれます。
* 音訳 - 音訳メソッドが API に追加されました。 このメソッドでは、ある文字体系 (アラビア語 など) で書かれた言葉や文章が別の文字体系 (ラテン語 など) に変換されます。
* 言語 - 新しい "languages" メソッドは、"translate"、"dictionary"、"transliterate" メソッドで使用するために、言語情報を JSON 形式で届けます。
* 翻訳の新機能 - 別のメソッドとして V2 API に存在した一部の機能に対応する目的で、新しい機能が "translate" メソッドに追加されました。 それには、たとえば、TranslateArray があります。
* 読み上げメソッド - テキスト読み上げ機能は、Microsoft Translator でサポートから外れました。 テキスト読み上げ機能は、[Microsoft Speech Service](../speech-service/text-to-speech.md) で利用できます。

V2 メソッドと V3 メソッドについてまとめた次のリストで、V3 メソッドと、V2 に備わっていた機能を提供する API を確認できます。

| V2 API メソッド   | V3 API 互換性 |
|:----------- |:-------------|
| `Translate`     | [Translate](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Translate](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Languages](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Languages](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Microsoft Speech Service](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Microsoft Speech Service](../speech-service/text-to-speech.md)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | この機能は現在サポートされていません       |
| `AddTranslationArray`    | この機能は現在サポートされていません          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | この機能は現在サポートされていません         |
| `GetTranslationsArray`      | この機能は現在サポートされていません         |

## <a name="move-to-json-format"></a>JSON 形式に移行

Microsoft Translator Translation V2 では、XML 形式でデータを受け取り、返しました。 V3 では、API を利用して送受信されるすべてのデータが JSON 形式になります。 XML は V3 では受け取られず、返されません。

この変更は、V2 Text Translation API のために記述されたアプリケーションのいくつかの面に影響を与えます。 例Languages API は、テキスト翻訳メソッド、表記変換メソッド、2 か国語辞書メソッドの言語情報を返します。 1 回の呼び出しですべてのメソッドのすべての言語情報を要求するか、それらを個別に要求できます。

言語メソッドは認証を要求しません。次のリンクをクリックすると、V3 の全言語情報を JSON で参照できます。

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>認証キー

V2 で使用している認証キーは、V3 で受け取られます。 新しいサブスクリプションを取得する必要はありません。 移行期間が 1 年間続くとき、アプリで V2 と V3 を混在させることができます。V2-XML から V3-JSON に移行しながら、新しいバージョンを公開することが簡単にできます。

## <a name="pricing-model"></a>価格モデル

Microsoft Translator V3 の価格設定は V2 のときと同じであり、スペースを含む文字あたりとなります。 V3 に新機能が追加されたことで、課金対象となる文字の数え方にいくつかの変更があります。

| V3 メソッド   | 課金対象となる文字数 |
|:----------- |:-------------|
| `Languages`     | いかなる文字も送信されず、何も数えられず、何も課金されません。          |
| `Translate`     | 翻訳のために送信された文字の数と文字の翻訳先となる言語の数に基づいて数えられます。 50 文字が送信され、5 か国語が要求された場合、50x5 で計算されます。           |
| `Transliterate`     | 音訳のために送信された文字数が数えられます。         |
| `Dictionary lookup & example`     | Dictionary の lookup と examples のために送信された文字数が数えられます。         |
| `BreakSentence`     | 課金はありません。       |
| `Detect`     | 課金はありません。      |

## <a name="v3-end-points"></a>V3 エンド ポイント

グローバル

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>V3 API テキスト翻訳メソッド

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>互換性とカスタマイズ

> [!NOTE]
> 
> Microsoft Translator Hub は、2019 年 5 月 17 日をもって廃止されます。 [重要な移行情報と日付を確認してください](https://www.microsoft.com/translator/business/hub/)。   

Microsoft Translator V3 では、ニューラル機械翻訳が既定で使用されます。 そのため、Microsoft Translator Hub では使用できません。 Translator Hub は、従来の統計的な機械翻訳のみをサポートしています。 ニューラル翻訳のカスタマイゼーションは、Custom Translator を使用してご利用いただけるようになりました。 [ニューラル機械翻訳のカスタマイゼーションに関する詳細](custom-translator/overview.md)

V3 テキスト API によるニューラル翻訳では、標準カテゴリ (SMT、speech、tech、generalnn) を使用できません。

| Version | エンドポイント | GDPR のプロセッサ準拠 | Translator Hub の使用 | カスタム トランスレーター (プレビュー) の使用 |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Translator バージョン 2|    api.microsofttranslator.com|    いいえ    |はい    |いいえ|
|Translator バージョン 3|    api.cognitive.microsofttranslator.com|    はい|    いいえ|    はい|

**Translator バージョン 3**
* 一般提供されており完全にサポートされます。
* プロセッサとして GDPR に準拠し、ISO 20001 と 20018 のほか、SOC 3 認定要件をすべて満たしています。 
* Translator の新しい NMT カスタマイズ機能であるカスタム トランスレーター (プレビュー) を使ってカスタマイズしたニューラル ネットワーク翻訳システムを呼び出すことができます。 
* Microsoft Translator Hub を使用して作成されたカスタム翻訳システムにアクセスすることはできません。

使用しているエンドポイントが api.cognitive.microsofttranslator.com である場合、ご使用の Translator のバージョンは 3 です。

**Translator バージョン 2**
* ISO 20001、20018、SOC 3 の認定要件をすべて満たしているわけではありません。 
* Translator のカスタマイズ機能を使ってカスタマイズしたニューラル ネットワーク翻訳システムを呼び出すことはできません。
* Microsoft Translator Hub を使用して作成されたカスタム翻訳システムにアクセスすることができます。
* 使用しているエンドポイントが api.microsofttranslator.com である場合、ご使用の Translator のバージョンは 2 です。

どのバージョンの Translator でも、翻訳の記録は作成されません。 翻訳はだれとも共有されません。 詳細については、[Translator の「トレースなし」](https://www.aka.ms/NoTrace)という Web ページを参照してください。

## <a name="links"></a>リンク

* [Microsoft プライバシーに関する声明](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure の法的情報](https://azure.microsoft.com/support/legal)
* [オンライン サービス規約](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [V3.0 ドキュメントを見る](reference/v3-0-reference.md)
