---
title: Azure SignalR Service の使用方法に関するクイック スタート
description: Azure SignalR Service を使って ASP.NET Core MVC アプリによるチャット ルームを作成する方法について説明します。
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: zhshang
ms.openlocfilehash: 3cfcf57f455a5c3b17b794acf2ded66ed2285eff
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94873507"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>クイック スタート:SignalR Service を使用してチャット ルームを作成する

Azure SignalR Service は、開発者がリアルタイムの機能を使って Web アプリケーションを簡単に作成できるようにするための Azure サービスです。 このサービスは当初 [SignalR for ASP.NET Core 2.1](/aspnet/core/signalr/introduction?preserve-view=true&view=aspnetcore-2.1) をベースとしたものでしたが、現在では、それ以降のバージョンがサポートされています。

この記事では、Azure SignalR Service の使用を開始する方法について説明します。 このクイック スタートでは、ASP.NET Core MVC Web アプリを使用してチャット アプリケーションを作成します。 このアプリでは、Azure SignalR Service との接続を確立して、リアルタイムのコンテンツ更新を可能にします。 Web アプリケーションをローカルでホストし、複数のブラウザー クライアントに接続します。 各クライアントは、他のすべてのクライアントにコンテンツ更新をプッシュできるようになります。 

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 1 つのオプションは、Windows、macOS、および Linux プラットフォームで使用可能な [Visual Studio Code](https://code.visualstudio.com/) です。

このチュートリアルのコードは、[GitHub リポジトリの AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom) からダウンロードできます。 また、[「SignalR Service の作成」のスクリプト](scripts/signalr-cli-create-service.md)に従って、このクイック スタートで使用される Azure リソースを作成することもできます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note-dotnet.md)]

## <a name="prerequisites"></a>前提条件

* [.NET Core SDK](https://www.microsoft.com/net/download/windows) のインストール。
* GitHub リポジトリの [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) をダウンロードまたは複製する。 

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsnetcore)。

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR リソースの作成

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsnetcore)。

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

このセクションでは、[.NET Core コマンド ライン インターフェイス (CLI)](/dotnet/core/tools/) を使用して ASP.NET Core MVC Web アプリ プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、Windows、macOS、および Linux プラットフォームで使用できることです。 

1. プロジェクトのフォルダーを作成します。 このクイック スタートでは、*E:\Testing\chattest* フォルダーを使用します。

2. 新しいフォルダーで、次のコマンドを実行してプロジェクトを作成します。

    ```dotnetcli
    dotnet new mvc
    ```

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsnetcore)。

## <a name="add-secret-manager-to-the-project"></a>プロジェクトにシークレット マネージャーを追加します

このセクションでは、プロジェクトに[シークレット マネージャー ツール](/aspnet/core/security/app-secrets)を追加します。 シークレット マネージャー ツールは、開発作業のための機密データをプロジェクト ツリーの外部に格納します。 このアプローチは、ソース コード内のアプリ シークレットが誤って共有されることを防止するのに役立ちます。

1. *.csproj* ファイルを開きます。 `DotNetCliToolReference` 要素を追加して、*Microsoft.Extensions.SecretManager.Tools* を含めます。 また、*chattest.csproj* の次のコードに示すように `UserSecretsId` 要素も追加し、ファイルを保存します。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>

    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.2" />
    </ItemGroup>

    </Project>
    ```

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsnetcore)。

## <a name="add-azure-signalr-to-the-web-app"></a>Web アプリに Azure SignalR を追加する

1. 次のコマンドを実行して、`Microsoft.Azure.SignalR` NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Microsoft.Azure.SignalR
    ```

2. 次のコマンドを実行して、プロジェクトのパッケージを復元します。

    ```dotnetcli
    dotnet restore
    ```

