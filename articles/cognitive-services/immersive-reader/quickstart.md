---
title: クイック スタート:C# を使用してイマーシブ リーダーを起動する Web アプリを作成する
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Web アプリを一から構築してイマーシブ リーダー API 機能を追加します。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: ed49f834cb7cd4f649d84aea9e549e212771eead
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899359"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>クイック スタート:イマーシブ リーダーを起動する Web アプリを作成する (C#)

[イマーシブ リーダー](https://www.onenote.com/learningtools)は、読解力向上のために実証済みの手法を実装する、包括的に設計されたツールです。

このクイックスタートでは、Web アプリを一から構築し、Immersive Reader SDK を使用してイマーシブ リーダー機能を統合します。 このクイック スタートの完全なサンプルは[こちら](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)で入手できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Azure Active Directory (Azure AD) 認証用に構成されたイマーシブ リーダー リソース。 設定するには、[これらの手順](./azure-active-directory-authentication.md)に従ってください。 サンプル プロジェクトのプロパティを構成するときに、ここで作成した値の一部が必要になります。 後で参照するために、実際のセッションの出力をテキスト ファイルに保存します。

## <a name="create-a-web-app-project"></a>Web アプリ プロジェクトの作成

Model-View-Controller が組み込まれた ASP.NET Core Web アプリケーション テンプレートを使用して、Visual Studio で新しいプロジェクトを作成します。

![[新しいプロジェクト]](./media/vswebapp.png)

![新しい ASP.NET Core Web アプリケーション](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Azure AD 認証トークンを取得する

この部分については、上の Azure AD 認証構成の前提条件の手順におけるいくつかの値が必要です。 そのセッションで保存したテキスト ファイルを参照します。

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

_ソリューション エクスプローラー_でプロジェクトを右クリックし、 **[ユーザー シークレットの管理]** を選択します。 これにより、_secrets.json_ という名前のファイルが開きます。 そのファイルの内容を次のように置き換え、上のカスタム プロパティ値を指定します。

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

_Controllers\HomeController.cs_ を開き、ファイルを次のコードに置き換えます。

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
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

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
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
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Microsoft.IdentityModel.Clients.ActiveDirectory NuGet パッケージの追加

上のコードでは **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet パッケージのオブジェクトが使用されているため、プロジェクトにそのパッケージへの参照を追加する必要があります。

**[ツール] -> [NuGet パッケージ マネージャー] -> [パッケージ マネージャー コンソール]** の順に選択して NuGet パッケージ マネージャー コンソールを開き、次のように入力します。

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
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
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
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

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
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
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
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

* [Node.js チュートリアル](./tutorial-nodejs.md)で、Node.js と Immersive Reader SDK を使用して他にできることを確認する
* [Python チュートリアル](./tutorial-python.md)を見て、Python と Immersive Reader SDK を使用して他にできることを確認する
* [iOS チュートリアル](./tutorial-ios-picture-immersive-reader.md)を見て、Swift と Immersive Reader SDK を使用して他にできることを確認する
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) と [Immersive Reader SDK リファレンス](./reference.md)を探索する
