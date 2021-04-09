---
title: チュートリアル:Blazor Server チャット アプリを構築する - Azure SignalR
description: このチュートリアルでは、Azure SignalR Service を使用して Blazor Server アプリの構築と変更を行う方法について説明します。
author: JialinXin
ms.service: signalr
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jixin
ms.openlocfilehash: b0059e986b9a6ba8152a1a61f8d696f1caa4646a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97755907"
---
# <a name="tutorial-build-a-blazor-server-chat-app"></a>チュートリアル:Blazor Server チャット アプリを構築する

このチュートリアルでは、Blazor Server アプリの構築と変更を行う方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Blazor Server アプリで簡単なチャット ルームを作成する。
> * Razor コンポーネントに変更を加える。
> * コンポーネントのイベント処理とデータ バインディングを使用する。
> * Visual Studio で Azure App Service にすばやくデプロイする。
> * ローカルの SignalR を Azure SignalR Service に移行する。

## <a name="prerequisites"></a>前提条件
* [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) (バージョン 3.0.100 以上) をインストールする
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) (バージョン 16.3 以上) をインストールする
> Visual Studio 2019 プレビュー バージョンでも動作します。このリリースには、より新しい .Net Core バージョンを対象とした最新の Blazor Server アプリ テンプレートが含まれています。

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="build-a-local-chat-room-in-blazor-server-app"></a>Blazor Server アプリでローカル チャット ルームを作成する

Visual Studio 2019 バージョン 16.2.0 以降では、Azure SignalR Service が組み込みの Web アプリ発行プロセスとなり、Web アプリと SignalR Service との間の依存関係管理が従来よりもはるかに容易になっています。 ローカル開発環境でのローカル SignalR の作業と、Azure App Service 向けの Azure SignalR Service の作業を、コードに変更を加えることなく同時に行うことができます。

