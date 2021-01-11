---
title: Azure SignalR Service の使用方法に関するクイック スタート
description: Azure SignalR Service を使って ASP.NET Core MVC アプリによるチャット ルームを作成する方法について説明します。
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 11/04/2019
ms.author: zhshang
ms.openlocfilehash: 6c330b201c74a2ce56283e30be90cd117b1022f6
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050524"
---
# <a name="quickstart-create-a-chat-room-by-using-signalr-service"></a>クイック スタート:SignalR Service を使用してチャット ルームを作成する


Azure SignalR Service は、開発者がリアルタイムの機能を使って Web アプリケーションを簡単に作成できるようにするための Azure サービスです。 このサービスは、[SignalR for ASP.NET Core 2.1](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-2.1) に基づいていますが、[SignalR for ASP.NET Core 3.0](https://docs.microsoft.com/aspnet/core/signalr/introduction?view=aspnetcore-3.0) もサポートしています。

この記事では、Azure SignalR Service の使用を開始する方法について説明します。 このクイック スタートでは、ASP.NET Core MVC Web アプリを使用してチャット アプリケーションを作成します。 このアプリでは、Azure SignalR Service との接続を確立して、リアルタイムのコンテンツ更新を可能にします。 Web アプリケーションをローカルでホストし、複数のブラウザー クライアントに接続します。 各クライアントは、他のすべてのクライアントにコンテンツ更新をプッシュできるようになります。 

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 1 つのオプションは、Windows、macOS、および Linux プラットフォームで使用可能な [Visual Studio Code](https://code.visualstudio.com/) です。

このチュートリアルのコードは、[GitHub リポジトリの AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom) からダウンロードできます。 また、[「SignalR Service の作成」のスクリプト](scripts/signalr-cli-create-service.md)に従って、このクイック スタートで使用される Azure リソースを作成することもできます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>前提条件

* [.NET Core SDK](https://www.microsoft.com/net/download/windows) のインストール。
* GitHub リポジトリの [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) をダウンロードまたは複製する。 

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

## <a name="create-an-azure-signalr-resource"></a>Azure SignalR リソースの作成

[!INCLUDE [azure-signalr-create](../../includes/signalr-create.md)]

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

## <a name="create-an-aspnet-core-web-app"></a>ASP.NET Core Web アプリケーションの作成

このセクションでは、[.NET Core コマンド ライン インターフェイス (CLI)](https://docs.microsoft.com/dotnet/core/tools/) を使用して ASP.NET Core MVC Web アプリ プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、Windows、macOS、および Linux プラットフォームで使用できることです。 

1. プロジェクトのフォルダーを作成します。 このクイック スタートでは、*E:\Testing\chattest* フォルダーを使用します。

2. 新しいフォルダーで、次のコマンドを実行してプロジェクトを作成します。

    ```dotnetcli
    dotnet new mvc
    ```

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

## <a name="add-secret-manager-to-the-project"></a>プロジェクトにシークレット マネージャーを追加します

このセクションでは、プロジェクトに[シークレット マネージャー ツール](https://docs.microsoft.com/aspnet/core/security/app-secrets)を追加します。 シークレット マネージャー ツールは、開発作業のための機密データをプロジェクト ツリーの外部に格納します。 このアプローチは、ソース コード内のアプリ シークレットが誤って共有されることを防止するのに役立ちます。

1. *.csproj* ファイルを開きます。 `DotNetCliToolReference` 要素を追加して、*Microsoft.Extensions.SecretManager.Tools* を含めます。 また、*chattest.csproj* の次のコードに示すように `UserSecretsId` 要素も追加し、ファイルを保存します。

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    <PropertyGroup>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <UserSecretsId>SignalRChatRoomEx</UserSecretsId>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.0" />
    </ItemGroup>
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.0" />
        <DotNetCliToolReference Include="Microsoft.Extensions.SecretManager.Tools" Version="2.0.0" />
    </ItemGroup>
    </Project>    
    ```
    
[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

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

    このシークレットは、構成 API でアクセスされます。 構成名の中のコロン (:) は、サポートされているすべてのプラットフォーム上の構成 API で機能します。 [環境別の構成](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0)に関するページを参照してください。 


4. *Startup.cs* を開き、`services.AddSignalR().AddAzureSignalR()` メソッドを呼び出して Azure SignalR Service を使うように、`ConfigureServices` メソッドを更新します。

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc();
        services.AddSignalR().AddAzureSignalR();
    }
    ```

    `AddAzureSignalR()` にパラメーターを渡していないため、このコードは、SignalR Service リソースの接続文字列に既定の構成キーを使用します。 既定の構成キーは *Azure:SignalR:ConnectionString* です。

5. また、*Startup.cs* で、`app.UseStaticFiles()` への呼び出しを次のコードに置き換えることで `Configure` メソッドを更新し、ファイルを保存します (ASP.NET Core 2 の場合のみ)。

    ```csharp
    app.UseFileServer();
    app.UseAzureSignalR(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```            
    ASP.NET Core 3 以降の場合は、上記のコードを次のコードに置き換えます。

    ```csharp
    app.UseFileServer();
    app.UseRouting();
    app.UseAuthorization();

    app.UseEndpoints(routes =>
    {
        routes.MapHub<Chat>("/chat");
    });
    ```

### <a name="add-a-hub-class"></a>ハブ クラスの追加

SignalR では、ハブは、クライアントから呼び出すことができる一連のメソッドを公開するコア コンポーネントです。 このセクションでは、次の 2 つのメソッドでハブ クラスを定義します。 

* `Broadcast`:このメソッドは、すべてのクライアントにメッセージをブロードキャストします。
* `Echo`:このメソッドは、メッセージを呼び出し元に返送します。

どちらのメソッドも、ASP.NET Core SignalR SDK によって提供される `Clients` インターフェイスを使用します。 このインターフェイスにより、接続されているすべてのクライアントにアクセスできるため、クライアントにコンテンツをプッシュできます。

1. プロジェクト ディレクトリに、*Hub* という名前の新しいフォルダーを追加します。 新しいフォルダーに、*Chat.cs* という名前の新しいハブ コード ファイルを追加します。

2. 次のコードを *Chat.cs* に追加してハブ クラスを定義し、ファイルを保存します。 

    使用しているプロジェクト名が *chattest* ではない場合は、このクラスの名前空間を更新してください。

    ```csharp
    using Microsoft.AspNetCore.SignalR;

    namespace chattest
    {

        public class Chat : Hub
        {
            public void BroadcastMessage(string name, string message)
            {
                Clients.All.SendAsync("broadcastMessage", name, message);
            }

            public void Echo(string name, string message)
            {
                Clients.Client(Context.ConnectionId).SendAsync("echo", name, message + " (echo from server)");
            }
        }
    }
    ```

### <a name="add-the-client-interface-for-the-web-app"></a>Web アプリのクライアント インターフェイスを追加する

このチャット ルーム アプリのクライアント ユーザー インターフェイスは、*wwwroot* ディレクトリ内の *index.html* という名前のファイルに含まれている HTML と JavaScript で構成されます。

*index.html* ファイル、*css* フォルダー、および *scripts* フォルダーを[サンプル リポジトリ](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/ChatRoom/wwwroot)の *wwwroot* フォルダーからコピーします。 それらをプロジェクトの *wwwroot* フォルダーに貼り付けます。

*index.html* のメイン コードを次に示します。 

```javascript
var connection = new signalR.HubConnectionBuilder()
                            .withUrl('/chat')
                            .build();
bindConnectionMessage(connection);
connection.start()
    .then(function () {
        onConnected(connection);
    })
    .catch(function (error) {
        console.error(error.message);
    });
```    

*index.html* 内のコードは、`HubConnectionBuilder.build()` を呼び出して Azure SignalR リソースへの HTTP 接続を作成します。

接続が成功すると、その接続が `bindConnectionMessage` に渡され、受信されたコンテンツ プッシュ用のイベント ハンドラーがクライアントに追加されます。 

`HubConnection.start()` は、ハブとの通信を開始します。 次に、`onConnected()` がボタンのイベント ハンドラーを追加します。 これらのハンドラーが接続を使用することで、そのクライアントからすべての接続済みクライアントにコンテンツ更新をプッシュできるようになります。

## <a name="add-a-development-runtime-profile"></a>開発ランタイム プロファイルの追加

このセクションでは、ASP.NET Core のための開発ランタイム環境を追加します。 詳細については、「[ASP.NET Core で複数の環境を使用する](https://docs.microsoft.com/aspnet/core/fundamentals/environments)」を参照してください。

1. プロジェクト内に *Properties* という名前のフォルダーを作成します。

2. このフォルダーに、次の内容を持つ *launchSettings.json* という名前の新しいファイルを追加し、ファイルを保存します。

    ```json
    {
        "profiles" : 
        {
            "ChatRoom": 
            {
                "commandName": "Project",
                "launchBrowser": true,
                "environmentVariables": 
                {
                    "ASPNETCORE_ENVIRONMENT": "Development"
                },
                "applicationUrl": "http://localhost:5000/"
            }
        }
    }
    ```

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```dotnetcli
    dotnet build
    ```

2. ビルドが正常に完了したら、次のコマンドを実行して Web アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

    このアプリは、開発ランタイム プロファイルの構成に従って、ポート 5000 上でローカルにホストされます。

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Development
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
    Application started. Press Ctrl+C to shut down.    
    ```

3. 2 つのブラウザー ウィンドウを開きます。 各ブラウザーで、`http://localhost:5000` に移動します。 名前を入力するよう求められます。 両方のクライアントのクライアント名を入力し、 **[Send]** ボタンを使用して、両方のクライアント間でのメッセージ コンテンツのプッシュをテストします。

    ![Azure SignalR グループ チャットの例](media/signalr-quickstart-dotnet-core/signalr-quickstart-complete-local.png)

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

次のチュートリアルに進む場合は、このクイック スタートで作成されたリソースを保持し、それを再利用できます。

クイック スタートのサンプル アプリケーションを完了した場合は、料金がかからないように、このクイック スタートで作成された Azure リソースを削除できます。 

> [!IMPORTANT]
> リソース グループの削除は元に戻せず、そのグループ内のすべてのリソースが含まれます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 このサンプルをホストするためのリソースを、保持したいリソースを含む既存のリソース グループ内に作成した場合は、リソース グループを削除する代わりに、各リソースをそのブレードから個別に削除できます。
> 
> 

[Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

**[名前でフィルター]** テキスト ボックスに、リソース グループの名前を入力します。 このクイックスタートの手順では、*SignalRTestResources* という名前のリソース グループを使用しました。 結果の一覧の中のリソース グループで、省略記号 ( **[...]** ) > **[リソース グループの削除]** を選択します。

   
![リソース グループを削除するための選択](./media/signalr-quickstart-dotnet-core/signalr-delete-resource-group.png)


リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにリソース グループの名前を入力し、 **[削除]** を選択します。
   
しばらくすると、リソース グループとそのリソースのすべてが削除されます。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、新しい Azure SignalR Service リソースを作成しました。 その後、それを ASP.NET Core Web アプリで使用して、コンテンツの更新を接続されている複数のクライアントにリアルタイムにプッシュしました。 Azure SignalR Service の使用についてさらに学習するには、認証をデモンストレーションするチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure SignalR Service の認証](./signalr-concept-authenticate-oauth.md)

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsnetcore)

