---
title: 'クイック スタート: テキストを音声に変換する (.NET Core) - Speech Services'
titleSuffix: Azure Cognitive Services
description: このクイック スタートでは、Text to Speech REST API を使用してテキストを音声に変換する方法について説明します。 このガイドに含まれているサンプル テキストは、音声合成マークアップ言語 (SSML) として構成されています。 そのため、音声応答の音声と言語を選択することができます。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: erhopf
ms.openlocfilehash: 5ae63b1738824095073ac6b9e1071f6b4a3e5ae1
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518849"
---
# <a name="quickstart-convert-text-to-speech-using-net-core"></a>クイック スタート: .NET Core を使用してテキストを音声に変換する

このクイック スタートでは、.NET Core と Text to Speech REST API を使用してテキストを音声に変換する方法について説明します。 このガイドに含まれているサンプル テキストは、[音声合成マークアップ言語 (SSML)](speech-synthesis-markup.md) として構成されており、応答の音声と言語を選択することができます。

このクイック スタートでは、[Azure Cognitive Services アカウント](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)と Speech Services リソースが必要になります。 アカウントを持っていない場合は、[無料試用版](https://azure.microsoft.com/try/cognitive-services/)を使用してサブスクリプション キーを取得できます。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* [.NET コア SDK](https://dotnet.microsoft.com/download)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)、または任意のテキスト エディター
* Speech Service の Azure サブスクリプション キー

## <a name="create-a-net-core-project"></a>.NET Core プロジェクトを作成する

新しいコマンド プロンプト (またはターミナル セッション) を開き、次のコマンドを実行します。

```console
dotnet new console -o tts-sample
cd tts-sample
```

最初のコマンドでは、2 つのことを行っています。 新しい .NET コンソール アプリケーションを作成し、`tts-sample` という名前のディレクトリを作成します。 2 つ目のコマンドでは、目的のプロジェクトのディレクトリに変更しています。

## <a name="select-the-c-language-version"></a>C# 言語のバージョンを選択する

このクイック スタートでは、C# 7.1 以降が必要です。 プロジェクトに使用する C# のバージョンは、いくつかの方法で変更できます。 このガイドでは、`tts-sample.csproj` ファイルを調整する方法について説明します。 Visual Studio で言語を変更するなど、選択可能なすべての方法については、「[C# 言語のバージョンの選択](https://docs.microsoft.com/dotnet/csharp/language-reference/configure-language-version)」を参照してください。

対象のプロジェクトを開いて、`tts-sample.csproj` を開きます。 `LangVersion` は必ず 7.1 以降に設定してください。 該当する言語バージョンのプロパティ グループが存在しない場合は、次の行を追加します。

```csharp
<PropertyGroup>
   <LangVersion>7.1</LangVersion>
</PropertyGroup>
```

必ず変更内容を保存してください。

## <a name="add-required-namespaces-to-your-project"></a>必要な名前空間をプロジェクトに追加する

先ほど実行した `dotnet new console` コマンドによって、プロジェクトが作成されています。これには `Program.cs` が含まれています。 このファイルに、アプリケーション コードを記述することになります。 `Program.cs` を開いて、既存の using ステートメントを置き換えます。 これらのステートメントによって、サンプル アプリのビルドと実行に必要なすべての型に確実にアクセスすることができます。

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.IO;
using System.Threading.Tasks;
```

## <a name="create-a-class-for-token-exchange"></a>トークン交換のためのクラスを作成する

Text to Speech REST API は、認証のためのアクセス トークンを必要とします。 アクセス トークンを取得するためには、交換が必要です。 このサンプルでは、`issueToken` エンドポイントを使用して、ご利用の Speech Services のサブスクリプション キーをアクセス トークンと交換します。

このサンプルでは、ご利用の Speech Services のサブスクリプションが米国西部リージョンにあることを想定しています。 別のリージョンを使用している場合は、`FetchTokenUri` の値を更新してください。 完全な一覧については、[リージョン](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#rest-apis)に関するセクションを参照してください。

```csharp
public class Authentication
{
    private string subscriptionKey;
    private string tokenFetchUri;

    public Authentication(string tokenFetchUri, string subscriptionKey)
    {
        if (string.IsNullOrWhiteSpace(tokenFetchUri))
        {
            throw new ArgumentNullException(nameof(tokenFetchUri));
        }
        if (string.IsNullOrWhiteSpace(subscriptionKey))
        {
            throw new ArgumentNullException(nameof(subscriptionKey));
        }
        this.tokenFetchUri = tokenFetchUri;
        this.subscriptionKey = subscriptionKey;
    }

    public async Task<string> FetchTokenAsync()
    {
        using (var client = new HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            UriBuilder uriBuilder = new UriBuilder(this.tokenFetchUri);

            var result = await client.PostAsync(uriBuilder.Uri.AbsoluteUri, null).ConfigureAwait(false);
            return await result.Content.ReadAsStringAsync().ConfigureAwait(false);
        }
    }
}
```

> [!NOTE]
> 認証の詳細については、[アクセス トークンによる認証](https://docs.microsoft.com/azure/cognitive-services/authentication#authenticate-with-an-authentication-token)に関するセクションを参照してください。

## <a name="get-an-access-token-and-set-the-host-url"></a>アクセス トークンを取得してホストの URL を設定する

`static void Main(string[] args)` を探して `static async Task Main(string[] args)` に置き換えます。

次に、このコードを main メソッドにコピーします。 いくつかの処理を行っていますが、最も重要なのは、テキストを入力として受け取って、`Authentication` 関数を呼び出し、サブスクリプション キーをアクセス トークンと交換する部分です。 なんらかの問題が発生した場合、コンソールにエラーが出力されます。

アプリを実行する前に、必ずサブスクリプション キーを追加してください。

```csharp
// Prompts the user to input text for TTS conversion
Console.Write("What would you like to convert to speech? ");
string text = Console.ReadLine();

// Gets an access token
string accessToken;
Console.WriteLine("Attempting token exchange. Please wait...\n");

// Add your subscription key here
// If your resource isn't in WEST US, change the endpoint
Authentication auth = new Authentication("https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken", "YOUR_SUBSCRIPTION_KEY");
try
{
    accessToken = await auth.FetchTokenAsync().ConfigureAwait(false);
    Console.WriteLine("Successfully obtained an access token. \n");
}
catch (Exception ex)
{
    Console.WriteLine("Failed to obtain an access token.");
    Console.WriteLine(ex.ToString());
    Console.WriteLine(ex.Message);
    return;
}
```

次に、テキスト読み上げのホストとルートを設定します。

```csharp
string host = "https://westus.tts.speech.microsoft.com/cognitiveservices/v1";
```

## <a name="build-the-ssml-request"></a>SSML 要求を構築する

テキストは、`POST` 要求の本文として送信されます。 SSML では、音声と言語を指定することができます。 このクイック スタートでは、言語を `en-US` に設定し、音声を `ZiraRUS` として設定した SSML を使用します。 要求の SSML を構築してみましょう。

```csharp
string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)'>" +
              text + "</voice></speak>";
