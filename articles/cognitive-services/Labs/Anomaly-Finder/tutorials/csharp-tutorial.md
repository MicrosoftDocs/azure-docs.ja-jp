---
title: 'チュートリアル: 異常検出、C#'
titlesuffix: Azure Cognitive Services
description: 異常検出 API を使用する C# アプリについて説明します。 API に元のデータ ポイントを送信し、予期される値と異常なポイントを取得します。
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 64183ea09203ea965bb889fe4741fdaf4dbd1e02
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228017"
---
# <a name="tutorial-anomaly-detection-with-c-application"></a>チュートリアル: C# アプリケーションによる異常検出

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

異常検出 API を使用して入力から異常を検出する基本的な Windows アプリケーションについて説明します。 例では、サブスクリプション キーを使用して、Anomaly Detection API に時系列データを送信し、その後 API から、データ ポイントごとの異常ポイントと予期された値をすべて取得します。

## <a name="prerequisites"></a>前提条件

### <a name="platform-requirements"></a>プラットフォームの要件

ここで紹介するサンプルは、[Visual Studio 2017、Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs) を使用して、.NET Framework 向けに開発されたものです。 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>異常検出にサブスクライブしてサブスクリプション キーを取得する 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>サンプルの取得と利用

異常検出のサンプル アプリケーションは [GitHub](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git) にあります。これを、お使いのコンピューターに複製します。 
<a name="Step1"></a>
### <a name="install-the-example"></a>サンプルをインストールする

GitHub Desktop で Sample\AnomalyDetectionSample.sln を開きます。

<a name="Step2"></a>
### <a name="build-the-example"></a>サンプルをビルドする

Ctrl キーと Shift キーを押しながら B キーを押すか、リボン メニューで [ビルド] をクリックしてから [ソリューションのビルド] を選択します。

<a name="Step3"></a>
### <a name="run-the-example"></a>例を実行する

1. ビルドが完了したら、**F5** キーを押すか、リボン メニューの **[開始]** をクリックしてサンプルを実行します。
2. "{your_subscription_key}" の文字が表示されているテキスト ボックスが置かれた異常検出ユーザー インターフェイス ウィンドウを探します。
3. サンプル データの入ったファイル request.json を独自のデータに置き換えて、[送信] ボタンをクリックします。 アップロードしたデータは Microsoft が受信のうえ、その中にある異常なポイントを検出します。 ロードしたデータが Microsoft のサーバーに保持されることはありません。 異常なポイントをもう一度検出する場合には、同じデータをもう一度アップロードする必要があります。
4. データに問題がなければ、[応答] フィールドに異常検出の結果が表示されます。 エラーが発生した場合には、[応答] フィールドにエラーに関する情報も表示されます。

<a name="Review"></a>
### <a name="read-the-result"></a>結果を読み取る

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>確認し学習する

アプリケーションを実行できたので、サンプル アプリが Cognitive Services テクノロジとどのように統合しているかを見てみましょう。 そうすることによって、このアプリを基にした開発作業や、Microsoft の異常検出を使用した独自のアプリの開発が簡単にできるようになります。

このサンプル アプリでは、異常検出 RESTful API エンドポイントを使用しています。

この RESTful API がサンプル アプリケーションでどのように使用されているかを確認するときは、**AnomalyDetectionClient.cs** のコード スニペットを見てみましょう。 このファイルには、"KEY SAMPLE CODE STARTS HERE" (主要なサンプル コードはここから開始) と "KEY SAMPLE CODE ENDS HERE" (主要なサンプル コードはここで終了) というコード コメントが含まれていて、下に再掲したコード スニペットを探すのに役立ちます。

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
### <a name="request"></a>**要求**
以下のコード スニペットは、HttpClient を使って異常検出 API のエンドポイントにサブスクリプション キーとデータ ポイントを送信する方法を示したものです。

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
            if (res.IsSuccessStatusCode)
            {
                return await res.Content.ReadAsStringAsync();
            }
            else
            {
                return $"ErrorCode: {res.StatusCode}";
            }
        }
    }
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [REST API リファレンス](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
