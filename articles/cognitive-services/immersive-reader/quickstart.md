---
title: クイック スタート:C# を使用してイマーシブ リーダーを起動する Web アプリを作成する
titlesuffix: Azure Cognitive Services
description: このクイックスタートでは、Web アプリを一から構築してイマーシブ リーダー API 機能を追加します。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 3b408de6b60e7e7704ee228b52c399e5b80e3a9e
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718420"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>クイック スタート:イマーシブ リーダーを起動する Web アプリを作成する (C#)

[イマーシブ リーダー](https://www.onenote.com/learningtools)は、読解力向上のために実証済みの手法を実装する、包括的に設計されたツールです。

このクイックスタートでは、Web アプリを一から構築し、Immersive Reader SDK を使用してイマーシブ リーダー機能を統合します。 このクイック スタートの完全なサンプルは[こちら](https://github.com/microsoft/immersive-reader-sdk/tree/master/samples/quickstart-csharp)で入手できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* イマーシブ リーダーのサブスクリプション キー。 [こちらの手順](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)に従って入手してください。

## <a name="create-a-web-app-project"></a>Web アプリ プロジェクトの作成

Model-View-Controller が組み込まれた ASP.NET Core Web アプリケーション テンプレートを使用して、Visual Studio で新しいプロジェクトを作成します。

![[新しいプロジェクト]](./media/vswebapp.png)

![新しい ASP.NET Core Web アプリケーション](./media/vsmvc.png)

## <a name="acquire-an-access-token"></a>アクセス トークンの取得

この次の手順では、サブスクリプション キーとエンドポイントが必要です。 サブスクリプション キーは、Azure portal のイマーシブ リーダー リソースの [キー] ページで確認できます。 エンドポイントは、[概要] ページで確認できます。

_ソリューション エクスプローラー_でプロジェクトを右クリックし、 **[ユーザー シークレットの管理]** を選択します。 これにより、_secrets.json_ という名前のファイルが開きます。 このファイルの内容を次のように置き換えます。サブスクリプション キーとエンドポイントはご自分のものを指定してください。

```json
{
  "SubscriptionKey": YOUR_SUBSCRIPTION_KEY,
  "Endpoint": YOUR_ENDPOINT
}
```

_Controllers\HomeController.cs_ を開いて、`HomeController` クラスを次のコードに置き換えます。

```csharp
public class HomeController : Controller
{
    private readonly string SubscriptionKey;
    private readonly string Endpoint;

    public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
    {
        SubscriptionKey = configuration["SubscriptionKey"];
        Endpoint = configuration["Endpoint"];

        if (string.IsNullOrEmpty(Endpoint) || string.IsNullOrEmpty(SubscriptionKey))
        {
            throw new ArgumentNullException("Endpoint or subscriptionKey is null!");
        }
    }

    public IActionResult Index()
    {
        return View();
    }

    [Route("token")]
    public async Task<string> Token()
    {
        return await GetTokenAsync();
    }

    /// <summary>
    /// Exchange your Azure subscription key for an access token
    /// </summary>
    private async Task<string> GetTokenAsync()
    {
        using (var client = new System.Net.Http.HttpClient())
        {
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", SubscriptionKey);
            using (var response = await client.PostAsync(Endpoint, null))
            {
                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="add-sample-content"></a>サンプル コンテンツの追加

ここでは、この Web アプリにいくつかのサンプル コンテンツを追加します。 _Views\Home\Index.cshtml_ を開いて、自動的に生成されたコードを次のサンプルに置き換えます。

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
<script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.1.js'></script>
<script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
<script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        ImmersiveReader.launchAsync(token, content, { uiZIndex: 1000000 });
    }
</script>
}
```

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

メニュー バーから **[デバッグ] > [デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。

ブラウザーの表示は次のようになります。

![サンプル アプリ](./media/quickstart-result.png)

[イマーシブ リーダー] ボタンをクリックすると、イマーシブ リーダーが起動し、ページのコンテンツが表示されます。

![Immersive Reader](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>次の手順

* [チュートリアル](./tutorial.md)を見て、Immersive Reader SDK で他にできることを確認する
* [Immersive Reader SDK](https://github.com/Microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する