```

> [!NOTE]
> このサンプルでは、`ZiraRUS` の音声フォントを使用しています。 Microsoft から提供されている音声/言語の全一覧については、[言語のサポート](language-support.md)に関するページを参照してください。 認識できる独自の音声を自社ブランド用に作成することを検討している場合は、「[カスタム音声フォントの作成](how-to-customize-voice-font.md)」を参照してください。

## <a name="instantiate-the-client-make-a-request-and-save-synthesized-audio-to-a-file"></a>クライアントをインスタンス化して要求を行い、合成された音声をファイルに保存する

このコード サンプルでは、多くのことを実行しています。 何が行われているのかを簡単に見ていきましょう。

* クライアントと要求がインスタンス化されます。
* HTTP メソッドが `POST` として設定されます。
* 必要なヘッダーが要求に追加されます。
* 要求が送信されて状態コードがチェックされます。
* 応答が非同期的に読み取られ、sample.wav という名前のファイルに書き込まれます。

このコードをプロジェクトにコピーします。 `User-Agent` ヘッダーの値は必ず、Azure portal から得られるリソースの名前に置き換えてください。

```csharp
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Set the HTTP method
        request.Method = HttpMethod.Post;
        // Construct the URI
        request.RequestUri = new Uri(host);
        // Set the content type header
        request.Content = new StringContent(body, Encoding.UTF8, "application/ssml+xml");
        // Set additional header, such as Authorization and User-Agent
        request.Headers.Add("Authorization", "Bearer " + accessToken);
        request.Headers.Add("Connection", "Keep-Alive");
        // Update your resource name
        request.Headers.Add("User-Agent", "YOUR_RESOURCE_NAME");
        request.Headers.Add("X-Microsoft-OutputFormat", "riff-24khz-16bit-mono-pcm");
        // Create a request
        Console.WriteLine("Calling the TTS service. Please wait... \n");
        using (var response = await client.SendAsync(request).ConfigureAwait(false))
        {
            response.EnsureSuccessStatusCode();
            // Asynchronously read the response
            using (var dataStream = await response.Content.ReadAsStreamAsync().ConfigureAwait(false))
            {
                Console.WriteLine("Your speech file is being written to file...");
                using (var fileStream = new FileStream(@"sample.wav", FileMode.Create, FileAccess.Write, FileShare.Write))
                {
                    await dataStream.CopyToAsync(fileStream).ConfigureAwait(false);
                    fileStream.Close();
                }
                Console.WriteLine("\nYour file is ready. Press any key to exit.");
                Console.ReadLine();
            }
        }
    }
}
```

## <a name="run-the-sample-app"></a>サンプル アプリを実行する

以上で、テキスト読み上げアプリを実行する準備が整いました。 コマンド ライン (またはターミナル セッション) でプロジェクト ディレクトリに移動して、次のコマンドを実行します。

```console
dotnet run
```

成功した場合、プロジェクト フォルダーに音声ファイルが保存されます。 任意のメディア プレーヤーを使用して再生してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

サブスクリプション キーをプログラムにハードコーディングしている場合は、このクイック スタートを終了するときにサブスクリプション キーを必ず削除してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [GitHub で .NET のサンプルを詳しく見てみる](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/Samples-Http/NETCore)

## <a name="see-also"></a>関連項目

* [Text-to-speech API リファレンス](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)
* [カスタム音声フォントの作成](how-to-customize-voice-font.md)
* [カスタム音声を作成するための音声サンプルを録音する](record-custom-voice-samples.md)
