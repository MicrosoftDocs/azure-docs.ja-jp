---
title: クイック スタート:Ink Recognizer REST API および C# を使用したデジタル インクの認識
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Ink Recognizer API と C# を使用して、デジタル インク ストロークの認識を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: a4bb02f11c7a9a75ddc96e0ee8e9f4b868f8ade5
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051595"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-c"></a>クイック スタート:Ink Recognizer REST API および C# を使用したデジタル インクの認識

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

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

- このクイックスタートのインク ストローク データのサンプルは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-ink-strokes.json) にあります。

### <a name="create-an-ink-recognizer-resource"></a>Ink Recognizer リソースを作成する

[!INCLUDE [creating-an-ink-recognizer-resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. Visual Studio で新しいコンソール ソリューションを作成し、次のパッケージを追加します。 
    
    [!code-csharp[import statements](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=imports)]

2. サブスクリプション キー、エンドポイント、およびサンプル JSON ファイル用の変数を作成します。 エンドポイントは、API にアクセスするために、後で `inkRecognitionUrl` と結合されます。 

    [!code-csharp[endpoint file and key variables](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>要求を送信する関数を作成する

1. 上記で作成した変数を取る `Request` という新しい非同期関数を作成します。

2. `HttpClient`オブジェクトを使用して、クライアントのセキュリティ プロトコルとヘッダー情報を設定します。 `Ocp-Apim-Subscription-Key` ヘッダーには必ずサブスクリプション キーを追加してください。 その後、要求の `StringContent` オブジェクトを作成します。
 
3. `PutAsync()` を使用して要求を送信します。 要求が成功した場合は、応答を返します。  
    
    [!code-csharp[request example method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=request)]

## <a name="send-an-ink-recognition-request"></a>インク認識要求を送信する

1. `recognizeInk()` という新しい関数を作成します。 要求を構成し、エンドポイント、サブスクリプション キー、API の URL、デジタル インク ストローク データを指定して `Request()` 関数を呼び出すことで送信します。

2. JSON オブジェクトを逆シリアル化し、それをコンソールに書き込みます。 
    
    [!code-csharp[request to recognize ink data](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=recognize)]

## <a name="load-your-digital-ink-data"></a>デジタル インク データを読み込む

`LoadJson()` という関数を作成して、インク データの JSON ファイルを読み込みます。 `StreamReader` および `JsonTextReader` を使用して `JObject` を作成し、それを返します。

[!code-csharp[load the JSON file](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=loadJson)]

## <a name="send-the-api-request"></a>API 要求を送信する

1. アプリケーションの main メソッドで、上記で作成した関数を使用して JSON データを読み込みます。 

2. 上記で作成した `recognizeInk()` 関数を呼び出します。 `System.Console.ReadKey()` を使用して、アプリケーションの実行後にコンソール ウィンドウを開いたままにします。
    
    [!code-csharp[file main method](~/cognitive-services-rest-samples/dotnet/Vision/InkRecognition/quickstart/recognizeInk.cs?name=main)]


## <a name="run-the-application-and-view-the-response"></a>アプリケーションを実行し、応答を表示する

アプリケーションを実行します。 成功応答が JSON 形式で返されます。 JSON 応答は [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/dotnet/Vision/InkRecognition/quickstart/example-response.json) でも確認できます。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2089907)


デジタル インキング アプリで Ink Recognition API がどのように動作するかを確認するには、GitHub 上の次のサンプル アプリケーションを参照してください。
* [C# およびユニバーサル Windows プラットフォーム (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# および Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript Web ブラウザー アプリ](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java および Android モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift および iOS モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089805)
