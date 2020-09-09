---
title: クイック スタート:Ink Recognizer REST API および Java を使用したデジタル インクの認識
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Ink Recognizer API と Java を使用して、デジタル インク ストロークの認識を開始します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: aahi
ms.custom: devx-track-java
ms.openlocfilehash: b73f5013fdbef34344ece672bacff90db9bac60b
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89051578"
---
# <a name="quickstart-recognize-digital-ink-with-the-ink-recognizer-rest-api-and-java"></a>クイック スタート:Ink Recognizer REST API および Java を使用したデジタル インクの認識

[!INCLUDE [ink-recognizer-deprecation](../includes/deprecation-note.md)]

このクイックスタートは、デジタル インク ストロークで Ink Recognizer API の使用を始めるときに使用します。 この Java アプリケーションは、JSON 形式のインク ストローク データを含む API 要求を送信し、応答を取得します。

このアプリケーションは Java で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

通常、この API はデジタル インキング アプリから呼び出します。 このクイックスタートでは、JSON ファイルから次の手書きサンプルのインク ストローク データを送信します。

![手書きテキストのイメージ](../media/handwriting-sample.jpg)

このクイック スタートのソース コードは、[GitHub](https://go.microsoft.com/fwlink/?linkid=2089904) にあります。

## <a name="prerequisites"></a>前提条件

- [Java&trade; Development Kit (JDK) 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 以降。

- Maven リポジトリから以下のライブラリをインポートします
    - [JSON in Java](https://mvnrepository.com/artifact/org.json/json) パッケージ
    - [Apache HttpClient](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) パッケージ

- このクイックスタートのインク ストローク データのサンプルは、[GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-ink-strokes.json) にあります。

### <a name="create-an-ink-recognizer-resource"></a>Ink Recognizer リソースを作成する

[!INCLUDE [creating an ink recognizer resource](../includes/setup-instructions.md)]

## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. 普段使用している IDE またはエディターで新しい Java プロジェクトを作成し、以下のライブラリをインポートします。
    
    [!code-java[import statements](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=imports)]

2. サブスクリプション キー、エンドポイント、および JSON ファイル用の変数を作成します。 エンドポイントは、後で Ink Recognizer URI に追加されます。

    [!code-java[initial vars](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=vars)]

## <a name="create-a-function-to-send-requests"></a>要求を送信する関数を作成する

1. 上記で作成した変数を取る `sendRequest()` という新しい関数を作成します。 次に、以下の手順のようにします。

2. API に要求を送信できる `CloseableHttpClient` オブジェクトを作成します。 エンドポイントと Ink Recognizer URL を組み合わせて、`HttpPut` 要求オブジェクトに要求を送信します。

3. 要求の `setHeader()` 関数を使用して、`Content-Type` ヘッダーを `application/json` に設定し、サブスクリプション キーを `Ocp-Apim-Subscription-Key` ヘッダーに追加します。

4. 送信するデータに対して要求の `setEntity()` 関数を使用します。   

5. クライアントの `execute()` 関数を使用して、要求を送信し、それを `CloseableHttpResponse` オブジェクトに格納します。 

6. `HttpEntity` オブジェクトを作成して、応答の内容を格納します。 `getEntity()` で内容を取得します。 応答が空でない場合は、それを返します。
    
    [!code-java[send a request](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=sendRequest)]

## <a name="send-an-ink-recognition-request"></a>インク認識要求を送信する

`recognizeInk()` というメソッドを作成し、インク ストローク データを認識します。 自身のエンドポイント、URL、サブスクリプション キー、および JSON データを使用して、上記で作成した `sendRequest()` メソッドを呼び出します。 結果を取得し、それをコンソールに出力します。

[!code-java[recognizeInk](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=recognizeInk)]

## <a name="load-your-digital-ink-data-and-send-the-request"></a>デジタル インク データを読み込み、要求を送信する

1. アプリケーションの main メソッドで、要求に追加されるデータを含む JSON ファイルを読み取ります。

2. 上記で作成したインク認識関数を呼び出します。
    
    [!code-java[main method](~/cognitive-services-rest-samples/java/InkRecognition/quickstart/RecognizeInk.java?name=main)]


## <a name="run-the-application-and-view-the-response"></a>アプリケーションを実行し、応答を表示する

アプリケーションを実行します。 成功応答が JSON 形式で返されます。 JSON 応答は [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/InkRecognition/quickstart/example-response.json) でも確認できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [REST API リファレンス](https://go.microsoft.com/fwlink/?linkid=2089907)


デジタル インキング アプリで Ink Recognition API がどのように動作するかを確認するには、GitHub 上の次のサンプル アプリケーションを参照してください。
* [C# およびユニバーサル Windows プラットフォーム (UWP)](https://go.microsoft.com/fwlink/?linkid=2089803)  
* [C# および Windows Presentation Foundation (WPF)](https://go.microsoft.com/fwlink/?linkid=2089804)
* [JavaScript Web ブラウザー アプリ](https://go.microsoft.com/fwlink/?linkid=2089908)       
* [Java および Android モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089906)
* [Swift および iOS モバイル アプリ](https://go.microsoft.com/fwlink/?linkid=2089805)