3. シークレット マネージャーに、*Azure:SignalR:ConnectionString* という名前のシークレットを追加します。 

    このシークレットには、SignalR Service リソースにアクセスするための接続文字列が含められます。 *Azure:SignalR:ConnectionString* は、SignalR が接続を確立するために検索する既定の構成キーです。 次のコマンドにある値を SignalR Service リソースの接続文字列に置き換えます。

    このコマンドは、 *.csproj* ファイルと同じディレクトリで実行する必要があります。

    ```dotnetcli
    dotnet user-secrets set Azure:SignalR:ConnectionString "<Your connection string>"
    ```

    シークレット マネージャーは、Web アプリがローカルでホストされている間、そのアプリをテストするためにのみ使用されます。 後のチュートリアルでは、チャット Web アプリを Azure にデプロイします。 Web アプリが Azure にデプロイされた後は、シークレット マネージャーで接続文字列を格納する代わりに、アプリケーションの設定を使用します。

    このシークレットは、構成 API でアクセスされます。 構成名の中のコロン (:) は、サポートされているすべてのプラットフォーム上の構成 API で機能します。 [環境別の構成](/dotnet/core/extensions/configuration-providers#environment-variable-configuration-provider)に関するページを参照してください。


4. *Startup.cs* を開き、`AddSignalR()` メソッドと `AddAzureSignalR()` メソッドを呼び出して Azure SignalR Service を使うように、`ConfigureServices` メソッドを更新します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSignalR()
                .AddAzureSignalR();
    }
    ```

    `AddAzureSignalR()` にパラメーターを渡していないため、このコードは、SignalR Service リソースの接続文字列に既定の構成キーを使用します。 既定の構成キーは *Azure:SignalR:ConnectionString* です。

5. *Startup.cs* で、`Configure` メソッドを次のコードに置き換えて更新します。

    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseRouting();
        app.UseFileServer();
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapHub<ChatHub>("/chat");
        });
    }
    ```

### <a name="add-a-hub-class"></a>ハブ クラスの追加

SignalR では、ハブは、クライアントから呼び出すことができる一連のメソッドを公開するコア コンポーネントです。 このセクションでは、次の 2 つのメソッドでハブ クラスを定義します。

* `Broadcast`:このメソッドは、すべてのクライアントにメッセージをブロードキャストします。
* `Echo`:このメソッドは、メッセージを呼び出し元に返送します。

どちらのメソッドも、ASP.NET Core SignalR SDK によって提供される `Clients` インターフェイスを使用します。 このインターフェイスにより、接続されているすべてのクライアントにアクセスできるため、クライアントにコンテンツをプッシュできます。

1. プロジェクト ディレクトリに、*Hub* という名前の新しいフォルダーを追加します。 新しいフォルダーに、*ChatHub.cs* という名前の新しいハブ コード ファイルを追加します。

2. 次のコードを *ChatHub.cs* に追加してハブ クラスを定義し、ファイルを保存します。

    使用しているプロジェクト名が *SignalR.Mvc* ではない場合は、このクラスの名前空間を更新してください。

    ```csharp
    using Microsoft.AspNetCore.SignalR;
    using System.Threading.Tasks;
    
    namespace SignalR.Mvc
    {
        public class ChatHub : Hub
        {
            public Task BroadcastMessage(string name, string message) =>
                Clients.All.SendAsync("broadcastMessage", name, message);
    
            public Task Echo(string name, string message) =>
                Clients.Client(Context.ConnectionId)
                       .SendAsync("echo", name, $"{message} (echo from server)");
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Web アプリのクライアント インターフェイスを追加する

このチャット ルーム アプリのクライアント ユーザー インターフェイスは、*wwwroot* ディレクトリ内の *index.html* という名前のファイルに含まれている HTML と JavaScript で構成されます。

*css/site.css* ファイルを [サンプル リポジトリ](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot)の *wwwroot* フォルダーからコピーします。 プロジェクトの *css/site.css* を、コピーしたものに置き換えます。

*index.html* のメイン コードを次に示します。

*wwwroot* ディレクトリに *index.html* という名前の新しいファイルを作成し、次の HTML をコピーして、新しく作成したファイルに貼り付けます。

```html
<!DOCTYPE html>
<html>
<head>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@3.3.7/dist/css/bootstrap.min.css" rel="stylesheet" />
    <link href="css/site.css" rel="stylesheet" />
    <title>Azure SignalR Group Chat</title>
