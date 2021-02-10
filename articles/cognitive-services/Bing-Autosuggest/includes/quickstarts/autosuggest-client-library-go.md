---
title: Bing Autosuggest Go クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: 1c3ed6cc97c6fc77083baa915fac2f5530922eac
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947986"
---
Go 用 Bing Autosuggest クライアント ライブラリの使用を開始します。 以下の手順に従って、ライブラリをインストールし、基本タスクの例を試してみましょう。

Go 用 Bing Autosuggest クライアント ライブラリを使用して、部分クエリ文字列に基づいて検索候補を取得します。

[リファレンス ドキュメント](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [サンプル コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/BingAutoSuggest/BingAutoSuggestQuickstart.go)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[無料で作成](https://azure.microsoft.com/free/cognitive-services)できます。
* 最新バージョンの [Go](https://golang.org/dl/)。

Azure リソースを作成して、Bing Autosuggest クライアント ライブラリの使用を開始します。 適切なリソースの種類を次の中から選択します。

[!INCLUDE [cognitive-services-bing-autosuggest-signup-requirements](~/includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-environment-variables"></a>環境変数を作成する

>[!NOTE]
> 2019 年 7 月 1 日より後に作成されたリソースのエンドポイントでは、次に示すカスタム サブドメイン形式を使用します。 リージョンのエンドポイントの詳細および全一覧については、「[Cognitive Services のカスタム サブドメイン名](../../../cognitive-services-custom-subdomains.md)」を参照してください。

作成したリソースのキーとエンドポイントを使用して、認証用に 2 つの環境変数を作成します。
<!-- replace the below variable names with the names expected in the code sample.-->
* `AUTOSUGGEST_SUBSCRIPTION_KEY`:要求を認証するためのリソース キー。
* `AUTOSUGGEST_ENDPOINT`:API 要求を送信するためのリソース エンドポイント。 `https://<your-custom-subdomain>.api.cognitive.microsoft.com` のようになります。

ご利用のオペレーティング システムの手順に従ってください。
<!-- replace the below endpoint and key examples -->
### <a name="windows"></a>[Windows](#tab/windows)

```console
setx BING_AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx BING_AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
```

環境変数を追加したら、コンソール ウィンドウを再起動します。

### <a name="linux"></a>[Linux](#tab/linux)

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source ~/.bashrc` を実行します。

### <a name="macos"></a>[macOS](#tab/unix)

次のように `.bash_profile` を編集し、環境変数を追加します。

```bash
export AUTOSUGGEST_SUBSCRIPTION_KEY=<replace-with-your-autosuggest-api-key>
export AUTOSUGGEST_ENDPOINT=<replace-with-your-autosuggest-api-endpoint>
```

環境変数を追加した後、変更を有効にするには、コンソール ウィンドウから `source .bash_profile` を実行します。
***

## <a name="create-a-new-go-project"></a>新しい Go プロジェクトを作成する

コンソール ウィンドウ (cmd、PowerShell、ターミナル、Bash) で、Go プロジェクト用に新しいワークスペースを作成し、そこに移動します。 ワークスペースには次の 3 つのフォルダーが格納されます。

* **src**:このディレクトリには、ソース コードとパッケージが格納されます。 `go get` コマンドを使用してインストールされるパッケージはすべてここに存在します。
* **pkg**: このディレクトリには、コンパイル済みの Go パッケージ オブジェクトが格納されます。 これらのファイルにはいずれも `.a` という拡張子が付きます。
* **bin**: このディレクトリには、`go install` を実行するときに作成されたバイナリ実行可能ファイルが格納されます。

> [!TIP]
> [Go ワークスペース](https://golang.org/doc/code.html#Workspaces)の構造についての詳細情報をご覧いただけます。 このガイドには、`$GOPATH` と `$GOROOT` の設定に関する情報が記載されています。

`my-app` というワークスペースと必須のサブディレクトリ (`src`、`pkg`、`bin`) を作成しましょう。

```
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

## <a name="install-the-client-library-for-go"></a>Go 用クライアント ライブラリをインストールする

ここで、Go 用クライアント ライブラリをインストールします。

```bash
$ go get -u <library-location-or-url>
```

または、dep を使用している場合は、リポジトリ内で次を実行します。

```bash
$ dep ensure -add <library-location-or-url>
```

## <a name="create-your-go-application"></a>Go アプリケーションを作成する

次に、`src/sample-app.go` という名前のファイルを作成します。

```bash
$ cd src
$ touch sample-app.go
```

`sample-app.go` を開き、パッケージの名前を追加して、次のライブラリをインポートします。

```Go
package main

import (
    "context"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v1.0/autosuggest"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"
)
```

`main` という名前の関数を作成します。 次に、Bing Autosuggest キーとエンドポイントの環境変数を作成します。

```go
func main() {
    // Add your Bing Autosuggest subscription key to your environment variables.
    if "" == os.Getenv("BING_AUTOSUGGEST_SUBSCRIPTION_KEY") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_SUBSCRIPTION_KEY.")
    }
    // Add your Bing Autosuggest endpoint to your environment variables.
    var subscription_key string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
    if "" == os.Getenv("BING_AUTOSUGGEST_ENDPOINT") {
        log.Fatal("Please set/export the environment variable BING_AUTOSUGGEST_ENDPOINT.")
    }
    var endpoint string = os.Getenv("BING_AUTOSUGGEST_ENDPOINT")
}
```

## <a name="code-examples"></a>コード例

これらのコード サンプルは、Go 用 Bing Autosuggest クライアント ライブラリを使用して基本的なタスクを実行する方法を示しています。

* [クライアントを認証する](#authenticate-the-client)
* [API 要求を送信する](#send-an-api-request)

### <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイックスタートでは、`BING_AUTOSUGGEST_SUBSCRIPTION_KEY` という名前の Bing Autosuggest キー用と `BING_AUTOSUGGEST_ENDPOINT` という名前のエンドポイント用の[環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)してあることを前提としています。

`main()` 関数で、実際のエンドポイントとキーを使用してクライアントをインスタンス化します。

```go
// Get the context, which is required by the SDK methods.
ctx := context.Background()

client := autosuggest.New()
// Set the subscription key on the client.
client.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscription_key)
client.Endpoint = endpoint
```

### <a name="send-an-api-request"></a>API 要求を送信する

同じメソッド内でクライアントの [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) メソッドを使用して、クエリを Bing に送信します。 次に、[Suggestions](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions) 応答を反復処理し、最初の候補を出力します。

```Go
// This should return the query suggestion "xbox."
result, err := client.AutoSuggest (ctx, "xb", "", "", "", "", "", "", "", "", "", "", []autosuggest.ResponseFormat{"Json"})
if nil != err {
    log.Fatal(err)
}

groups := *result.SuggestionGroups
if len(groups) > 0 {
    group, _ := groups[0].AsSuggestionsSuggestionGroup()
    fmt.Printf ("First suggestion group: %s\n", (*group).Name)
    suggestions := *(*group).SearchSuggestions
    if len(suggestions) > 0 {
        fmt.Println("First suggestion:")
        fmt.Printf("Query: %s\n", *suggestions[0].Query)
        fmt.Printf("Display text: %s\n", *suggestions[0].DisplayText)
    } else {
        fmt.Println("No suggestions found in this group.")
    }
} else {
    fmt.Println("No suggestions found.")
}
```

## <a name="run-the-application"></a>アプリケーションの実行

自分のアプリケーション ディレクトリから `go run [arguments]` コマンドを使用して、Go アプリケーションを実行します。

```Go
go run sample-app.go
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [Azure portal でリソース グループを削除する](../../../cognitive-services-apis-create-account.md#clean-up-resources)。
* [Azure CLI でリソース グループを削除する](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Bing Autosuggest チュートリアル](../../tutorials/autosuggest.md)

## <a name="see-also"></a>関連項目

- [Bing Autosuggest とは](../../get-suggested-search-terms.md)
- [Bing Autosuggest API v7 リファレンス](/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
