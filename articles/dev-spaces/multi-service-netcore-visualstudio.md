---
title: .NET Core と Visual Studio を使用した複数の依存サービスの実行
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.custom: vs-azure
ms.workload: azure-vs
author: zr-msft
ms.author: zarhoads
ms.date: 07/09/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s '
ms.openlocfilehash: 66a08ad674477da478ec7037833fe4cb836f9bb0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59357057"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したマルチサービスの開発

このチュートリアルでは、Azure Dev Spaces を使用してマルチサービス アプリケーションを開発する方法と、Dev Spaces が提供するいくつかの追加の利点について学習します。

## <a name="call-another-container"></a>別のコンテナーを呼び出す
このセクションでは、2 つ目のサービス (`mywebapi`) を作成し、`webfrontend` でそのサービスを呼び出します。 各サービスは別々のコンテナーで実行されます。 その後、両方のコンテナーでデバッグします。

![](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>*mywebapi* のサンプル コードをダウンロードする
時間を節約するために、GitHub リポジトリからサンプル コードをダウンロードしましょう。 https://github.com/Azure/dev-spaces に移動し、**[Clone or download]** をクリックして GitHub リポジトリをダウンロードします。 このセクションのコードは、`samples/dotnetcore/getting-started/mywebapi` にあります。

### <a name="run-mywebapi"></a>*mywebapi* を実行する
1. "*別の Visual Studio ウィンドウ*" で `mywebapi` プロジェクトを開きます。
1. 前に `webfrontend` プロジェクトで行ったように、起動設定ドロップダウンから **[Azure Dev Spaces]** を選択します。 今回は新しい AKS クラスターを作成するのではなく、作成済みのものを選択します。 前述のように、[スペース] は既定の `default` のままにし、**[OK]** をクリックします。 [出力] ウィンドウを見ると、デバッグを開始したときの作業速度を上げるために、Visual Studio によって開発空間でこの新しいサービスの "ウォーム アップ" が開始されたことがわかります。
1. F5 キーを押し、サービスがビルドされ、展開されるまで待ちます。 準備ができると、Visual Studio のステータス バーがオレンジ色に変わります。
1. **[出力]** ウィンドウの **[Azure Dev Spaces for AKS]** ウィンドウに表示されるエンドポイント URL を書き留めます。 これは、`http://localhost:<portnumber>` のように表示されます。 コンテナーはローカルで実行されているように見えますが、実際には Azure の開発空間で実行されています。
2. `mywebapi` の準備ができたら、ブラウザーで localhost アドレスを開き、URL に `/api/values` を追加して `ValuesController` の既定の GET API を呼び出します。 
3. すべての手順が正常に完了すると、次のような `mywebapi` サービスからの応答を確認できます。

    ![](media/get-started-netcore-visualstudio/WebAPIResponse.png)

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend* から *mywebapi* に対して要求を行う
次に、`mywebapi` に対して要求を行う `webfrontend` のコードを記述しましょう。 `webfrontend` プロジェクトがある Visual Studio ウィンドウに切り替えます。 `HomeController.cs` ファイルで、About メソッドのコードを次のコードに "*置き換えます*"。

   ```csharp
   public async Task<IActionResult> About()
   {
      ViewData["Message"] = "Hello from webfrontend";

      using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

      return View();
   }
   ```

前述のコード例は、`azds-route-as` ヘッダーを受信要求から送信要求に転送します。 これにより[チーム シナリオ](team-development-netcore-visualstudio.md)での生産性の高い開発エクスペリエンスがどのように促進されるのかについては、後ほど説明します。

### <a name="debug-across-multiple-services"></a>複数のサービスでデバッグする
1. この時点で、`mywebapi` は引き続きデバッガーがアタッチされた状態で実行されています。 そうでない場合は、`mywebapi` プロジェクトで F5 キーを押します。
1. `api/values/{id}` の GET 要求を処理する、`Controllers/ValuesController.cs` ファイルの `Get(int id)` メソッドにブレークポイントを設定します。
1. 上記のコードを貼り付けた `webfrontend` プロジェクトで、GET 要求を `mywebapi/api/values` に送信する直前にブレークポイントを設定します。
1. `webfrontend` プロジェクトで F5 キーを押します。 Visual Studio はブラウザーで適切な localhost ポートを再度開き、Web アプリが表示されます。
1. ページの上部にある **[About]** リンクをクリックすると、`webfrontend` プロジェクトのブレークポイントがトリガーされます。 
1. F10 キーを押して続行します。 `mywebapi` プロジェクトのブレークポイントがトリガーされます。
1. F5 キーを押して続行し、`webfrontend` プロジェクトのコードに戻ります。
1. F5 キーをもう一度押すと要求が完了し、ブラウザーでページが返されます。 Web アプリの [About] ページに、2 つのサービスによって連結されたメッセージ "Hello from webfrontend and Hello from mywebapi" が表示されます。

お疲れさまでした。 これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。

### <a name="automatic-tracing-for-http-messages"></a>HTTP メッセージの自動トレース
お気付きかもしれませんが、*mywebapi* に対する HTTP 呼び出しを出力するための特別なコードが *webfrontend* に含まれていないにもかかわらず、出力ウィンドウに HTTP トレース メッセージが表示されます。
```
// The request from your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io --gyk-> webfrontend:
   GET /Home/About HTTP/1.1

// *webfrontend* reaching out to *mywebapi*
webfrontend-668b7ddb9f-n5rhj --pu5-> mywebapi:
   GET /api/values/1 HTTP/1.1

// Response from *mywebapi*
webfrontend-668b7ddb9f-n5rhj <-pu5-- mywebapi:
   HTTP/1.1 200 OK
   Hello from mywebapi

// Response from *webfrontend* to your browser
default.webfrontend.856bb3af715744c6810b.eus.azds.io <-gyk-- webfrontend:
   HTTP/1.1 200 OK
   <!DOCTYPE html>
   <html>
   <head>
       <meta charset="utf-8" />
       <meta name="viewport" content="width=device-width, initial-sc...<[TRUNCATED]>
```
これは、Dev Spaces のインストルメンテーションから得られる "無料" の利点の 1 つです。 ここでは、開発中に複雑なマルチサービス呼び出しを追跡しやすくするために、システムを通過する HTTP 要求を追跡するコンポーネントを挿入します。

### <a name="well-done"></a>お疲れさまでした。
これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Dev Spaces でのチーム開発について学ぶ](team-development-netcore-visualstudio.md)
