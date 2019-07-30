---
title: .NET Core と VS Code を使用した複数の依存サービスの実行
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/21/2018
ms.topic: tutorial
description: Azure のコンテナーとマイクロサービスを使用した迅速な Kubernetes 開発
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー, Helm, サービス メッシュ, サービス メッシュのルーティング, kubectl, k8s
ms.openlocfilehash: 2a1e99ba1c19dfdcaaf1b6709e6d3976968cf623
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503107"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Azure Dev Spaces を使用したマルチサービスの開発

このチュートリアルでは、Azure Dev Spaces を使用してマルチサービス アプリケーションを開発する方法と、Dev Spaces が提供するいくつかの追加の利点について学習します。

## <a name="call-a-service-running-in-a-separate-container"></a>別のコンテナーで実行されているサービスを呼び出す

このセクションでは、2 つ目のサービス (`mywebapi`) を作成し、`webfrontend` でそのサービスを呼び出します。 各サービスは別々のコンテナーで実行されます。 その後、両方のコンテナーでデバッグします。

![複数のコンテナー](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>*mywebapi* のサンプル コードをダウンロードする
時間を節約するために、GitHub リポジトリからサンプル コードをダウンロードしましょう。 [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces ) に移動し、 **[Clone or download]** をクリックして GitHub リポジトリをダウンロードします。 このセクションのコードは、`samples/dotnetcore/getting-started/mywebapi` にあります。

### <a name="run-mywebapi"></a>*mywebapi* を実行する
1. "*別の VS Code ウィンドウ*" で、`mywebapi` フォルダーを開きます。
1. ( **[表示 | コマンド パレット]** メニューを使用して) **コマンド パレット**を開き、オート コンプリートを使用して次のコマンドを入力および選択します: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`。 このコマンドと、デプロイ用にプロジェクトを構成する `azds prep` コマンドを混同しないでください。
1. F5 キーを押し、サービスがビルドされ、展開されるまで待ちます。 準備ができると、*Application started.Press Ctrl+C to shut down.* (アプリケーションが開始しました。シャットダウンするには Ctrl + C キーを押してください) というメッセージがデバッグ コンソールに表示されます。
1. エンドポイント URL は `http://localhost:<portnumber>` のように表記されます。 **ヒント: VS Code のステータス バーがオレンジ色に変わり、クリック可能な URL が表示されます。** コンテナーはローカルで実行されているように見えますが、実際には Azure の開発空間で実行されています。 localhost アドレスである理由は、`mywebapi` でパブリック エンドポイントが定義されておらず、Kubernetes インスタンス内からしかアクセスできないためです。 利便性を考慮し、また、ローカル コンピューターからのプライベート サービスとの対話を容易にするために、Azure Dev Spaces では、Azure で実行されているコンテナーへの一時的な SSH トンネルが作成されます。
1. `mywebapi` の準備ができたら、ブラウザーで localhost アドレスを開きます。 URL に `/api/values` を追加して、`ValuesController` の既定の GET API を呼び出します。
1. すべての手順が正常に完了すると、`mywebapi` サービスからの応答を確認できます。

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>*webfrontend* から *mywebapi* に対して要求を行う
次に、`mywebapi` に対して要求を行う `webfrontend` のコードを記述しましょう。
1. `webfrontend` の VS Code ウィンドウに切り替えます。
1. `HomeController.cs` 内の About メソッドのコードを "*置き換えます*"。

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

前述のコード例は、`azds-route-as` ヘッダーを受信要求から送信要求に転送します。 これがチームによる[共同開発](team-development-netcore.md)にどのように役立つかについては、後ほど説明します。

### <a name="debug-across-multiple-services"></a>複数のサービスでデバッグする
1. この時点で、`mywebapi` は引き続きデバッガーがアタッチされた状態で実行されています。 そうでない場合は、`mywebapi` プロジェクトで F5 キーを押します。
1. `api/values/{id}` の GET 要求を処理する `Get(int id)` メソッド内にブレークポイントを設定します。 これは、[*Controllers/ValuesController.cs* ファイルの 23 行目](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23)あたりになります。
1. `webfrontend` プロジェクトで、GET 要求を `mywebapi/api/values` に送信する直前にブレークポイントを設定します。 これは、前のセクションで変更した [*Controllers/HomeController.cs* ファイル](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs)の 32 行目あたりになります。
1. `webfrontend` プロジェクトで F5 キーを押します。
1. Web アプリを起動し、両方のサービスでコードをステップ実行します。
1. Web アプリの [About]\(詳細情報\) ページに、2 つのサービスによって連結されたメッセージ "Hello from webfrontend and Hello from mywebapi" が表示されます。

### <a name="well-done"></a>お疲れさまでした。
これで、各コンテナーを個別に開発して展開できる、複数コンテナー アプリケーションが作成されました。


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Dev Spaces でのチーム開発について学ぶ](team-development-netcore.md)
