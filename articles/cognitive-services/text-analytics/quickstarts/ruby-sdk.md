---
title: クイック スタート:Ruby SDK を使用して Text Analytics Cognitive Service を呼び出す
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: tasharm
ms.openlocfilehash: 5e1578d88d6e151479961aeaf7a54c130419f282
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142711"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>クイック スタート:Ruby SDK を使用して Text Analytics サービスを呼び出す

<a name="HOLTop"></a>


このクイックスタートを使用して、Ruby 用 Text Analytics SDK を使用した言語の分析を開始します。 [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API は、ほとんどのプログラミング言語に対応しますが、この SDK を使用すれば、アプリケーションに対して簡単にサービスを統合することができます。 このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb) にあります。

API の技術ドキュメントについては、[API の定義](//go.microsoft.com/fwlink/?LinkID=759346)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

* [Ruby 2.5.5 以降](https://www.ruby-lang.org/)
* Text Analytics [SDK for Ruby](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
 
[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Ruby プロジェクトを作成し SDK をインストールする

1. 新しい Ruby プロジェクトを作成し、`Gemfile` という名前の新しいファイルを追加します。
2. 下のコードを `Gemfile` に追加することで、Text Analytics SDK をプロジェクトに追加します。

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

## <a name="create-a-text-analytics-client"></a>Text Analytics クライアントを作成する

1. 任意のエディターまたは IDE で `TextAnalyticsExamples.rb` という名前の新しいファイルを作成します。 Text Analytics SDK をインポートします。

2. 資格情報オブジェクトは、Text Analytics クライアントで使用されます。 これを `CognitiveServicesCredentials.new()` で作成し、サブスクリプション キーを渡します。

3. 正しい Text Analytics エンドポイントでクライアントを作成します。

```ruby
# encoding: UTF-8
# Without this encoding directive, you might get an error such as:
# sdk.rb:60: invalid multibyte char (UTF-8)

require 'azure_cognitiveservices_textanalytics'

include Azure::CognitiveServices::TextAnalytics::V2_1::Models

key_var = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if (!ENV[key_var])
    raise "Please set/export the following environment variable: " + key_var
else
    subscription_key = ENV[key_var]
end

endpoint_var = "TEXT_ANALYTICS_ENDPOINT"
if (!ENV[endpoint_var])
    raise "Please set/export the following environment variable: " + endpoint_var
else
    endpoint = ENV[endpoint_var]
end

credentials = MsRestAzure::CognitiveServicesCredentials.new(subscription_key)

textAnalyticsClient =
    Azure::TextAnalytics::Profiles::Latest::Client.new({
        credentials: credentials
    })
textAnalyticsClient.endpoint = endpoint
```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>センチメント分析

Text Analytics SDK または API を使用すると、一連のテキスト レコードのセンチメント分析を実行できます。 次の例は、複数のドキュメントのセンチメント スコアを表示します。

1. 上記で作成した Text Analytics クライアントをパラメーターとして使用する `SentimentAnalysisExample()` という新しい関数を作成します。

2. 分析する一連の `MultiLanguageInput` オブジェクトを定義します。 各オブジェクトの言語とテキストを追加します。 ID には任意の値を指定できます。

```ruby
def SentimentAnalysisExample(client)
    # The documents to be analyzed. Add the language of the document. The ID can be any value.
    input_1 = MultiLanguageInput.new
    input_1.id = '1'
    input_1.language = 'en'
    input_1.text = 'I had the best day of my life.'

    input_2 = MultiLanguageInput.new
    input_2.id = '2'
    input_2.language = 'en'
    input_2.text = 'This was a waste of my time. The speaker put me to sleep.'

    input_3 = MultiLanguageInput.new
    input_3.id = '3'
    input_3.language = 'es'
    input_3.text = 'No tengo dinero ni nada que dar...'

    input_4 = MultiLanguageInput.new
    input_4.id = '4'
    input_4.language = 'it'
    input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
```

3. 同じ関数内では、複数のドキュメントを 1 つの一覧にまとめます。 それを `MultiLanguageBatchInput` オブジェクトの `documents` フィールドに追加します。 

4. `MultiLanguageBatchInput` オブジェクトをパラメーターとしてクライアントの `sentiment()` 関数を呼び出し、ドキュメントを送信します。 結果が返されたら、それらを印刷します。
```ruby
    input_documents =  MultiLanguageBatchInput.new
    input_documents.documents = [input_1, input_2, input_3, input_4]

    result = client.sentiment(
        multi_language_batch_input: input_documents
    )
  
    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
            puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
    end
end
```

5. `SentimentAnalysisExample()` 関数を呼び出します。

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>言語検出

Text Analytics サービスは、多数の言語およびロケールから、テキスト ドキュメントの言語を検出できます。 次の例は、複数のドキュメントの記述で使用された言語を表示します。

1. 上記で作成した Text Analytics クライアントをパラメーターとして使用する `DetectLanguageExample()` という新しい関数を作成します。 

2. 分析する一連の `LanguageInput` オブジェクトを定義します。 各オブジェクトの言語とテキストを追加します。 ID には任意の値を指定できます。

```ruby
def DetectLanguageExample(client)
    # The documents to be analyzed.
    language_input_1 = LanguageInput.new
    language_input_1.id = '1'
    language_input_1.text = 'This is a document written in English.'

    language_input_2 = LanguageInput.new
    language_input_2.id = '2'
    language_input_2.text = 'Este es un document escrito en Español..'

    language_input_3 = LanguageInput.new
    language_input_3.id = '3'
    language_input_3.text = '这是一个用中文写的文件'
```

3. 同じ関数内では、複数のドキュメントを 1 つの一覧にまとめます。 それを `LanguageBatchInput` オブジェクトの `documents` フィールドに追加します。 

4. `LanguageBatchInput` オブジェクトをパラメーターとしてクライアントの `detect_language()` 関数を呼び出し、ドキュメントを送信します。 結果が返されたら、それらを印刷します。
```ruby
    input_documents = LanguageBatchInput.new
    input_documents.documents = [language_input_1, language_input_2, language_input_3]

    result = client.detect_language(
        language_batch_input: input_documents
    )

    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== LANGUAGE DETECTION ====='
        result.documents.each do |document|
            puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
        end
    else
        puts 'No results data..'
    end
end
```

5. 関数 `DetectLanguageExample` を呼び出します

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>エンティティの認識

Text Analytics サービスは、テキスト ドキュメント内のさまざまなエンティティ (人、場所、物) を区別し抽出できます。 次の例は、いくつかのドキュメント例で見つかったエンティティを表示します。

1. 上記で作成した Text Analytics クライアントをパラメーターとして使用する `Recognize_Entities()` という新しい関数を作成します。

2. 分析する一連の `MultiLanguageInput` オブジェクトを定義します。 各オブジェクトの言語とテキストを追加します。 ID には任意の値を指定できます。

```ruby
def RecognizeEntitiesExample(client)
    # The documents to be analyzed.
    input_1 = MultiLanguageInput.new
    input_1.id = '1'
    input_1.language = 'en'
    input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'

    input_2 = MultiLanguageInput.new
    input_2.id = '2'
    input_2.language = 'es'
    input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. Within the same function, combine the documents into a list. Add it to the `documents` field of a `MultiLanguageBatchInput` object. 

4. Call the client's `entities()` function with the `MultiLanguageBatchInput` object as a parameter to send the documents. If any results are returned, print them.

```ruby
    input_documents = MultiLanguageBatchInput.new
    input_documents.documents = [input_1, input_2]

    result = client.entities(
    multi_language_batch_input: input_documents
    )

    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== ENTITY RECOGNITION ====='
        result.documents.each do |document|
            puts "Document ID: #{document.id}"
            document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                    puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
            end
        end
    else
        puts 'No results data..'
    end
end
```

5. 関数 `RecognizeEntitiesExample` を呼び出します
```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Output

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

Text Analytics サービスは、文内のキー フレーズを抽出できます。 次の例は、いくつかのドキュメント例で見つかったエンティティを複数の言語で表示します。

1. 上記で作成した Text Analytics クライアントをパラメーターとして使用する `KeyPhraseExtractionExample()` という新しい関数を作成します。

2. 分析する一連の `MultiLanguageInput` オブジェクトを定義します。 各オブジェクトの言語とテキストを追加します。 ID には任意の値を指定できます。

```ruby
def KeyPhraseExtractionExample(client)
    # The documents to be analyzed.
    input_1 = MultiLanguageInput.new
    input_1.id = '1'
    input_1.language = 'ja'
    input_1.text = '猫は幸せ'

    input_2 = MultiLanguageInput.new
    input_2.id = '2'
    input_2.language = 'de'
    input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'

    input_3 = MultiLanguageInput.new
    input_3.id = '3'
    input_3.language = 'en'
    input_3.text = 'My cat is stiff as a rock.'

    input_4 = MultiLanguageInput.new
    input_4.id = '4'
    input_4.language = 'es'
    input_4.text = 'A mi me encanta el fútbol!'
```

3. 同じ関数内では、複数のドキュメントを 1 つの一覧にまとめます。 それを `MultiLanguageBatchInput` オブジェクトの `documents` フィールドに追加します。 

4. `MultiLanguageBatchInput` オブジェクトをパラメーターとしてクライアントの `key_phrases()` 関数を呼び出し、ドキュメントを送信します。 結果が返されたら、それらを印刷します。

```ruby
    input_documents =  MultiLanguageBatchInput.new
    input_documents.documents = [input_1, input_2, input_3, input_4]

    result = client.key_phrases(
        multi_language_batch_input: input_documents
    )

    if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
            puts "Document Id: #{document.id}"
            puts '  Key Phrases'
            document.key_phrases.each do |key_phrase|
                puts "    #{key_phrase}"
            end
        end
    else
        puts 'No results data..'
    end
end
```

5. 関数 `KeyPhraseExtractionExample` を呼び出します

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
```

### <a name="output"></a>Output

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

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>関連項目

 [Text Analytics の概要](../overview.md)  
 [よく寄せられる質問 (FAQ)](../text-analytics-resource-faq.md)