</head>
<body>
    <h2 class="text-center" style="margin-top: 0; padding-top: 30px; padding-bottom: 30px;">Azure SignalR Group Chat</h2>
    <div class="container" style="height: calc(100% - 110px);">
        <div id="messages" style="background-color: whitesmoke; "></div>
        <div style="width: 100%; border-left-style: ridge; border-right-style: ridge;">
            <textarea id="message"
                      style="width: 100%; padding: 5px 10px; border-style: hidden;"
                      placeholder="Type message and press Enter to send..."></textarea>
        </div>
        <div style="overflow: auto; border-style: ridge; border-top-style: hidden;">
            <button class="btn-warning pull-right" id="echo">Echo</button>
            <button class="btn-success pull-right" id="sendmessage">Send</button>
        </div>
    </div>
    <div class="modal alert alert-danger fade" id="myModal" tabindex="-1" role="dialog" aria-labelledby="myModalLabel">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <div>Connection Error...</div>
                    <div><strong style="font-size: 1.5em;">Hit Refresh/F5</strong> to rejoin. ;)</div>
                </div>
            </div>
        </div>
    </div>

    <!--Reference the SignalR library. -->
    <script src="https://cdn.jsdelivr.net/npm/@microsoft/signalr@3.1.8/dist/browser/signalr.min.js"></script>

    <!--Add script to update the page and send messages.-->
    <script type="text/javascript">
        document.addEventListener('DOMContentLoaded', function () {

            const generateRandomName = () =>
                Math.random().toString(36).substring(2, 10);

            let username = generateRandomName();
            const promptMessage = 'Enter your name:';
            do {
                username = prompt(promptMessage, username);
                if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
                    username = '';
                    promptMessage = 'Invalid input. Enter your name:';
                }
            } while (!username)

            const messageInput = document.getElementById('message');
            messageInput.focus();

            function createMessageEntry(encodedName, encodedMsg) {
                var entry = document.createElement('div');
                entry.classList.add("message-entry");
                if (encodedName === "_SYSTEM_") {
                    entry.innerHTML = encodedMsg;
                    entry.classList.add("text-center");
                    entry.classList.add("system-message");
                } else if (encodedName === "_BROADCAST_") {
                    entry.classList.add("text-center");
                    entry.innerHTML = `<div class="text-center broadcast-message">${encodedMsg}</div>`;
                } else if (encodedName === username) {
                    entry.innerHTML = `<div class="message-avatar pull-right">${encodedName}</div>` +
                        `<div class="message-content pull-right">${encodedMsg}<div>`;
                } else {
                    entry.innerHTML = `<div class="message-avatar pull-left">${encodedName}</div>` +
                        `<div class="message-content pull-left">${encodedMsg}<div>`;
                }
                return entry;
            }

            function bindConnectionMessage(connection) {
                var messageCallback = function (name, message) {
                    if (!message) return;
                    var encodedName = name;
                    var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
                    var messageEntry = createMessageEntry(encodedName, encodedMsg);

                    var messageBox = document.getElementById('messages');
                    messageBox.appendChild(messageEntry);
                    messageBox.scrollTop = messageBox.scrollHeight;
                };
                connection.on('broadcastMessage', messageCallback);
                connection.on('echo', messageCallback);
                connection.onclose(onConnectionError);
            }

            function onConnected(connection) {
                console.log('connection started');
                connection.send('broadcastMessage', '_SYSTEM_', username + ' JOINED');
                document.getElementById('sendmessage').addEventListener('click', function (event) {
                    if (messageInput.value) {
                        connection.send('broadcastMessage', username, messageInput.value);
                    }

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
                    connection.send('echo', username, messageInput.value);

                    messageInput.value = '';
                    messageInput.focus();
                    event.preventDefault();
                });
            }

            function onConnectionError(error) {
                if (error && error.message) {
                    console.error(error.message);
                }
                var modal = document.getElementById('myModal');
                modal.classList.add('in');
                modal.style = 'display: block;';
            }

            const connection = new signalR.HubConnectionBuilder()
                .withUrl('/chat')
                .build();
            bindConnectionMessage(connection);
            connection.start()
                .then(() => onConnected(connection))
                .catch(error => console.error(error.message));
        });
    </script>