1. チャット Blazor アプリを作成する
   
   Visual Studio で [新しいプロジェクトの作成]、[Blazor アプリ] の順に選択します。次に、アプリに名前を付けてフォルダーを選択し、[Blazor Server アプリ] を選択します。 Visual Studio がターゲット フレームワークを正しく認識できるよう、あらかじめ .NET Core SDK 3.0 以上をインストールしておいてください。

   [ ![[新しいプロジェクトの作成] で、Blazor App テンプレートが選択されています。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-create.png#lightbox)
   
   または、コマンドを実行します
   ```dotnetcli
   dotnet new blazorserver -o BlazorChat
   ```
   
1. `BlazorChatSampleHub.cs` ファイルを追加して、チャットの `Hub` を実装します。
   
   ```cs
   using System;
   using System.Threading.Tasks;
   using Microsoft.AspNetCore.SignalR;
   
   namespace BlazorChat
   {
       public class BlazorChatSampleHub : Hub
       {
           public const string HubUrl = "/chat";
   
           public async Task Broadcast(string username, string message)
           {
               await Clients.All.SendAsync("Broadcast", username, message);
           }
   
           public override Task OnConnectedAsync()
           {
               Console.WriteLine($"{Context.ConnectionId} connected");
               return base.OnConnectedAsync();
           }
   
           public override async Task OnDisconnectedAsync(Exception e)
           {
               Console.WriteLine($"Disconnected {e?.Message} {Context.ConnectionId}");
               await base.OnDisconnectedAsync(e);
           }
       }
   }
   ```
   
1. `Startup.Configure()` にハブのエンドポイントを追加します。
   
   ```cs
   app.UseEndpoints(endpoints =>
   {
       endpoints.MapBlazorHub();
       endpoints.MapFallbackToPage("/_Host");
       endpoints.MapHub<BlazorChatSampleHub>(BlazorChatSampleHub.HubUrl);
   });
   ```
   
1. SignalR クライアントを使用するための `Microsoft.AspNetCore.SignalR.Client` パッケージをインストールします。

   ```dotnetcli
   dotnet add package Microsoft.AspNetCore.SignalR.Client --version 3.1.7
   ```

1. `Pages` フォルダーに `ChatRoom.razor` を作成して、SignalR クライアントを実装します。 次の手順に従うか、[ChatRoom.razor](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BlazorChat/Pages/ChatRoom.razor) をそのままコピーしてください。

   1. ページ リンクと参照を追加します。
      
      ```razor
      @page "/chatroom"
      @inject NavigationManager navigationManager
      @using Microsoft.AspNetCore.SignalR.Client;
      ```

   1. メッセージを送受信するためのコードを新しい SignalR クライアントに追加します。
      
      ```razor
      @code {
          // flag to indicate chat status
          private bool _isChatting = false;
          
          // name of the user who will be chatting
          private string _username;
      
          // on-screen message
          private string _message;
          
          // new message input
          private string _newMessage;
          
          // list of messages in chat
          private List<Message> _messages = new List<Message>();
          
          private string _hubUrl;
          private HubConnection _hubConnection;
      
          public async Task Chat()
          {
              // check username is valid
              if (string.IsNullOrWhiteSpace(_username))
              {
                  _message = "Please enter a name";
                  return;
              };
      
              try
              {
                  // Start chatting and force refresh UI.
                  _isChatting = true;
                  await Task.Delay(1);

                  // remove old messages if any
                  _messages.Clear();
         
                  // Create the chat client
                  string baseUrl = navigationManager.BaseUri;
      
                  _hubUrl = baseUrl.TrimEnd('/') + BlazorChatSampleHub.HubUrl;
      
                  _hubConnection = new HubConnectionBuilder()
                      .WithUrl(_hubUrl)
                      .Build();
      
                  _hubConnection.On<string, string>("Broadcast", BroadcastMessage);
      
                  await _hubConnection.StartAsync();
      
                  await SendAsync($"[Notice] {_username} joined chat room.");
              }
              catch (Exception e)
              {
                  _message = $"ERROR: Failed to start chat client: {e.Message}";
                  _isChatting = false;
              }
          }
      
          private void BroadcastMessage(string name, string message)
          {
              bool isMine = name.Equals(_username, StringComparison.OrdinalIgnoreCase);
      
              _messages.Add(new Message(name, message, isMine));
      
              // Inform blazor the UI needs updating
              StateHasChanged();
          }
      
          private async Task DisconnectAsync()
          {
              if (_isChatting)
              {
                  await SendAsync($"[Notice] {_username} left chat room.");
      
                  await _hubConnection.StopAsync();
                  await _hubConnection.DisposeAsync();
      
                  _hubConnection = null;
                  _isChatting = false;
              }
          }
      
          private async Task SendAsync(string message)
          {
              if (_isChatting && !string.IsNullOrWhiteSpace(message))
              {
                  await _hubConnection.SendAsync("Broadcast", _username, message);
      
                  _newMessage = string.Empty;
              }
          }
      
          private class Message
          {
              public Message(string username, string body, bool mine)
              {
                  Username = username;
                  Body = body;
                  Mine = mine;
              }
      
              public string Username { get; set; }
              public string Body { get; set; }
              public bool Mine { get; set; }
      
              public bool IsNotice => Body.StartsWith("[Notice]");
      
              public string CSS => Mine ? "sent" : "received";
          }
      }
      ```

   1. UI が SignalR クライアントと対話するためのレンダリング部分を `@code` の前に追加します。
      
      ```razor
      <h1>Blazor SignalR Chat Sample</h1>
      <hr />
      
      @if (!_isChatting)
      {
          <p>
              Enter your name to start chatting:
          </p>
      
          <input type="text" maxlength="32" @bind="@_username" />
          <button type="button" @onclick="@Chat"><span class="oi oi-chat" aria-hidden="true"></span> Chat!</button>
      
          // Error messages
          @if (_message != null)
          {
              <div class="invalid-feedback">@_message</div>
              <small id="emailHelp" class="form-text text-muted">@_message</small>
          }
      }
      else
      {
          // banner to show current user
          <div class="alert alert-secondary mt-4" role="alert">
              <span class="oi oi-person mr-2" aria-hidden="true"></span>
              <span>You are connected as <b>@_username</b></span>
              <button class="btn btn-sm btn-warning ml-md-auto" @onclick="@DisconnectAsync">Disconnect</button>
          </div>
          // display messages
          <div id="scrollbox">
              @foreach (var item in _messages)
              {
                  @if (item.IsNotice)
                  {
                      <div class="alert alert-info">@item.Body</div>
                  }
                  else
                  {
                      <div class="@item.CSS">
                          <div class="user">@item.Username</div>
                          <div class="msg">@item.Body</div>
                      </div>
                  }
              }
              <hr />
              <textarea class="input-lg" placeholder="enter your comment" @bind="@_newMessage"></textarea>
              <button class="btn btn-default" @onclick="@(() => SendAsync(_newMessage))">Send</button>
          </div>
      }
      ```

1. `NavMenu.razor` を更新し、次のようなチャット ルームの入口となるメニューを `NavMenuCssClass` の下に挿入します。

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="chatroom">
           <span class="oi oi-chat" aria-hidden="true"></span> Chat room
       </NavLink>
   </li>
   ```
   
1. `site.css` を更新して、チャット領域のバブル ビューを最適化します。 末尾に次のコードを追加します。

   ```css
   /* improved for chat text box */
   textarea {
       border: 1px dashed #888;
       border-radius: 5px;
       width: 80%;
       overflow: auto;
       background: #f7f7f7
   }
   
   /* improved for speech bubbles */
   .received, .sent {
       position: relative;
       font-family: arial;
       font-size: 1.1em;
       border-radius: 10px;
       padding: 20px;
       margin-bottom: 20px;
   }
   
   .received:after, .sent:after {
       content: '';
       border: 20px solid transparent;
       position: absolute;
       margin-top: -30px;
   }
   
   .sent {
       background: #03a9f4;
       color: #fff;
       margin-left: 10%;
       top: 50%;
       text-align: right;
   }
   
   .received {
       background: #4CAF50;
       color: #fff;
       margin-left: 10px;
       margin-right: 10%;
   }
   
   .sent:after {
       border-left-color: #03a9f4;
       border-right: 0;
       right: -20px;
   }
   
   .received:after {
       border-right-color: #4CAF50;
       border-left: 0;
       left: -20px;
   }
   
   /* div within bubble for name */
   .user {
       font-size: 0.8em;
       font-weight: bold;
       color: #000;
   }
   
   .msg {
       /*display: inline;*/
   }
   ```

1. <kbd>F5</kbd> キーを押して、アプリを実行します。 次のようにチャットできるようになります。

   [ ![Bob と Alice との間で交わされたチャットのアニメーションが表示されています。Alice が "Hello" と言うと、Bob が "Hi" と言います。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat.gif#lightbox)
   
[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="publish-to-azure"></a>Azure に発行する

   現時点で、Blazor アプリはローカルの SignalR で動作していますが、Azure App Service にデプロイするときは、[Azure SignalR Service](/aspnet/core/signalr/scale?view=aspnetcore-3.1#azure-signalr-service) の使用をお勧めします。そうすることで、Blazor Server アプリを多数のコンカレント SignalR 接続にスケールアップすることができます。 さらに、SignalR サービスのグローバル リーチと高パフォーマンスのデータ センターは、地理的条件による待機時間の短縮に役立ちます。

> [!IMPORTANT]
> Blazor Server アプリでは、UI の状態がサーバー側で維持されます。そのため、この場合サーバーの固定が必須となります。 アプリ サーバーが 1 台である場合、必然的にサーバーの固定が保証されます。 しかしアプリ サーバーが複数存在する場合、クライアントのネゴシエーションと接続の相手となるサーバーが変化し、Blazor アプリで UI エラーが生じる可能性があります。 そこで、次のように `appsettings.json` でサーバーの固定を有効にする必要があります。
> ```json
> "Azure:SignalR:ServerStickyMode": "Required"
> ```

1. プロジェクトを右クリックして、`Publish` に移動します。

   * ターゲット:Azure
   * 特定のターゲット: すべての種類の **Azure App Service** がサポートされています。
   * App Service: 新しいものを作成するか、既存のアプリ サービスを選択します。

   [ ![このアニメーションでは、[ターゲット] として [Azure] を選択し、[特定のターゲット] として [Azure App Service] を選択しています。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-profile.gif#lightbox)

1. Azure SignalR Service の依存関係を追加します

   発行プロファイルの作成後、 **[サービスの依存関係]** に推奨メッセージが表示されます。 このパネルで **[構成]** をクリックして、既存の Azure SignalR サービスを選択するか、新しく作成してください。

   [ ![[発行] で、[構成] へのリンクが強調表示されています。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency.png#lightbox)

   Azure SignalR Service が Azure 上にあればアプリがそれに自動的に切り替わるよう、サービス依存関係によって以下が実行されます。

   * Azure SignalR Service を使用するように [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) を更新する。
   * Azure SignalR Service NuGet パッケージの参照を追加する。
   * プロファイル プロパティを更新して、依存関係の設定を保存する。
   * 選択内容に応じてシークレット ストアを構成する。
   * 選択した Azure SignalR サービスがアプリの対象となるように `appsettings` 構成を追加する。

   [ ![[Summary of changes]\(変更の概要\) で、チェック ボックスを使用してすべての依存関係が選択されています。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-dependency-summary.png#lightbox)

1. アプリの発行

   以上で発行する準備は完了です。 発行が完了すると、ブラウザーでページが自動的に表示されます。 
   > [!NOTE]
   > 最初にページにアクセスしたときには、Azure App Service のデプロイが開始されるまでの待ち時間の関係で、すぐには正常に動作しない場合があります。しばらくしてから、ページを最新の情報に更新してみてください。
   > また、トラフィックが Azure SignalR Service にリダイレクト済みであるかどうかは、ブラウザーのデバッガー モード (<kbd>F12</kbd> キー) を使用して確認できます。

   [ ![Blazor SignalR Chat Sample には、自分の名前を入力するためのテキスト ボックスと、会話を開始するための [Chat!] ボタンがあります。](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png) ](media/signalr-tutorial-build-blazor-server-chat-app/blazor-chat-azure.png#lightbox)
   
[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="further-topic-enable-azure-signalr-service-in-local-development"></a>その他のトピック: ローカル開発で Azure SignalR Service を有効にする

1. Azure SignalR SDK への参照を追加します。

   ```dotnetcli
   dotnet add package Microsoft.Azure.SignalR --version 1.5.1
   ```

1. Azure SignalR Service の呼び出しを `Startup.ConfigureServices()` に追加します。

   ```cs
   public void ConfigureServices(IServiceCollection services)
   {
       ...
       services.AddSignalR().AddAzureSignalR();
       ...
   }
   ```

1. `appsetting.json` または[シークレット マネージャー](/aspnet/core/security/app-secrets?tabs=visual-studio&view=aspnetcore-3.1#secret-manager) ツールで、Azure SignalR Service の `ConnectionString` を構成します。

> [!NOTE]
> 手順 2 の代わりに、SignalR SDK への [`HostingStartupAssembly`](/aspnet/core/fundamentals/host/platform-specific-configuration?view=aspnetcore-3.1) を使用することもできます。
> 
> 1. Azure SignalR Service を有効にするための構成を `appsetting.json` に追加します。
>    ```js
>    "Azure": {
>      "SignalR": {
>        "Enabled": true,
>        "ConnectionString": <your-connection-string>
>      }
>    }
>    ```
> 
> 1. Azure SignalR SDK を使用するためのホスティング スタートアップ アセンブリを割り当てます。 `launchSettings.json` を編集し、`environmentVariables` 内に次のような構成を追加します。
>    ```js
>    "environmentVariables": {
>        ...,
>        "ASPNETCORE_HOSTINGSTARTUPASSEMBLIES": "Microsoft.Azure.SignalR"
>      }
>    ```

[問題がある場合は、お知らせください。](https://aka.ms/asrs/qsblazor)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースをクリーンアップするには、Azure portal を使用してリソース グループを削除します。

## <a name="next-steps"></a>次の手順

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Blazor Server アプリで簡単なチャット ルームを作成する。
> * Razor コンポーネントに変更を加える。
> * コンポーネントのイベント処理とデータ バインディングを使用する。
> * Visual Studio で Azure App Service にすばやくデプロイする。
> * ローカルの SignalR を Azure SignalR Service に移行する。

高可用性についての詳細を参照してください。
> [!div class="nextstepaction"]
> [回復性とディザスター リカバリー](signalr-concept-disaster-recovery.md)

## <a name="additional-resources"></a>その他のリソース

* [ASP.NET Core Blazor](/aspnet/core/blazor)
