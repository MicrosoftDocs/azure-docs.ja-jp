---
title: Bing Autosuggest C# クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2020
ms.author: aahi
ms.openlocfilehash: f3599a804549a19c3a633cfacf1a97b5ee232a32
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947129"
---
.NET 用 Bing Autosuggest クライアント ライブラリの使用を開始します。 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

.NET 用 Bing Autosuggest クライアント ライブラリを使用して、部分クエリ文字列に基づいて検索候補を取得します。

[リファレンスのドキュメント](/dotnet/api/overview/azure/cognitiveservices/bing-autosuggest-readme) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingAutoSuggest) | [パッケージ (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.AutoSuggest/) | [サンプル コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/BingAutoSuggest/Program.cs)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[無料で作成](https://azure.microsoft.com/free/cognitive-services)できます。
* 最新バージョンの [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

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
setx AUTOSUGGEST_SUBSCRIPTION_KEY <replace-with-your-autosuggest-api-key>
setx AUTOSUGGEST_ENDPOINT <replace-with-your-autosuggest-api-endpoint>
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

## <a name="create-a-new-c-application"></a>新しい C# アプリケーションを作成する

好みのエディターまたは IDE で、新しい .NET Core アプリケーションを作成します。

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、`dotnet new` コマンドを使用し、`bing-autosuggest-quickstart` という名前で新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイル (*program.cs*) を使用する単純な "Hello World" C# プロジェクトが作成されます。

```console
dotnet new console -n bing-autosuggest-quickstart
```

新しく作成されたアプリ フォルダーにディレクトリを変更します。 次を使用してアプリケーションをビルドできます。

```console
dotnet build
```

ビルドの出力に警告やエラーが含まれないようにする必要があります。

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

プロジェクト ディレクトリから、好みのエディターまたは IDE で *program.cs* ファイルを開きます。 次の `using` ディレクティブを追加します。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
```

`Program` クラスに、リソースの Azure エンドポイントおよびキー用の変数を作成します。 アプリケーションの起動後に環境変数を作成した場合、その変数にアクセスするには、アプリケーションを実行しているエディター、IDE、またはシェルを閉じて、もう一度開く必要があります。

```csharp
private const string key_var = "AUTOSUGGEST_SUBSCRIPTION_KEY";
private static readonly string subscription_key = Environment.GetEnvironmentVariable(key_var);

// Note you must use the same region as you used to get your subscription key.
private const string endpoint_var = "AUTOSUGGEST_ENDPOINT";
private static readonly string endpoint = Environment.GetEnvironmentVariable(endpoint_var);
```

アプリケーションの `Main` メソッドに、次のメソッド呼び出しを追加します。その定義は後で記述します。

```csharp
static void Main(string[] args)
{
    Task.WaitAll(RunQuickstart());
    Console.WriteLine("Press any key to exit.");
    Console.Read();
}
```

## <a name="install-the-client-library"></a>クライアント ライブラリをインストールする

次のコマンドを使用して、アプリケーション ディレクトリ内に .NET 用 Bing Autosuggest クライアント ライブラリをインストールします。

```console
dotnet add package Microsoft.Azure.CognitiveServices.Search.AutoSuggest --version 2.0.0
```

Visual Studio IDE を使用している場合、クライアント ライブラリは、ダウンロード可能な NuGet パッケージとして入手できます。

## <a name="code-examples"></a>コード例

以下のコード スニペットは、.NET 用 Bing Autosuggest クライアント ライブラリを使用して次のタスクを実行する方法を示します。

* [クライアントを認証する](#authenticate-the-client)
* [自動提案要求を送信する](#send-an-autosuggest-request)

### <a name="authenticate-the-client"></a>クライアントを認証する

> [!NOTE]
> このクイックスタートでは、`AUTOSUGGEST_SUBSCRIPTION_KEY` という名前の Bing Autosuggest キー用と `AUTOSUGGEST_ENDPOINT` という名前のエンドポイント用の[環境変数を作成](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication)してあることを前提としています。


新しい非同期メソッドで、実際のエンドポイントとキーを使用してクライアントをインスタンス化します。 自分のキーを指定して [ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.apikeyserviceclientcredentials) オブジェクトを作成し、それを自分のエンドポイントと共に使用して [AutosuggestClient](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclient) オブジェクトを作成します。

```csharp
async static Task RunQuickstart()
{
    // Generate the credentials and create the client.
    var credentials = new Microsoft.Azure.CognitiveServices.Search.AutoSuggest.ApiKeyServiceClientCredentials(subscription_key);
    var client = new AutoSuggestClient(credentials, new System.Net.Http.DelegatingHandler[] { })
    {
        Endpoint = endpoint
    };
}
```

### <a name="send-an-autosuggest-request"></a>自動提案要求を送信する

同じメソッド内でクライアントの [AutoSuggestMethodAsync](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.autosuggestclientextensions.autosuggestmethodasync#Microsoft_Azure_CognitiveServices_Search_AutoSuggest_AutoSuggestClientExtensions_AutoSuggestMethodAsync_Microsoft_Azure_CognitiveServices_Search_AutoSuggest_IAutoSuggestClient_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_String_System_Collections_Generic_IList_System_String__System_Threading_CancellationToken_) メソッドを使用して、クエリを Bing に送信します。 次に、[Suggestions](/dotnet/api/microsoft.azure.cognitiveservices.search.autosuggest.models.suggestions) 応答を反復処理し、最初の候補を出力します。

```csharp
var result = await client.AutoSuggestMethodAsync("xb");
var groups = result.SuggestionGroups;
if (groups.Count > 0) {
    var group = groups[0];
    Console.Write("First suggestion group: {0}\n", group.Name);
    var suggestions = group.SearchSuggestions;
    if (suggestions.Count > 0)
    {
        Console.WriteLine("First suggestion:");
        Console.WriteLine("Query: {0}", suggestions[0].Query);
        Console.WriteLine("Display text: {0}", suggestions[0].DisplayText);
    }
    else
    {
        Console.WriteLine("No suggestions found in this group.");
    }
}
else
{
    Console.WriteLine("No suggestions found.");
}
```

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーション ディレクトリから `dotnet run` コマンドを使用してアプリケーションを実行します。

```dotnet
dotnet run
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
- [Bing Autosuggest dotnet リファレンス](/dotnet/api/overview/azure/cognitiveservices/bing-autosuggest-readme)