</body>
</html>
```

*index.html* 内のコードは、`HubConnectionBuilder.build()` を呼び出して Azure SignalR リソースへの HTTP 接続を作成します。

接続が成功すると、その接続が `bindConnectionMessage` に渡され、受信されたコンテンツ プッシュ用のイベント ハンドラーがクライアントに追加されます。 

`HubConnection.start()` は、ハブとの通信を開始します。 次に、`onConnected()` がボタンのイベント ハンドラーを追加します。 これらのハンドラーが接続を使用することで、そのクライアントからすべての接続済みクライアントにコンテンツ更新をプッシュできるようになります。

## <a name="add-a-development-runtime-profile"></a>開発ランタイム プロファイルの追加

このセクションでは、ASP.NET Core のための開発ランタイム環境を追加します。 詳細については、「[ASP.NET Core で複数の環境を使用する](/aspnet/core/fundamentals/environments)」を参照してください。

1. プロジェクト内に *Properties* という名前のフォルダーを作成します。

2. このフォルダーに、次の内容を持つ *launchSettings.json* という名前の新しいファイルを追加し、ファイルを保存します。

    ```json
    {
        "profiles" : {
            "ChatRoom": {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsnetcore)。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```dotnetcli
    dotnet build
    ```

1. ビルドが正常に完了したら、次のコマンドを実行して Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

    このアプリは、開発ランタイム プロファイルの構成に従って、ポート 5000 上でローカルにホストされます。

    ```output
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: https://localhost:5001
    info: Microsoft.Hosting.Lifetime[0]
          Now listening on: http://localhost:5000
    info: Microsoft.Hosting.Lifetime[0]
          Application started. Press Ctrl+C to shut down.
    info: Microsoft.Hosting.Lifetime[0]
          Hosting environment: Development
    info: Microsoft.Hosting.Lifetime[0]
          Content root path: E:\Testing\chattest
    ```

1. 2 つのブラウザー ウィンドウを開きます。 各ブラウザーで、`http://localhost:5000` に移動します。 名前を入力するよう求められます。 両方のクライアントのクライアント名を入力し、 **[Send]** ボタンを使用して、両方のクライアント間でのメッセージ コンテンツのプッシュをテストします。

    ![Azure SignalR グループ チャットの例](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsnetcore)。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに進む場合は、このクイック スタートで作成されたリソースを保持し、それを再利用できます。

クイック スタートのサンプル アプリケーションを完了した場合は、料金がかからないように、このクイック スタートで作成された Azure リソースを削除できます。 

> [!IMPORTANT]
> リソース グループの削除は元に戻せず、そのグループ内のすべてのリソースが含まれます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルをホストするためのリソースを、保持したいリソースを含む既存のリソース グループ内に作成した場合は、リソース グループを削除する代わりに、各リソースをそのブレードから個別に削除できます。

[Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

**[名前でフィルター]** テキスト ボックスに、リソース グループの名前を入力します。 このクイックスタートの手順では、*SignalRTestResources* という名前のリソース グループを使用しました。 結果の一覧の中のリソース グループで、省略記号 ( **[...]** ) > **[リソース グループの削除]** を選択します。

![リソース グループを削除するための選択](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)

リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を入力し、 **[削除]** を選択します。

しばらくすると、リソース グループとそのリソースのすべてが削除されます。

問題がある場合は、 [トラブルシューティング ガイド](signalr-howto-troubleshoot-guide.md)をお試しになるか、[ご連絡ください](https://aka.ms/asrs/qsnetcore)。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、新しい Azure SignalR Service リソースを作成しました。 その後、それを ASP.NET Core Web アプリで使用して、コンテンツの更新を接続されている複数のクライアントにリアルタイムにプッシュしました。 Azure SignalR Service の使用についてさらに学習するには、認証をデモンストレーションするチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure SignalR Service の認証](./signalr-concept-authenticate-oauth.md)
