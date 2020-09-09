---
title: クイック スタート:Ruby 用 Text Analytics クライアント ライブラリ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Azure Cognitive Services の Ruby Text Analytics クライアント ライブラリを使用して言語を検出します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 8afceb19af0d177415d0b68b5d38f19d18835af5
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291764"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>クイック スタート:Ruby 用 Text Analytics クライアント ライブラリを使用する

Text Analytics クライアント ライブラリの概要を紹介します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

Text Analytics クライアント ライブラリを使って次のことを実行します。

* センチメント分析
* 言語検出
* エンティティの認識
* キー フレーズの抽出

> [!NOTE]
> このクイックスタートは、Text Analytics バージョン 2.1 だけに適用されます。 現在、Ruby 用 v3 クライアント ライブラリは使用できません。

[ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [パッケージ (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Ruby](https://www.ruby-lang.org/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成"  target="_blank">Text Analytics リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 
    * アプリケーションを Text Analytics API に接続するには、作成するリソースのキーとエンドポイントが必要です。 この作業は、このクイックスタートの中で後から行います。
    * Free 価格レベルを使ってサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-ruby-application"></a>新しい Ruby アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。 次に、`GemFile` という名前のファイルと、自分のコード用の Ruby ファイルを作成します。

```console
mkdir myapp && cd myapp
```

`GemFile` に次の行を追加して、クライアント ライブラリを依存関係として追加します。

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

Ruby ファイルに、次のパッケージをインポートします。

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

自分のリソースの Azure エンドポイントおよびキー用の変数を作成します。 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>オブジェクト モデル 

Text Analytics クライアントでは、ご利用のキーを使用して Azure に対して認証を行います。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。 

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Ruby 用 Text Analytics クライアント ライブラリを使用して次のことを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [エンティティの認識](#entity-recognition)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>クライアントを認証する

`TextAnalyticsClient` という名前のクラスを作成します。 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

このクラスに、キーとエンドポイントを使用してクライアントを認証する `initialize` という関数を作成します。 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

クラスの外部で、クライアントの `new()` 関数を使用してそれをインスタンス化します。

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>センチメント分析

クライアント オブジェクト内で、後で作成される入力ドキュメントの一覧を受け取る `AnalyzeSentiment()` という名前の関数を作成します。 クライアントの `sentiment()` 関数を呼び出し、結果を取得します。 結果を反復処理し、各ドキュメントの ID とセンチメント スコアを印刷します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

クライアント関数の外部で、上記で作成した `TextAnalyticsClient` オブジェクトを受け取る `SentimentAnalysisExample()` という新しい関数を作成します。 分析するドキュメントを含む `MultiLanguageInput` オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`Language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。 次に、クライアントの `AnalyzeSentiment()` 関数を呼び出します。

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

`SentimentAnalysisExample()` 関数を呼び出します。

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>出力

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>言語検出

クライアント オブジェクト内で、後で作成される入力ドキュメントの一覧を受け取る `DetectLanguage()` という名前の関数を作成します。 クライアントの `detect_language()` 関数を呼び出し、結果を取得します。 結果を反復処理し、各ドキュメントの ID と検出された言語を出力します。

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

クライアント関数の外部で、上記で作成した `TextAnalyticsClient` オブジェクトを受け取る `DetectLanguageExample()` という新しい関数を作成します。 分析するドキュメントを含む `LanguageInput` オブジェクトの一覧を作成します。 各オブジェクトには、`id`、および `text` 属性が含まれます。 `text` 属性には、分析対象のテキストが格納され、`id` には任意の値を指定できます。 次に、クライアントの `DetectLanguage()` 関数を呼び出します。

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

`DetectLanguageExample()` 関数を呼び出します。

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>出力

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>エンティティの認識

クライアント オブジェクト内で、後で作成される入力ドキュメントの一覧を受け取る `RecognizeEntities()` という名前の関数を作成します。 クライアントの `entities()` 関数を呼び出し、結果を取得します。 次に、結果を反復処理し、各ドキュメントの ID と認識されたエンティティを出力します。

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

クライアント関数の外部で、上記で作成した `TextAnalyticsClient` オブジェクトを受け取る `RecognizeEntitiesExample()` という新しい関数を作成します。 分析するドキュメントを含む `MultiLanguageInput` オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はテキストの言語であり、`id` には任意の値を指定できます。 次に、クライアントの `RecognizeEntities()` 関数を呼び出します。

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

`RecognizeEntitiesExample()` 関数を呼び出します。

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>出力

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

クライアント オブジェクト内で、後で作成される入力ドキュメントの一覧を受け取る `ExtractKeyPhrases()` という名前の関数を作成します。 クライアントの `key_phrases()` 関数を呼び出し、結果を取得します。 結果を反復処理し、各ドキュメントの ID と抽出されたキー フレーズを出力します。

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

クライアント関数の外部で、上記で作成した `TextAnalyticsClient` オブジェクトを受け取る `KeyPhraseExtractionExample()` という新しい関数を作成します。 分析するドキュメントを含む `MultiLanguageInput` オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はテキストの言語であり、`id` には任意の値を指定できます。 次に、クライアントの `ExtractKeyPhrases()` 関数を呼び出します。

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


`KeyPhraseExtractionExample()` 関数を呼び出します。

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>出力

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                rock
Document ID: 4
         Key phrases:
                fútbol
```
