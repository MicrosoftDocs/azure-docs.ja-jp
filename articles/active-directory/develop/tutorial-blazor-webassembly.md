---
title: チュートリアル - Blazor WebAssembly アプリでユーザーのサインインを行い、保護された API を呼び出す
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Blazor WebAssembly (WASM) アプリで Microsoft ID プラットフォームを使用して、ユーザーをサインインし、保護された API を呼び出します。
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 47b4f36aec9a906317a9704a7d73bf66385d9e88
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552118"
---
# <a name="tutorial-sign-in-users-and-call-a-protected-api-from-a-blazor-webassembly-app"></a>チュートリアル:Blazor WebAssembly アプリでユーザーのサインインを行い、保護された API を呼び出す

このチュートリアルでは、Microsoft ID プラットフォームを使用し、Azure Active Directory (Azure AD) にアプリを登録することで、ユーザーのサインインを行い、Microsoft Graph からデータを取得する Blazor WebAssembly アプリを構築します。 

このチュートリアルの内容:

> [!div class="checklist"]
>
> * Microsoft ID プラットフォームを使用して、[認証と承認](authentication-vs-authorization.md)に Azure Active Directory (Azure AD) を使用するように構成された新しい Blazor WebAssembly アプリを作成する
> * 保護された Web API (この場合は [Microsoft Graph](/graph/overview)) からデータを取得する

このチュートリアルでは .NET Core 3.1 を使用します。 .NET のドキュメントに、ASP.NET Core 5.0 を使用して [Blazor WebAssembly アプリのセキュリティを確保する方法](/aspnet/core/blazor/security/webassembly/graph-api)が記載されています。 

[Blazor Server のチュートリアル](tutorial-blazor-server.md)も用意されています。 

## <a name="prerequisites"></a>前提条件

* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* アプリを登録できる Azure AD テナント。 Azure AD テナントへのアクセス権がない場合は、[Microsoft 365 開発者プログラム](https://developer.microsoft.com/microsoft-365/dev-program)に登録するか、[Azure の無料アカウント](https://azure.microsoft.com/free)を作成することによって取得できます。

## <a name="register-the-app-in-the-azure-portal"></a>Azure portal でアプリを登録する

認証に Azure Active Directory (Azure AD) を使用するすべてのアプリは、Azure AD に登録する必要があります。 [アプリケーションの登録](quickstart-register-app.md)に関する記事にある手順と、以下の仕様に従います。

- **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
- **[リダイレクト URI]** ドロップ ダウンは **[Web]** に設定したままにして、「`https://localhost:5001/authentication/login-callback`」と入力します。 Kestrel で実行されているアプリの既定のポートは 5001 です。 アプリが別のポートで使用可能な場合は、`5001` ではなく、そのポート番号を指定します。

登録されたら、 **[管理]** で、 **[認証]**  >  **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** を選択します。 **[アクセス トークン]** と **[ID トークン]** を選択し、 **[保存]** を選択します。

## <a name="create-the-app-using-the-net-core-cli"></a>.NET Core CLI を使用してアプリを作成する

アプリを作成するには、最新の Blazor テンプレートが必要です。 .NET Core CLI の場合、これらは次のコマンドを使用してインストールできます。

```dotnetcli
dotnet new -i Microsoft.Identity.Web.ProjectTemplates::1.6.0
```

それから、次のコマンドを実行してアプリケーションを作成します。 コマンドのプレースホルダーをアプリの [概要] ページの適切な情報に置き換え、コマンド シェルでコマンドを実行します。 `-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。

```dotnetcli
dotnet new blazorwasm2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| プレースホルダー   | Azure portal での名前       | 例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorWASMSample`                         |
| `{CLIENT ID}` | アプリケーション (クライアント) ID | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ディレクトリ (テナント) ID   | `e86c78e2-0000-0000-0000-918e0565a45e` |

## <a name="test-the-app"></a>アプリをテストする

アプリをビルドして実行できる状態になりました。 このテンプレート アプリを実行するときは、--framework を使用して実行するフレームワークを指定する必要があります。 このチュートリアルでは .NET Standard 2.1 を使用しますが、テンプレートでは他のフレームワークもサポートされています。

```dotnetcli
dotnet run --framework netstandard2.1
```

ブラウザーで `https://localhost:5001` に移動し、Azure AD ユーザー アカウントを使用してログインします。アプリが実行されていて、Microsoft ID プラットフォームを使用してユーザーのログインが行われていることがわかります。

Microsoft ID プラットフォームを使用して Azure AD でのログインを有効にするこのテンプレートのコンポーネントについては、[このトピックに関する ASP.NET のドキュメント](/aspnet/core/blazor/security/webassembly/standalone-with-azure-active-directory#authentication-package)を参照してください。

## <a name="retrieving-data-from-a-protected-api-microsoft-graph"></a>保護された API (Microsoft Graph) からデータを取得する

[Microsoft Graph](/graph/overview) は、Microsoft 365 データへのアクセスをユーザーに提供する API を備え、Microsoft ID プラットフォームによって発行されたトークンをサポートしているため、保護された API の例として使用するのに適しています。 このセクションでは、Microsoft Graph を呼び出して、ユーザーのメールをアプリケーションの [Fetch data]\(データのフェッチ\) ページに表示するコードを追加します。

このセクションは、保護された API を名前付きクライアントから呼び出す一般的なアプローチを使用して記述されています。 同じ方法は、他の保護された API を呼び出す場合にも使用できます。 ただし、Microsoft Graph を自分のアプリケーションから呼び出す予定がある場合は、Graph SDK を使用することで定型的なコードを減らすことができます。 [Graph SDK の使用方法](/aspnet/core/blazor/security/webassembly/graph-api?view=aspnetcore-5.0&preserve-view=true)は、.NET のドキュメントに記載されています。

開始する前に、必要なアクセス許可を変更するため、アプリからログアウトしてください。そうしないと、現在のトークンは機能しません。 まだしていない場合は、以下のコードを更新する前に、もう一度アプリを実行して **[ログアウト]** を選択します。

次に、アプリの登録とコードを更新して、ユーザーの電子メールをプルし、アプリ内にメッセージを表示します。

まず、アプリの登録に `Mail.Read` API アクセス許可を追加して、アプリがユーザーの電子メールへのアクセスを要求していることを Azure AD で認識できるようにします。

1. Azure portal の **[アプリの登録]** でアプリを選択します。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[アクセス許可の追加]**  >  **[Microsoft Graph]** を選択します。
1. **[委任されたアクセス許可]** を選択し、**Mail.Read** アクセス許可を検索して選択します。
1. **[アクセス許可の追加]** を選択します.

次に、netstandard2.1 **ItemGroup** にあるプロジェクトの *.csproj* ファイルに以下を追加します。 これにより、次の手順でカスタム HttpClient を作成できるようになります。

```xml
<PackageReference Include="Microsoft.Extensions.Http" Version="3.1.7" />
```

次に、以下のいくつかの手順で指定どおりにコードを変更します。 これらの変更によって、Microsoft Graph API に送信される送信要求に[アクセス トークン](access-tokens.md)が追加されます。 このパターンの詳細については、「[ASP.NET Core Blazor WebAssembly のセキュリティに関するその他のシナリオ](/aspnet/core/blazor/security/webassembly/additional-scenarios)」を参照してください。

まず、次のコードを使用して *GraphAPIAuthorizationMessageHandler.cs* という名前の新しいファイルを作成します。 このハンドラーは、`User.Read` および `Mail.Read` スコープに対するアクセス トークンを Microsoft Graph API への送信要求に追加するユーザーになります。

```csharp
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.WebAssembly.Authentication;

public class GraphAPIAuthorizationMessageHandler : AuthorizationMessageHandler
{
    public GraphAPIAuthorizationMessageHandler(IAccessTokenProvider provider,
        NavigationManager navigationManager)
        : base(provider, navigationManager)
    {
        ConfigureHandler(
            authorizedUrls: new[] { "https://graph.microsoft.com" },
            scopes: new[] { "https://graph.microsoft.com/User.Read", "https://graph.microsoft.com/Mail.Read" });
    }
}
```

次に、*Program.cs* の `Main` メソッドの内容を次のコードで置き換えます。 このコードでは新しい `GraphAPIAuthorizationMessageHandler` を利用し、ユーザーが初めてサインインしたときにアプリが要求する既定のスコープとして `User.Read` と `Mail.Read` を追加します。

```csharp
var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<App>("app");

builder.Services.AddScoped<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddHttpClient("GraphAPI",
        client => client.BaseAddress = new Uri("https://graph.microsoft.com"))
    .AddHttpMessageHandler<GraphAPIAuthorizationMessageHandler>();

builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("User.Read");
    options.ProviderOptions.DefaultAccessTokenScopes.Add("Mail.Read");
});

await builder.Build().RunAsync();
```

最後に、*FetchData.razor* ページの内容を次のコードで置き換えます。 このコードにより、Microsoft Graph API からユーザーの電子メール データがフェッチされ、一覧として表示されます。 `OnInitializedAsync` では、適切なアクセス トークンを使用する新しい `HttpClient` が作成され、Microsoft Graph API への要求を行うために使用されます。

```c#
@page "/fetchdata"
@using System.ComponentModel.DataAnnotations
@using System.Text.Json.Serialization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@using Microsoft.Extensions.Logging
@inject IAccessTokenProvider TokenProvider
@inject IHttpClientFactory ClientFactory
@inject IHttpClientFactory HttpClientFactory

<p>This component demonstrates fetching data from a service.</p>

@if (messages == null)
{
    <p><em>Loading...</em></p>
}
else
{
    <h1>Hello @userDisplayName !!!!</h1>
    <table class="table">
        <thead>
            <tr>
                <th>Subject</th>
                <th>Sender</th>
                <th>Received Time</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var mail in messages)
            {
                <tr>
                    <td>@mail.Subject</td>
                    <td>@mail.Sender</td>
                    <td>@mail.ReceivedTime</td>
                </tr>
            }
        </tbody>
    </table>
}

@code {

    private string userDisplayName;
    private List<MailMessage> messages = new List<MailMessage>();

    private HttpClient _httpClient;

    protected override async Task OnInitializedAsync()
    {
        _httpClient = HttpClientFactory.CreateClient("GraphAPI");
        try {
            var dataRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me");

            if (dataRequest.IsSuccessStatusCode)
            {
                var userData = System.Text.Json.JsonDocument.Parse(await dataRequest.Content.ReadAsStreamAsync());
                userDisplayName = userData.RootElement.GetProperty("displayName").GetString();
            }

            var mailRequest = await _httpClient.GetAsync("https://graph.microsoft.com/beta/me/messages?$select=subject,receivedDateTime,sender&$top=10");

            if (mailRequest.IsSuccessStatusCode)
            {
                var mailData = System.Text.Json.JsonDocument.Parse(await mailRequest.Content.ReadAsStreamAsync());
                var messagesArray = mailData.RootElement.GetProperty("value").EnumerateArray();

                foreach (var m in messagesArray)
                {
                    var message = new MailMessage();
                    message.Subject = m.GetProperty("subject").GetString();
                    message.Sender = m.GetProperty("sender").GetProperty("emailAddress").GetProperty("address").GetString();
                    message.ReceivedTime = m.GetProperty("receivedDateTime").GetDateTime();
                    messages.Add(message);
                }
            }
        }
        catch (AccessTokenNotAvailableException ex)
        {
            // Tokens are not valid - redirect the user to log in again
            ex.Redirect();
        }
    }

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}
```

ここで、アプリを再起動します。 メールを読み取るためのアクセス権をアプリに付与することを求めるメッセージが表示されます。 これは、アプリが `Mail.Read` スコープを要求するときに必要です。

同意したら、[フェッチ データ] ページに移動して、いくつかの電子メールを読み取ります。

:::image type="content" source="./media/tutorial-blazor-webassembly/final-app.png" alt-text="最終的なアプリのスクリーンショット。&quot;Hello Nicholas&quot; という見出しがあり、Nicholas に属している電子メールの一覧が表示されています。":::

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームのベスト プラクティスと推奨事項](./identity-platform-integration-checklist.md)
