---
title: Azure SignalR Service クライアントの認証に関するガイド
description: e2e の例に従って独自の認証を実装し、それを Azure SignalR Service と統合する方法について説明します。
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-js, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: cd5b709843ba2d98170aaa58eb5c3cc1ec8883af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734683"
---
# <a name="azure-signalr-service-authentication"></a>Azure SignalR Service の認証

このチュートリアルは、クイック スタートで導入したチャット ルーム アプリケーションをベースにしています。 まだ「[Create a chat room with SignalR Service (SignalR Service を使用してチャット ルームを作成する)](signalr-quickstart-dotnet-core.md)」を完了していない場合は、先にこの演習を完了してください。

このチュートリアルでは、独自の認証を実装し、Microsoft Azure SignalR サービスと統合する方法を学習します。

クイック スタートのチャット ルーム アプリケーションで最初に使用した認証は、現実のシナリオでは単純すぎます。 アプリケーションでは各クライアントが自分の ID を主張することができ、サーバーは単にそれを受け入れます。 このアプローチは、悪意のあるユーザーが機密データにアクセスするために他人を偽装する可能性がある現実のアプリケーションではあまり役に立ちません。

[GitHub](https://github.com/) は、[OAuth](https://oauth.net/) と呼ばれる一般的な業界標準プロトコルに基づく認証 API を提供します。 これらの API により、サード パーティ アプリケーションは GitHub アカウントを認証できます。 このチュートリアルでは、これらの API を使用して、チャット ルーム アプリケーションへのクライアント ログインを許可する前の GitHub アカウントによる認証を実装します。 GitHub アカウントの認証後、アカウント情報は Web クライアントが認証に使用できるように Cookie として追加されます。

GitHub を通じて提供される OAuth 認証 API の詳細については、「[Basics of Authentication (認証の基礎)](https://developer.github.com/v3/guides/basics-of-authentication/)」を参照してください。

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 ただし、推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルのコードは、[GitHub リポジトリの AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat) からダウンロードできます。

![Azure でホストされている OAuth Complete](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * GitHub アカウントに新しい OAuth アプリを登録する
> * GitHub 認証をサポートする認証コントローラーを追加する
> * Azure に ASP.NET Core Web アプリをデプロイする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を満たしている必要があります。

- [GitHub](https://github.com/) で作成されたアカウント
- [Git](https://git-scm.com/)
- [.NET Core SDK](https://www.microsoft.com/net/download/windows)
- Bash 環境用に [Azure Cloud Shell](../cloud-shell/quickstart.md) が構成されている。
- GitHub リポジトリの [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) をダウンロードまたは複製する。

## <a name="create-an-oauth-app"></a>OAuth アプリを作成する

1. Web ブラウザーを開き、`https://github.com` に移動してアカウントにサインインします。

2. アカウントで **[Settings]\(設定\)**  >  **[Developer settings]\(開発者向け設定\)** に移動し、 *[OAuth Apps]\(OAuth アプリ\)* の下の **[Register a new application]\(新しいアプリケーションの登録\)** または **[New OAuth App]\(新しい OAuth アプリ\)** をクリックします。

3. 新しい OAuth アプリには次の設定を使用し、 **[アプリケーションの登録]** をクリックします。

    | 設定名 | 推奨値 | 説明 |
    | ------------ | --------------- | ----------- |
    | アプリケーション名 | *Azure SignalR チャット* | GitHub ユーザーは、認証しているアプリを認識して信頼することができる必要があります。   |
    | ホームページ URL | `http://localhost:5000/home` | |
    | アプリケーションの説明 | *Azure SignalR サービスと GitHub 認証を使用するチャット ルーム サンプル* | 使用されている認証のコンテキストをアプリケーションのユーザーが理解するために役立つ、アプリケーションの有益な説明。 |
    | 認証コールバックの URL | `http://localhost:5000/signin-github` | この設定は、OAuth アプリケーションの最も重要な設定です。 認証の成功後に GitHub がユーザーに返すコールバック URL です。 このチュートリアルでは、 */signin-github* の *AspNet.Security.OAuth.GitHub* パッケージの既定のコールバック URL を使用する必要があります。  |

4. 新しい OAuth アプリの登録が完了したら、次のコマンドを使用して、*クライアント ID* と *クライアント シークレット* を Secret Manager に追加します。 *Your_GitHub_Client_Id* と *Your_GitHub_Client_Secret* を実際の OAuth アプリの値に置き換えます。

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>OAuth フローを実装する

### <a name="update-the-startup-class-to-support-github-authentication"></a>GitHub 認証をサポートするように Startup クラスを更新する

1. 最新の *Microsoft.AspNetCore.Authentication.Cookies* および *AspNet.Security.OAuth.GitHub* パッケージへの参照を追加し、すべてのパッケージを復元します。

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. *Startup.cs* を開き、次の名前空間に `using` ステートメントを追加します。

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. `Startup` クラスの先頭に、GitHub OAuth アプリのシークレットを保持する Secret Manager キーの定数を追加します。

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. `ConfigureServices` メソッドに次のコードを追加して、GitHub OAuth アプリでの認証をサポートします。

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. `GetUserCompanyInfoAsync` ヘルパー メソッドを `Startup` クラスに追加します。

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Startup クラスの `Configure` メソッドを次のコード行で更新し、ファイルを保存します。

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>認証コントローラーを追加する

このセクションでは、GitHub OAuth アプリを使用してクライアントを認証する `Login` API を実装します。 認証されると、API はクライアントをチャット アプリにリダイレクトして戻す前に、Web クライアントの応答に Cookie を追加します。 その Cookie は、後でクライアントを識別するために使用されます。

1. *chattest\Controllers* ディレクトリに新しいコントローラー コード ファイルを追加します。 ファイルに *AuthController.cs* という名前を付けます。

2. 認証コントローラーに、次のコードを追加します。 プロジェクト ディレクトリが *chattest* でなかった場合は、名前空間を更新してください。

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. 変更を保存します。

### <a name="update-the-hub-class"></a>Hub クラスを更新する

既定では、Web クライアントが SignalR Service に接続しようとすると、内部的に提供されるアクセス トークンに基づいて接続が許可されます。 このアクセス トークンは、認証済み ID に関連付けられていません。 このアクセスは、実際には匿名アクセスです。

このセクションでは、ハブ クラスに `Authorize` 属性を追加し、認証されたユーザーの要求からユーザー名を読み取るようにハブ メソッドを更新することで、実際の認証を有効にします。

1. *Hub\Chat.cs* を開き、これらの名前空間への参照を追加します。

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. 次に示すように、ハブのコードを更新します。 このコードは、`Authorize` 属性を `Chat` クラスに追加し、ハブ メソッドでユーザーの認証された ID を使用します。 また、`OnConnectedAsync` メソッドも追加されます。このメソッドは、新しいクライアントが接続するたびに、システム メッセージをチャット ルームに記録します。

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. 変更を保存します。

### <a name="update-the-web-client-code"></a>Web クライアント コードを更新する

1. *wwwroot\index.html* を開き、ユーザー名の入力を求めるコードを、認証コントローラーから返された Cookie を使用するコードに置き換えます。

    次のコードを *index.html* から削除します。

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    Cookie を使用するために、上のコードの代わりに次のコードを追加します。

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. Cookie を使用するために追加したコード行のすぐ下に、`appendMessage` 関数の次の定義を追加します。

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. `bindConnectionMessage` と `onConnected` 関数を次のコードで更新し、`appendMessage` を使用するようにします。

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. *index.html* の末尾で、以下のように `connection.start()` のエラー ハンドラーを更新して、ユーザーにログインを求めるメッセージを表示するようにします。

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. 変更を保存します。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドし、ローカルで実行する

1. すべてのファイルの変更を保存します。

2. .NET Core CLI を使用してアプリケーションをビルドし、コマンド シェルで次のコマンドを実行します。

    ```dotnetcli
    dotnet build
    ```

3. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

    既定では、アプリはポート 5000 でローカルにホストされます。

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. ブラウザー ウィンドウを起動して、`http://localhost:5000` に移動します。 上部の **[here]** リンクをクリックして、GitHub にログインします。

    ![Azure でホストされている OAuth Complete](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    GitHub アカウントへのチャット アプリのアクセスを承認するように求めるメッセージが表示されます。 **[Authorize]** ボタンをクリックします。

    ![OAuth アプリを承認する](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    リダイレクトされてチャット アプリケーションに戻り、GitHub アカウント名でログインされます。 Web アプリケーションは、追加された新しい認証を使用してユーザーを認証することによって、アカウント名を判別しました。

    ![識別されたアカウント](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    チャット アプリが GitHub で認証を実行し、認証情報をクッキーとして保存するようになりました。他のユーザーが自分のアカウントで認証を行い、他のワークステーションから通信できるようにするには、チャット アプリを Azure にデプロイする必要があります。

## <a name="deploy-the-app-to-azure"></a>Azure にアプリケーションをデプロイする

Azure CLI の環境を準備する:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

このセクションでは、Azure コマンド ライン インターフェイス (CLI) を使用して、Azure で ASP.NET アプリケーションをホストするための新しい Web アプリを [Azure App Service](../app-service/index.yml) に作成します。 Web アプリは、ローカル Git デプロイを使用するように構成されます。 また、Web アプリは、SignalR 接続文字列、GitHub OAuth のアプリ シークレット、およびデプロイ ユーザーによっても構成されます。

以下のリソースを作成する場合は、SignalR Service リソースと同じリソース グループを使用してください。 このようにすると、後ですべてのリソースを削除する場合に、クリーンアップが容易になります。 この例では、前のチュートリアルで推奨されたグループ名である *SignalRTestResources* が使用されたと仮定しています。

### <a name="create-the-web-app-and-plan"></a>Web アプリと計画を作成する

以下のコマンド用のテキストをコピーし、パラメーターを更新します。 更新したスクリプトを Azure Cloud Shell に貼り付け、**Enter** キーを押して、新しい App Service プランと Web アプリを作成します。

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| パラメーター | 説明 |
| -------------------- | --------------- |
| ResourceGroupName | このリソース グループ名は、前のチュートリアルで指定されたものです。 すべてのチュートリアル リソースをまとめてグループ化しておくことをお勧めします。 前のチュートリアルで使用したのと同じリソース グループを使用します。 |
| WebAppPlan | 新しい一意の App Service プラン名を入力します。 |
| WebAppName | これが新しい Web アプリの名前になり、URL の一部になります。 一意な名前を使用してください。 たとえば、signalrtestwebapp22665120 です。   |

### <a name="add-app-settings-to-the-web-app"></a>アプリ設定を Web アプリに追加する

このセクションでは、次のコンポーネントのアプリ設定を追加します。

* SignalR Service リソースの接続文字列
* GitHub OAuth アプリのクライアント ID
* GitHub OAuth アプリのクライアント シークレット

以下のコマンド用のテキストをコピーし、パラメーターを更新します。 更新したスクリプトを Azure Cloud Shell に貼り付け、**Enter** キーを押して、アプリ設定を追加します。

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| パラメーター | 説明 |
| -------------------- | --------------- |
| GitHubClientId | この変数に GitHub OAuth アプリのシークレット クライアント ID を割り当てます。 |
| GitHubClientSecret | この変数に GitHub OAuth アプリのシークレット パスワードを割り当てます。 |
| ResourceGroupName | この変数を、前のセクションで使用したのと同じリソース グループ名に更新します。 |
| SignalRServiceResource | この変数を、クイック スタートで作成した SignalR Service リソースの名前に更新します。 たとえば、signalrtestsvc48778624 です。 |
| WebAppName | この変数を、前のセクションで作成した新しい Web アプリの名前に更新します。 |

### <a name="configure-the-web-app-for-local-git-deployment"></a>ローカル Git デプロイ用に Web アプリを構成する

Azure Cloud Shell に、次のスクリプトを貼り付けます。 このスクリプトは、Git で Web アプリにコードをデプロイするときに使用する新しいデプロイ ユーザー名とパスワードを作成します。 また、このスクリプトはローカル Git リポジトリによるデプロイ用に Web アプリを構成し、Git デプロイ URL を返します。

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| パラメーター | 説明 |
| -------------------- | --------------- |
| DeploymentUserName | 新しいデプロイ ユーザー名を選択します。 |
| DeploymentUserPassword | 新しいデプロイ ユーザーのパスワードを選択します。 |
| ResourceGroupName | 前のセクションで使用したのと同じリソース グループ名を使用します。 |
| WebAppName | これが前に作成した新しい Web アプリの名前になります。 |

このコマンドから返される Git デプロイ URL を書き留めておきます。 この URL は、後で使用します。

### <a name="deploy-your-code-to-the-azure-web-app"></a>Azure Web アプリにコードをデプロイする

コードをデプロイするには、Git Shell で次のコマンドを実行します。

1. プロジェクト ディレクトリのルートに移動します。 Git リポジトリを使用してプロジェクトを初期化していない場合は、次のコマンドを実行します。

    ```bash
    git init
    ```

2. 前に書き留めておいた Git デプロイ URL のリモートを追加します。

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. 初期化されたリポジトリ内のすべてのファイルをステージングし、コミットを追加します。

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Azure の Web アプリにコードをデプロイします。

    ```bash
    git push Azure main
    ```

    Azure にコードをデプロイするための認証を求めるメッセージが表示されます。 前に作成したデプロイ ユーザーのユーザー名とパスワードを入力します。

### <a name="update-the-github-oauth-app"></a>GitHub OAuth アプリを更新する

最後に、GitHub OAuth アプリの **ホームページ URL** と **認証コールバックの URL** を、ホストされている新しいアプリを指すように更新する必要があります。

1. ブラウザーで [https://github.com](https://github.com) を開き、アカウントの **[Settings]\(設定\)**  >  **[Developer settings]\(開発者向け設定\)**  >  **[Oauth Apps]\(Oauth アプリ\)** の順に移動します。

2. 認証アプリをクリックし、 **[Homepage URL]\(ホームページ URL\)** と **[Authorization callback URL]\(認証コールバックの URL\)** を次のように更新します。

    | 設定 | 例 |
    | ------- | ------- |
    | ホームページ URL | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | 認証コールバックの URL | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. Web アプリの URL に移動し、アプリケーションをテストします。

    ![Azure でホストされている OAuth Complete](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに進む場合は、このクイック スタートで作成したリソースを維持して、次のチュートリアルで再利用することができます。

クイック スタートのサンプル アプリケーションの使用を終える場合は、課金を避けるために、このクイック スタートで作成した Azure リソースを削除することができます。

> [!IMPORTANT]
> いったん削除したリソース グループを元に戻すことはできません。リソース グループとそこに存在するすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルのホストとなるリソースを、保持するリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、個々のブレードから各リソースを個別に削除することができます。

[Azure ポータル](https://portal.azure.com) にサインインし、 **[リソース グループ]** をクリックします。

**[名前でフィルター]** ボックスにリソース グループの名前を入力します。 この記事の手順では、*SignalRTestResources* という名前のリソース グループを使用しました。 結果一覧でリソース グループの **[...]** をクリックし、 **[リソース グループの削除]** をクリックします。

![削除](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を入力し、 **[削除]** をクリックします。

しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure SignalR Service を使用してより良い認証方法を提供するために、OAuth による認証を追加しました。 Azure SignalR Server の使用方法の詳細については、SignalR Service の Azure CLI サンプルを参照してください。

> [!div class="nextstepaction"]
> [Azure SignalR の CLI サンプル](./signalr-reference-cli.md)