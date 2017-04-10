## <a name="create-the-webapi-project"></a>Web API プロジェクトを作成する
新しい ASP.NET WebAPI バックエンドを次のセクションで作成します。その目的には主に次の 3 つがあります。

1. **クライアントの認証**: クライアント要求を認証し、ユーザーを要求と関連付けるメッセージ ハンドラーが後で追加されます。
2. **クライアント通知の登録**: その後で、クライアント デバイスで通知を受信するための新しい登録を処理するコントローラーを追加します。 認証されたユーザー名は [タグ](https://msdn.microsoft.com/library/azure/dn530749.aspx)として自動的に登録に追加されます。
3. **クライアントへの通知の送信**: さらに、ユーザーがタグに関連するデバイスやクライアントにセキュリティで保護されたプッシュ通知をトリガーできるコントローラーも追加します。 

次の手順では、新しい ASP.NET WebAPI バックエンドを作成する方法を説明します。 

> [!NOTE]
> **重要**: このチュートリアルを始める前に、最新の NuGet パッケージ マネージャーがインストールされていることを確認してください。 確認するには、Visual Studio を起動します。 **[ツール]** メニューの **[拡張機能と更新プログラム]** をクリックします。 **NuGet Package Manager for Visual Studio 2013**を探し、バージョンが 2.8.50313.46 以降であることを確認します。 違う場合は、アンインストールしてから、NuGet パッケージ マネージャーをもう一度インストールしてください。
> 
> ![][B4]
> 
> [!NOTE]
> Web サイトのデプロイ用に Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) がインストールされていることを確認してください。
> 
> 

