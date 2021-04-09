---
title: チュートリアル - 認証に Microsoft ID プラットフォームを使用する Blazor Server アプリを作成する | Azure
titleSuffix: Microsoft identity platform
description: このチュートリアルでは、Blazor Server アプリで Microsoft id プラットフォームを使用して認証を設定します。
author: knicholasa
ms.author: nichola
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.date: 09/15/2020
ms.openlocfilehash: 5a631d9ae7a7d1792e3c4e4a2cbf8281e1168283
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99226019"
---
# <a name="tutorial-create-a-blazor-server-app-that-uses-the-microsoft-identity-platform-for-authentication"></a>チュートリアル:認証に Microsoft ID プラットフォームを使用する Blazor Server アプリを作成する

このチュートリアルでは、Microsoft ID プラットフォームを使用し、Azure Active Directory (Azure AD) にアプリを登録することで、ユーザーのサインインを行い、Microsoft Graph からデータを取得する Blazor Server アプリを構築します。

[Blazor WASM](tutorial-blazor-webassembly.md) のチュートリアルも用意されています。

このチュートリアルの内容:

> [!div class="checklist"]
> * 認証に Azure Active Directory (Azure AD) を使用するように構成された新しい Blazor Server アプリを作成する
> * Microsoft.Identity.Web を使用して認証と承認の両方を処理する
> * 保護された Web API (Microsoft Graph) からデータを取得する

## <a name="prerequisites"></a>前提条件

