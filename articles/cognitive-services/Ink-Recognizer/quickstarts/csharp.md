---
title: クイック スタート:Ink Recognizer REST API および C# を使用したデジタル インクの認識
titleSuffix: Azure Cognitive Services
description: Ink Recognizer API を使用して、デジタル インク ストロークの認識を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 05/02/2019
ms.author: aahi
ms.openlocfilehash: 6a1b4ab43a7d87ac1162a7f0a3556d6bc3bfbfab
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721249"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>クイック スタート:Ink Recognizer REST API および C# を使用したデジタル インクの認識

このクイックスタートを使用して、Ink Recognizer API へのデジタル インク ストロークの送信を開始します。 この C# アプリケーションは、JSON 形式のインク ストローク データを含む API 要求を送信し、応答を取得します。

このアプリケーションは C# で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

通常、この API はデジタル インキング アプリから呼び出します。 このクイックスタートでは、JSON ファイルから次の手書きサンプルのインク ストローク データを送信します。

![手書きテキストのイメージ](../media/handwriting-sample.jpg)

このクイック スタートのソース コードは、[GitHub](https://go.microsoft.com/fwlink/?linkid=2089502) にあります。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) の任意のエディション。
- [Newtonsoft.Json](https://www.newtonsoft.com/json)
    - Visual Studio に Newtonsoft.Json を NuGet パッケージとしてインストールするには、次の手順に従います。
        1. **Solution Manager** を右クリックします
        2. **[NuGet パッケージの管理]**
        3. `Newtonsoft.Json` を探して、パッケージをインストールします
- Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。

- このクイックスタートのインク ストローク データのサンプルは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-ink-strokes.json) にあります。

[!INCLUDE [cognitive-services-ink-recognizer-signup-requirements](../../../../includes/cognitive-services-ink-recognizer-signup-requirements.md)]


## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. Visual Studio で新しいコンソール ソリューションを作成し、次のパッケージを追加します。 

    ```csharp
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    ```

2. サブスクリプション キーとエンドポイントの変数を作成します。 インク認識に使用できる URI を以下に示します。 これは、API 要求 URl を作成するために、後からサービス エンドポイントに付加されます。

    ```csharp
    // Replace the subscriptionKey string with your valid subscription key.
    const string subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Replace the dataPath string with a path to the JSON formatted ink stroke data.
    const string dataPath = @"PATH-TO-INK-STROKE-DATA"; 

    // URI information for ink recognition:
    const string endpoint = "https://api.cognitive.microsoft.com";
    const string inkRecognitionUrl = "/inkrecognizer/v1.0-preview/recognize";
    ```

## <a name="create-a-function-to-send-requests"></a>要求を送信する関数を作成する

1. 上記で作成した変数を取る `Request` という新しい非同期関数を作成します。

2. `HttpClient`オブジェクトを使用して、クライアントのセキュリティ プロトコルとヘッダー情報を設定します。 `Ocp-Apim-Subscription-Key` ヘッダーには必ずサブスクリプション キーを追加してください。 その後、要求の `StringContent` オブジェクトを作成します。
 
3. `PutAsync()` を使用して要求を送信します。 要求が成功した場合は、応答を返します。  
    
    ```csharp
    static async Task<string> Request(string apiAddress, string endpoint, string subscriptionKey, string requestData){
        
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(apiAddress) }){
            System.Net.ServicePointManager.SecurityProtocol = SecurityProtocolType.Tls12 | SecurityProtocolType.Tls11 | SecurityProtocolType.Tls;
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            var res = await client.PutAsync(endpoint, content);
            if (res.IsSuccessStatusCode){
                return await res.Content.ReadAsStringAsync();
            }
            else{
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
    ```

## <a name="send-an-ink-recognition-request"></a>インク認識要求を送信する

1. `recognizeInk()` という新しい関数を作成します。 要求を構成し、エンドポイント、サブスクリプション キー、API の URL、デジタル インク ストローク データを指定して `Request()` 関数を呼び出すことで送信します。

2. JSON オブジェクトを逆シリアル化し、それをコンソールに書き込みます。 
    
    ```csharp
    static void recognizeInk(string requestData){

        //construct the request
        var result = Request(
            endpoint,
            inkRecognitionUrl,
            subscriptionKey,
            requestData).Result;

        dynamic jsonObj = Newtonsoft.Json.JsonConvert.DeserializeObject(result);
        System.Console.WriteLine(jsonObj);
    }
    ```

## <a name="load-your-digital-ink-data"></a>デジタル インク データを読み込む

`LoadJson()` という関数を作成して、インク データの JSON ファイルを読み込みます。 `StreamReader` および `JsonTextReader` を使用して `JObject` を作成し、それを返します。
    
```csharp
public static JObject LoadJson(string fileLocation){

    var jsonObj = new JObject();

    using (StreamReader file = File.OpenText(fileLocation))
    using (JsonTextReader reader = new JsonTextReader(file)){
        jsonObj = (JObject)JToken.ReadFrom(reader);
    }
    return jsonObj;
}
```

## <a name="send-the-api-request"></a>API 要求を送信する

1. アプリケーションの main メソッドで、上記で作成した関数を使用して JSON データを読み込みます。 

2. 上記で作成した `recognizeInk()` 関数を呼び出します。 `System.Console.ReadKey()` を使用して、アプリケーションの実行後にコンソール ウィンドウを開いたままにします。
    
    ```csharp
    static void Main(string[] args){

        var requestData = LoadJson(dataPath);
        string requestString = requestData.ToString(Newtonsoft.Json.Formatting.None);
        recognizeInk(requestString);
        System.Console.WriteLine("\nPress any key to exit ");
        System.Console.ReadKey();
        }
    ```

## <a name="run-the-application-and-view-the-response"></a>アプリケーションを実行し、応答を表示する

アプリケーションを実行します。 成功応答が JSON 形式で返されます。 JSON 応答は [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/InkRecognition/quickstart/example-response.json) でも確認できます。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2089907)


デジタル インキング アプリで Ink Recognition API がどのように動作するかを確認するには、GitHub 上の次のサンプル アプリケーションを参照してください。
* [C# およびユニバーサル Windows プラットフォーム (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# および Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript Web ブラウザー アプリ](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java および Android モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift および iOS モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089805)