1. Visual Studio または Visual Studio Express を起動します。 **[サーバー エクスプローラー]** をクリックして Azure アカウントにサインインします。 Visual Studio でアカウントの Web サイト リソースを作成するには、サインインする必要があります。
2. Visual Studio で、**[ファイル]**、**[新規]**、**[プロジェクト]** の順にクリックし、**[テンプレート]**、**[Visual C#]** の順に展開します。次に、**[Web]**、**[ASP.NET Web アプリケーション]** の順にクリックし、「**AppBackend**」という名前を入力して、**[OK]** をクリックします。 
   
    ![][B1]
3. **[新しい ASP.NET プロジェクト]** ダイアログ ボックスで **[Web API]**、**[OK]** の順にクリックします。
   
    ![][B2]
4. **[Microsoft Azure Web App の構成]** ダイアログで、サブスクリプションと、既に作成した **App Service プラン**を選択します。 また、 **[新しい App Service プランの作成]** を選択してダイアログからプランを作成することもできます。 このチュートリアルではデータベースは必要ありません。 App Service プランを選択したら、 **[OK]** をクリックしてプロジェクトを作成します。
   
    ![][B5]

## <a name="authenticating-clients-to-the-webapi-backend"></a>WebAPI バックエンドでクライアントを認証する
このセクションでは、新しいバックエンドに対して **AuthenticationTestHandler** という名前の新しいメッセージ ハンドラー クラスを作成します。 このクラスは [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) から派生し、メッセージ ハンドラーとして追加されるため、バックエンドに送信されるすべての要求を処理できます。 

1. ソリューション エクスプローラーで、**AppBackend** プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスに「**AuthenticationTestHandler.cs**」という名前を付け、**[追加]** をクリックして、クラスを生成します。 説明を簡単にするために、このクラスを使用して *基本認証* でユーザーを認証します。 実際のアプリでは、任意の認証スキームを使用できます。
2. AuthenticationTestHandler.cs に次の `using` ステートメントを追加します。
   
        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;
3. AuthenticationTestHandler.cs で、 `AuthenticationTestHandler` クラス定義を次のコードに置き換えます。 
   
    このハンドラーは、次の 3 つの条件すべてを満たす場合に要求を承認します。
   
   * 要求に *Authorization* ヘッダーが含まれている。 
   * 要求に "基本" ** 認証が使用されている。 
   * ユーザー名の文字列とパスワードの文字列が同じである。
     
     それ以外の場合、要求は拒否されます。 これは正規の認証や認証アプローチではありません。 このチュートリアルでの非常に単純な例です。
     
     要求メッセージが認証され、 `AuthenticationTestHandler`によって承認される場合、基本認証ユーザーは [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx)の現在の要求に添付されます。 HttpContext のユーザー情報は、後で別のコントローラー (RegisterController) で使用され、通知登録の要求に [タグ](https://msdn.microsoft.com/library/azure/dn530749.aspx) を追加します。
     
       public class AuthenticationTestHandler : DelegatingHandler   {
     
           protected override Task<HttpResponseMessage> SendAsync(
           HttpRequestMessage request, CancellationToken cancellationToken)
           {
               var authorizationHeader = request.Headers.GetValues("Authorization").First();
     
               if (authorizationHeader != null && authorizationHeader
                   .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
               {
                   string authorizationUserAndPwdBase64 =
                       authorizationHeader.Substring("Basic ".Length);
                   string authorizationUserAndPwd = Encoding.Default
                       .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                   string user = authorizationUserAndPwd.Split(':')[0];
                   string password = authorizationUserAndPwd.Split(':')[1];
     
                   if (verifyUserAndPwd(user, password))
                   {
                       // Attach the new principal object to the current HttpContext object
                       HttpContext.Current.User =
                           new GenericPrincipal(new GenericIdentity(user), new string[0]);
                       System.Threading.Thread.CurrentPrincipal =
                           System.Web.HttpContext.Current.User;
                   }
                   else return Unauthorized();
               }
               else return Unauthorized();
     
               return base.SendAsync(request, cancellationToken);
           }
     
           private bool verifyUserAndPwd(string user, string password)
           {
               // This is not a real authentication scheme.
               return user == password;
           }
     
           private Task<HttpResponseMessage> Unauthorized()
           {
               var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
               var tsc = new TaskCompletionSource<HttpResponseMessage>();
               tsc.SetResult(response);
               return tsc.Task;
           }
       }
     
     > [!NOTE]
     > **セキュリティに関する注意**: `AuthenticationTestHandler` クラスは、本当の認証を提供するわけではありません。 基本認証を模倣するためだけに使用されるため、安全ではありません。 実稼働のアプリケーションとサービスでは、セキュリティで保護された認証メカニズムを実装する必要があります。                
     > 
     > 
4. **App_Start/WebApiConfig.cs** クラスの `Register` メソッドの末尾に、次のコードを追加してメッセージ ハンドラーを登録します。
   
        config.MessageHandlers.Add(new AuthenticationTestHandler());
5. 変更を保存します。

## <a name="registering-for-notifications-using-the-webapi-backend"></a>WebAPI バックエンドを使用して通知を登録する
このセクションでは、Notification Hubs のクライアント ライブラリを使用して、通知用にユーザーとデバイスを登録する要求を処理する新しいコントローラーを WebAPI バックエンドに追加します。 コントローラーでは、 `AuthenticationTestHandler`で認証され、HttpContext に添付されたユーザーにユーザー タグを追加します。 タグは、文字列の形式 `"username:<actual username>"`になります。

1. ソリューション エクスプローラーで **AppBackend** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
2. 左側にある **[オンライン]** をクリックし、**[検索]** ボックスで「**Microsoft.Azure.NotificationHubs**」を検索します。
3. 結果の一覧で、**[Microsoft Azure Notification Hubs]** をクリックしてから、**[インストール]** をクリックします。 インストールが完了したら、NuGet パッケージ マネージャーのウィンドウを閉じます。
   
    これにより <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet パッケージ</a>を利用して Azure Notification Hubs SDK に参照が追加されます。
4. 次は、通知の送信に使用する通知ハブとの接続を表す新しいクラス ファイルを作成します。 ソリューション エクスプローラーで、**Models** フォルダーを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 新しいクラスに「**Notifications.cs**」という名前を付け、**[追加]** をクリックして、クラスを生成します。 
   
    ![][B6]
5. Notifications.cs で、ファイルの先頭に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.NotificationHubs;
6. 次に、`Notifications` クラス定義を以下に置き換えます。2 つのプレースホルダーは、通知ハブに対する (フル アクセス権を持つ) 接続文字列と、ハブ名 ([Azure クラシック ポータル](http://manage.windowsazure.com)で確認できます) に置き換えてください。
   
        public class Notifications
        {
            public static Notifications Instance = new Notifications();
   
            public NotificationHubClient Hub { get; set; }
   
            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
                                                                             "<hub name>");
            }
        }
7. 次に **RegisterController**という名前の新しいコントローラーを作成します。 ソリューション エクスプローラーで、**Controllers** フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順にクリックします。 **[Web API 2 コントローラー -- 空]** 項目をクリックし、**[追加]** をクリックします。 新しいクラスに「**RegisterController**」という名前を付け、**[追加]** をもう一度クリックして、コントローラーを生成します。
   
    ![][B7]
   
    ![][B8]
8. RegisterController.cs に次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
9. `RegisterController` クラス定義内で、次のコードを追加します。 このコードでは、HttpContext に添付されるユーザーのユーザー タグを追加することに注意してください。 ユーザーは認証され、追加したメッセージ フィルター `AuthenticationTestHandler`で HttpContext に添付されます。 また、要求されたタグを登録する権限をユーザーが持っていることを確認するコードをオプションで追加することもできます。
   
        private NotificationHubClient hub;
   
        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }
   
        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }
   
        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            string newRegistrationId = null;
   
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);
   
                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }
   
            if (newRegistrationId == null) 
                newRegistrationId = await hub.CreateRegistrationIdAsync();
   
            return newRegistrationId;
        }
   
        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }
   
            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;
   
            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);
   
            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }
10. 変更を保存します。

## <a name="sending-notifications-from-the-webapi-backend"></a>WebAPI バックエンドから通知を送信する
このセクションでは、ASP.NET WebAPI バックエンドの Azure Notification Hubs サービス管理ライブラリを使用して、ユーザー名タグに基づく通知を送信する手段をクライアント デバイスに提供する新しいコントローラーを追加します。

1. **NotificationsController**という名前の新しいコントローラーを別に作成します。 前のセクションで **RegisterController** を作成したときと同じ方法で作成します。
2. NotificationsController.cs に次の `using` ステートメントを追加します。
   
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;
3. **NotificationsController** クラスに次のメソッドを追加します。
   
    このコードでは、プラットフォーム通知サービス (PNS) の `pns` パラメーターに基づく通知の種類を送信します。 `to_tag` の値はメッセージの *ユーザー名* タグを設定するために使用します。 このタグは、アクティブな通知ハブ登録のユーザー名のタグと一致する必要があります。 通知メッセージは、POST 要求の本文からプルされ、ターゲット PNS に合わせた形式に設定されます。 
   
    サポートされているデバイスで通知の受信に使用されるプラットフォーム通知サービス (PNS) に応じて、各種形式を使用したさまざまな通知がサポートされています。 たとえば Windows デバイスの場合、別の PNS で直接はサポートされていない [WNS によるトースト通知](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) を使用することもできます。 したがって、バックエンドが、通知を、サポートを計画しているデバイスの PNS でサポートされている形式に設定する必要があります。 その後、 [NotificationHubClient クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)
   
        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;
   
            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;
   
            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }
   
            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }
   
            return Request.CreateResponse(ret);
        }
4. **F5** キーを押して、アプリケーションを実行し、ここまでの作業に問題がないことを確認します。 アプリにより、Web ブラウザーが起動し、ASP.NET ホーム ページが表示される必要があります。 

## <a name="publish-the-new-webapi-backend"></a>新しい WebAPI バックエンドを発行する
1. 次に、このアプリを Azure の Web サイトにデプロイして、すべてのデバイスからアクセスできるようにします。 **AppBackend** プロジェクトを右クリックして **[発行]** を選択します。
2. 発行先として **Microsoft Azure Web Apps** を選択します。
   
    ![][B15]
3. Azure アカウントでログインし、既存または新規の Web アプリを選択します。
   
    ![][B16]
4. **[接続]** タブの **[宛先 URL]** プロパティをメモしておきます。 後で、この URL を *バックエンド エンドポイント* として参照します。 **[発行]**をクリックします。
   
    ![][B18]

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG


<!--HONumber=Nov16_HO3-->