- [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)
- アプリを登録できる Azure AD テナント。 Azure AD テナントへのアクセス権がない場合は、[Microsoft 365 開発者プログラム](https://developer.microsoft.com/microsoft-365/dev-program)に登録するか、[Azure の無料アカウント](https://azure.microsoft.com/free)を作成することによって取得できます。

## <a name="register-the-app-in-the-azure-portal"></a>Azure portal でアプリを登録する

認証に Azure Active Directory (Azure AD) を使用するすべてのアプリは、Azure AD に登録する必要があります。 [アプリケーションの登録](quickstart-register-app.md)に関する記事にある指示と、以下の追加の指示に従います。

- **[サポートされているアカウントの種類]** で、 **[この組織のディレクトリ内のアカウントのみ]** を選択します。
- **[リダイレクト URI]** ドロップ ダウンは **[Web]** に設定したままにして、「`https://localhost:5001/signin-oidc`」と入力します。 Kestrel で実行されているアプリの既定のポートは 5001 です。 アプリが別のポートで使用可能な場合は、`5001` ではなく、そのポート番号を指定します。

**[管理]** で、 **[認証]**  >  **[Implicit grant and hybrid flows]\(暗黙的な許可およびハイブリッド フロー\)** を選択します。 **[アクセス トークン]** と **[ID トークン]** を選択し、 **[保存]** を選択します。

最後に、アプリが保護された API (この場合 Microsoft Graph) を呼び出すので、その API を呼び出すためのアクセス トークンを要求するときに、その ID を確認するためにクライアント シークレットが必要になります。

1. 同じアプリの登録内で、 **[管理]** の下にある **[証明書 & シークレット]** を選択します。
2. 有効期限がない **新しいクライアント シークレット** を作成します。
3. 次の手順で使用するため、そのシークレットの **値** を書き留めておきます。 このウィンドウから移動すると、もう一度アクセスすることはできません。 ただし、必要に応じて再作成することはできます。

## <a name="create-the-app-using-the-net-cli"></a>.NET CLI を使用してアプリを作成する

次のコマンドを実行して、このチュートリアルで使用する、Microsoft.Identity.Web のテンプレートをダウンロードします。

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::0.4.0-preview
```

それから、次のコマンドを実行してアプリケーションを作成します。 コマンドのプレースホルダーをアプリの [概要] ページの適切な情報に置き換え、コマンド シェルでコマンドを実行します。 `-o|--output` オプションで指定した出力場所にプロジェクト フォルダーが存在しない場合は作成されて、アプリの名前の一部になります。

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph -o {APP NAME} --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

| プレースホルダー   | Azure portal での名前       | 例                                |
| ------------- | ----------------------- | -------------------------------------- |
| `{APP NAME}`  | &mdash;                 | `BlazorSample`                         |
| `{CLIENT ID}` | アプリケーション (クライアント) ID | `41451fa7-0000-0000-0000-69eff5a761fd` |
| `{TENANT ID}` | ディレクトリ (テナント) ID   | `e86c78e2-0000-0000-0000-918e0565a45e` |

次に、エディターで新しい Blazor アプリに移動し、"secret-from-app-registration" というテキストを置き換えて、クライアント シークレットを *appsettings.json* ファイルに追加します。

```json
"ClientSecret": "secret-from-app-registration",
```

## <a name="test-the-app"></a>アプリをテストする

アプリをビルドして実行できる状態になりました。 このテンプレート アプリを実行するときは、--framework を使用して実行するフレームワークを指定する必要があります。 このチュートリアルでは、.NET Core 3.1 SDK を使用します。

```dotnetcli
dotnet run --framework netcoreapp3.1
```

ブラウザーで `https://localhost:5001` に移動し、Azure AD ユーザー アカウントを使用してログインして、アプリが実行されていることを確認します。

## <a name="retrieving-data-from-microsoft-graph"></a>Microsoft Graph からデータを取得する

[Microsoft Graph](/graph/overview) には、ユーザーの Microsoft 365 データへのアクセスを提供するさまざまな API が用意されています。 Microsoft ID プラットフォームをアプリの ID プロバイダーとして使用することで、Microsoft ID プラットフォームによって発行されたトークンは Microsoft Graph によって直接サポートされるため、この情報に簡単にアクセスできます。 このセクションでは、サインインしたユーザーのメールをアプリケーションの [Fetch data]\(データのフェッチ\) ページに表示するためのコードを追加します。

開始する前に、必要なアクセス許可を変更するため、アプリからログアウトしてください。そうしないと、現在のトークンは機能しません。 まだしていない場合は、以下のコードを更新する前に、もう一度アプリを実行して **[ログアウト]** を選択します。

次に、アプリの登録とコードを更新して、ユーザーの電子メールをプルし、アプリ内にメッセージを表示します。 これを実行するには、まず Azure AD でアプリの登録アクセス許可を拡張して、電子メール データへのアクセスを有効にします。 次に、このデータを取得してページのいずれかに表示するコードを Blazor アプリに追加します。

1. Azure portal の **[アプリの登録]** でアプリを選択します。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[アクセス許可の追加]**  >  **[Microsoft Graph]** を選択します。
1. **[委任されたアクセス許可]** を選択し、**Mail.Read** アクセス許可を検索して選択します。
1. **[アクセス許可の追加]** を選択します.

*appsettings.json* ファイルで、適切なアクセス許可を持つ適切なトークンをフェッチするようにコードを更新します。 "DownstreamAPI" の下にある "user.read" スコープの後に "mail.read" を追加します。 これは、アプリがアクセスを要求するスコープ (またはアクセス許可) を指定するものです。

```json
"Scopes": "user.read mail.read"
```

次に、*FetchData.razor* ファイル内のコードを更新して、既定 (ランダム) の気象詳細ではなく電子メール データを取得します。 そのファイル内のコードを以下に置き換えます。

```csharp
@page "/fetchdata"

@inject IHttpClientFactory HttpClientFactory
@inject Microsoft.Identity.Web.ITokenAcquisition TokenAcquisitionService

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
        _httpClient = HttpClientFactory.CreateClient();


        // get a token
        var token = await TokenAcquisitionService.GetAccessTokenForUserAsync(new string[] { "User.Read", "Mail.Read" });

        // make API call
        _httpClient.DefaultRequestHeaders.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
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

    public class MailMessage
    {
        public string Subject;
        public string Sender;
        public DateTime ReceivedTime;
    }
}

```

アプリケーションを起動します。 新しく追加されたアクセス許可の入力を求められますが、これはすべてが正常に動作していることを示します。 アプリは基本的なユーザー プロファイル データ以外にも、電子メール データへのアクセスも要求しています。

同意したら、[フェッチ データ] ページに移動して、いくつかの電子メールを読み取ります。

:::image type="content" source="./media/tutorial-blazor-server/final-app-2.png" alt-text="最終的なアプリのスクリーンショット。&quot;Hello Nicholas&quot; という見出しがあり、Nicholas に属している電子メールの一覧が表示されています。":::

## <a name="next-steps"></a>次の手順

複数のパートから成るシナリオ シリーズで、ユーザーのサインインを行う Web アプリの作成方法をご覧ください。

> [!div class="nextstepaction"]
> [シナリオ: ユーザーをサインインさせる Web アプリ](scenario-web-app-sign-user-overview.md)
