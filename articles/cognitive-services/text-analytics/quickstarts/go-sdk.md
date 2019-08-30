---
title: クイック スタート:Go 用 Text Analytics クライアント ライブラリ | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API の使用をすぐに開始するために役立つ情報とコード サンプルを提供します。
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 25d8052cda422c185d49c36c5daff9ac4582e66c
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881021"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>クイック スタート:Go 用 Text Analytics クライアント ライブラリ

Go 用 Text Analytics クライアント ライブラリの概要を紹介します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。 

Go 用 Text Analytics クライアント ライブラリを使って次のことを実行します。

* センチメント分析
* 言語検出
* エンティティの認識
* キー フレーズの抽出

[リファレンスのドキュメント](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [パッケージ (Github)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [サンプル](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/)
* 最新バージョンの [Go](https://golang.org/dl/)

## <a name="setting-up"></a>設定

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure リソースを作成する 

Azure Cognitive Services は、ユーザーがサブスクライブする Azure リソースによって表されます。 [Azure portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) または [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) を使用して、ローカル コンピューター上に Text Analytics のリソースを作成します。 さらに、以下を実行できます。

* 7 日間有効な[試用版のキー](https://azure.microsoft.com/try/cognitive-services/#decision)を無料で入手する。 これは、サインアップ後に [Azure Web サイト](https://azure.microsoft.com/try/cognitive-services/my-apis/)で入手できます。
* [Azure portal](https://portal.azure.com) でご利用のリソースを表示する。

試用版のサブスクリプションまたはリソースからキーを取得した後、`TEXT_ANALYTICS_SUBSCRIPTION_KEY` という名前のキーの[環境変数を作成](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)します。

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

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
)
```

このクイック スタートのほとんどのパラメーターとプロパティでは文字列とブール ポインターが想定されているので、プロジェクトに次の関数を追加します。

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

プロジェクトの main 関数で、リソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
// This sample assumes you have created an environment variable for your key
subscriptionKey := os.Getenv("TEXT_ANALYTICS_SUBSCRIPTION_KEY")
// replace this endpoint with the correct one for your Azure resource. 
endpoint := "https://eastus.api.cognitive.microsoft.com"
```

## <a name="object-model"></a>オブジェクト モデル 

Text Analytics クライアントは、ご利用のキーを使用して Azure に対して認証を行う [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) オブジェクトです。 このクライアントには、テキストを単一の文字列として、またはバッチとして分析するためのメソッドがいくつか備わっています。 

テキストは、`documents` (使用したメソッドに応じて `id`、`text`、`language` の各属性の組み合わせを保持する `dictionary` オブジェクト) のリストとして API に送信されます。 `text` 属性には、分析対象のテキストが元の `language` で格納され、`id` には任意の値を指定できます。 

応答オブジェクトは、各ドキュメントの分析情報を格納するリストです。 

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python 用 Text Analytics クライアント ライブラリを使用して次のことを実行する方法が示されています。

* [クライアントを認証する](#authenticate-the-client)
* [感情分析](#sentiment-analysis)
* [言語検出](#language-detection)
* [エンティティの認識](#entity-recognition)
* [キー フレーズ抽出](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>クライアントを認証する

プロジェクトの main 関数で、新しい [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) オブジェクトを作成します。 ご利用のキーを [autorest.NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) 関数に渡すと、そのキーがクライアントの `authorizer` プロパティに渡されます。

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>センチメント分析

上記で作成したクライアントを受け取る `SentimentAnalysis()` という新しい関数を作成します。 分析するドキュメントを含む [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`Language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

同じ関数内で、クライアントの [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) 関数を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID とセンチメント スコアを印刷します。 0 に近いスコアは否定的センチメント、1 に近いスコアは肯定的センチメントを示します。

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

プロジェクトの main 関数で `SentimentAnalysis()` を呼び出します。

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>言語検出

上記で作成したクライアントを受け取る `LanguageDetection()` という新しい関数を作成します。 分析するドキュメントを含む [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id` および `text` 属性が含まれます。 `text` 属性には、分析対象のテキストが格納され、`id` には任意の値を指定できます。 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

同じ関数内で、クライアントの [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と検出された言語を出力します。

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

プロジェクトの main 関数で `LanguageDetection()` を呼び出します。

### <a name="output"></a>Output

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>エンティティの認識

上記で作成したクライアントを受け取る `ExtractEntities()` という新しい関数を作成します。 分析するドキュメントを含む [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        }
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

同じ関数内で、クライアントの [Entities()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) を呼び出して結果を取得します。 次に、結果を反復処理し、各ドキュメントの ID と抽出されたエンティティ スコアを出力します。

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

プロジェクトの main 関数で `ExtractEntities()` を呼び出します。

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>キー フレーズの抽出

上記で作成したクライアントを受け取る `ExtractKeyPhrases()` という新しい関数を作成します。 分析するドキュメントを含む [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) オブジェクトの一覧を作成します。 各オブジェクトには、`id`、`language`、および `text` 属性が含まれます。 `text` 属性には分析対象のテキストが格納され、`language` はドキュメントの言語であり、`id` には任意の値を指定できます。

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

同じ関数内で、クライアントの [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) を呼び出して結果を取得します。 結果を反復処理し、各ドキュメントの ID と抽出されたキー フレーズを出力します。

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

プロジェクトの main 関数で `ExtractKeyPhrases()` を呼び出します。

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、そのリソース グループに関連付けられている他のリソースも削除されます。

* [ポータル](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次の手順


> [!div class="nextstepaction"]
> [Text Analytics と Power BI](../tutorials/tutorial-power-bi-key-phrases.md)


* [Text Analytics の概要](../overview.md)
* [感情分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [エンティティの認識](../how-tos/text-analytics-how-to-entity-linking.md)
* [言語を検出する](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [言語の認識](../how-tos/text-analytics-how-to-language-detection.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices) にあります。
