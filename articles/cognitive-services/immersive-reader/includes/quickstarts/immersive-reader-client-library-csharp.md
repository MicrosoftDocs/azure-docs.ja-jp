---
title: イマーシブ リーダー C# クライアント ライブラリのクイックスタート
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Web アプリを一から構築してイマーシブ リーダー API 機能を追加します。
services: cognitive-services
author: pasta
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/20/2020
ms.author: pasta
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: ae0ed771bc00b2541db721af54bf277ff36042f9
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921778"
---
[イマーシブ リーダー](https://www.onenote.com/learningtools)は、読解力向上のために実証済みの手法を実装する、包括的に設計されたツールです。

このクイックスタートでは、Web アプリを一から構築し、イマーシブ リーダー クライアント ライブラリを使用してイマーシブ リーダーを統合します。 このクイック スタートの完全なサンプルは[こちら](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)で入手できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Azure Active Directory 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](../../how-to-create-immersive-reader.md)に従ってください。 サンプル プロジェクトのプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。

## <a name="create-a-web-app-project"></a>Web アプリ プロジェクトの作成

Model-View-Controller が組み込まれた ASP.NET Core Web アプリケーション テンプレートと ASP.NET Core 2.1 を使用して、Visual Studio で新しいプロジェクトを作成します。 プロジェクトに "QuickstartSampleWebApp" という名前を付けます。

![[新しいプロジェクト]](../../media/quickstart-csharp/1-createproject.png)

![新しいプロジェクトを構成する](../../media/quickstart-csharp/2-configureproject.png)

![新しい ASP.NET Core Web アプリケーション](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>認証の設定

### <a name="configure-authentication-values"></a>認証の値の構成

_ソリューション エクスプローラー_でプロジェクトを右クリックし、 **[ユーザー シークレットの管理]** を選択します。 これにより、_secrets.json_ という名前のファイルが開きます。 このファイルはソース管理にチェックインされません。 [こちら](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows)をご覧ください。 _secrets.json_ の内容を以下のように置き換え、イマーシブ リーダー リソースを作成したときに取得した値を指定します。

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージの追加

以下のコードでは **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet パッケージのオブジェクトが使用されているため、プロジェクトにそのパッケージへの参照を追加する必要があります。

**[ツール]、[NuGet パッケージ マネージャー]、[パッケージ マネージャー コンソール]** の順に選択して NuGet パッケージ マネージャー コンソールを開き、次のコマンドを実行します。

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>トークンを取得するためのコントローラーの更新 

_Controllers\HomeController.cs_ を開き、ファイルの先頭の _using_ ステートメントの後に次のコードを追加します。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

ここで、_secrets.json_ から Azure AD 値を取得するようにコントローラーを構成します。 _HomeController_ クラスの先頭で、```public class HomeController : Controller {``` の後に、次のコードを追加します。

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>サンプル コンテンツの追加
まず、_Views\Shared\Layout.cshtml_ を開きます。 ```</head>``` という行の前に、次のコードを追加します。

```html
@RenderSection("Styles", required: false)
```

次に、この Web アプリにサンプル コンテンツを追加します。 _Views\Home\Index.cshtml_ を開いて、自動的に生成されたすべてのコードを次のサンプルに置き換えます。

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

すべてのテキストに、テキストの言語を示す **lang** 属性があることに注意してください。 この属性は、イマーシブ リーダーが適切な言語と文法の機能を提供するために役立ちます。

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>イマーシブ リーダーの起動を処理するための JavaScript の追加

イマーシブ リーダー ライブラリは、イマーシブ リーダーの起動、イマーシブ リーダーのボタンの表示などの機能を提供します。 [こちら](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference)をご覧ください。

_Views\Home\Index.cshtml_ の末尾に、次のコードを追加します。

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>アプリのビルドと実行

メニュー バーから **[デバッグ] > [デバッグの開始]** の順に選択するか、**F5** キーを押して、アプリケーションを起動します。

ブラウザーの表示は次のようになります。

![サンプル アプリ](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>イマーシブ リーダーの起動

[イマーシブ リーダー] ボタンをクリックすると、イマーシブ リーダーが起動し、ページのコンテンツが表示されます。

![Immersive Reader](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>次のステップ

* [Node.js クイックスタート](../../tutorial-nodejs.md)を参照して、Node.js を使用してイマーシブ リーダー クライアント ライブラリで他にできることを確認する
* [Android チュートリアル](../../tutorial-android.md)を参照して、Android 用の Java または Kotlin を使用してイマーシブ リーダー SDK で他にできることを確認する
* [iOS チュートリアル](../../tutorial-ios.md)を参照して、iOS 用の Swift を使用してイマーシブ リーダー SDK で他にできることを確認する
* [Python チュートリアル](../../tutorial-python.md)を参照して、Python を使用してイマーシブ リーダー クライアント ライブラリで他にできることを確認する
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](../../reference.md)を探索する