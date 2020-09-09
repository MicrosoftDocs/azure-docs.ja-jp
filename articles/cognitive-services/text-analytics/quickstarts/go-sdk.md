---
title: クイック スタート:Go 用 Text Analytics クライアント ライブラリ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Azure Cognitive Services の Go Text Analytics クライアント ライブラリを使用して言語を検出します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.openlocfilehash: 6849046211ae4216f181dd8ef0ca391c2876363c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291790"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>クイック スタート:Go 用 Text Analytics クライアント ライブラリを使用する

- [リファレンス ドキュメント](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics)
- [ライブラリ ソース コード](https://github.com/Azure/azure-sdk-for-go/blob/090dc0ee4d8d2d60e2a9525774d967a4111a2b0c/services/cognitiveservices/v2.1/textanalytics/client.go)
- [パッケージ (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)
- [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> このクイックスタートは、Text Analytics バージョン 2.1 だけに適用されます。 現在、Go 用 v3 クライアント ライブラリは使用できません。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* 最新バージョンの [Go](https://golang.org/dl/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Text Analytics リソースを作成"  target="_blank">Text Analytics リソースを作成<span class="docon docon-navigate-external x-hidden-focus"></span></a>し、キーとエンドポイントを取得します。 
    * アプリケーションを Text Analytics API に接続するには、作成するリソースのキーとエンドポイントが必要です。 この作業は、このクイックスタートの中で後から行います。
    * Free 価格レベルを使ってサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

### <a name="create-a-new-go-project"></a>新しい Go プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、ターミナル、Bash) で、Go プロジェクト用に新しいワークスペースを作成し、そこに移動します。 ワークスペースには次の 3 つのフォルダーが格納されます。 

* **src** - このディレクトリには、ソース コードとパッケージが格納されます。 `go get` コマンドを使用してインストールされるパッケージはすべてここに存在します。
* **pkg** - このディレクトリには、コンパイル済みの Go パッケージ オブジェクトが格納されます。 これらのファイルにはいずれも `.a` という拡張子が付きます。
* **bin** - このディレクトリには、`go install` を実行するときに作成されたバイナリ実行可能ファイルが格納されます。

> [!TIP]
> [Go ワークスペース](https://golang.org/doc/code.html#Workspaces)の構造についての詳細情報をご覧いただけます。 このガイドには、`$GOPATH` と `$GOROOT` の設定に関する情報が記載されています。

`my-app` というワークスペースと必須のサブディレクトリ (`src`、`pkg`、`bin`) を作成します。

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Go 用 Text Analytics クライアント ライブラリをインストールする

Go 用クライアント ライブラリをインストールします。 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

または、dep を使用している場合は、リポジトリ内で次を実行します。

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Go アプリケーションを作成する

次に、`src/quickstart.go` という名前のファイルを作成します。

```bash
$ cd src
$ touch quickstart.go
```

普段使用している IDE またはテキスト エディターで `quickstart.go` を開きます。 次に、パッケージの名前を追加し、次のライブラリをインポートします。

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>オブジェクト モデル 

Text Analytics クライアントは、ご利用のキーを使用して Azure に対して認証を行う [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。 

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Go 用 Text Analytics クライアント ライブラリを使用して次のことを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [エンティティの認識](#entity-recognition)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>クライアントを認証する


新しい関数で、リソースの Azure エンドポイントとサブスクリプション キー用の変数を作成します。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

新しい [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) オブジェクトを作成します。 ご利用のキーを [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) 関数に渡すと、そのキーがクライアントの `authorizer` プロパティに渡されます。

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>センチメント分析

`SentimentAnalysis()` という新しい関数を作成し、前に作成した `GetTextAnalyticsClient()` メソッドを使用してクライアントを作成します。 分析するドキュメントを含む [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`Language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。 

クライアントの [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) 関数を呼び出し、結果を取得します。 結果を反復処理し、各ドキュメントの ID とセンチメント スコアを印刷します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

プロジェクト内で `SentimentAnalysis()` を呼び出します。

### <a name="output"></a>出力

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>言語検出

`LanguageDetection()` という新しい関数を作成し、前に作成した `GetTextAnalyticsClient()` メソッドを使用してクライアントを作成します。 分析するドキュメントを含む [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id` および `text` 属性が含まれます。 `text` 属性には、分析対象のテキストが格納され、`id` には任意の値を指定できます。 

クライアントの [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と検出された言語を出力します。

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

プロジェクト内で `LanguageDetection()` を呼び出します。

### <a name="output"></a>出力

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>エンティティの認識

`ExtractEntities()` という新しい関数を作成し、前に作成した `GetTextAnalyticsClient()` メソッドを使用してクライアントを作成します。 分析するドキュメントを含む [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。 

クライアントの [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) を呼び出して結果を取得します。 次に、結果を反復処理し、各ドキュメントの ID と抽出されたエンティティ スコアを出力します。

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

プロジェクト内で `ExtractEntities()` を呼び出します。

### <a name="output"></a>出力

```console
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

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

`ExtractKeyPhrases()` という新しい関数を作成し、前に作成した `GetTextAnalyticsClient()` メソッドを使用してクライアントを作成します。 分析するドキュメントを含む [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。

クライアントの [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と抽出されたキー フレーズを出力します。

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

プロジェクト内で `ExtractKeyPhrases()` を呼び出します。

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
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
