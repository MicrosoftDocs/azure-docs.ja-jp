---
title: インクルード ファイル
description: バックエンドの ASP .NET WebAPI プロジェクトを作成するためのコードが含まれるファイルをインクルードします。
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 590ba4b7a61fa437767d99ac6b9ae3e0fa94edc3
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227795"
---
## <a name="create-the-webapi-project"></a>Web API プロジェクトを作成する

次のセクションでは、新しい ASP.NET WebAPI バックエンドの作成について説明します。 このプロセスには、主に次の 3 つの目的があります。

- **クライアントの認証**: クライアント要求を認証し、ユーザーを要求と関連付けるメッセージ ハンドラーを追加します。
- **WebAPI バックエンドを使用した通知の登録**: クライアント デバイスで通知を受信するための新しい登録を処理するコントローラーを追加します。 認証されたユーザー名は[タグ](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md)として自動的に登録に追加されます。
- **クライアントへの通知の送信**: ユーザーがタグに関連するデバイスやクライアントにセキュリティで保護されたプッシュ通知をトリガーできるコントローラーを追加します。

次の操作を実行して、新しい ASP.NET WebAPI バックエンドを作成します。

> [!IMPORTANT]
> Visual Studio 2015 またはそれ以前のバージョンを使用している場合は、このチュートリアルを始める前に、Visual Studio 用の最新の NuGet パッケージ マネージャーがインストールされていることを確認してください。
>
>確認するには、Visual Studio を起動します。 **[ツール]** メニューの **[拡張機能と更新プログラム]** を選びます。 お使いの Visual Studio に対応した **NuGet パッケージ マネージャー**を探し、バージョンが最新であることを確認します。 最新バージョンでない場合は、アンインストールして、NuGet パッケージ マネージャーを再インストールしてください。

![][B4]

> [!NOTE]
> Web サイトのデプロイ用に Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) がインストールされていることを確認してください。

1. Visual Studio または Visual Studio Express を起動します。

2. **[サーバー エクスプローラー]** を選択し、Azure アカウントにサインインします。 アカウントの Web サイト リソースを作成するには、サインインする必要があります。

3. Visual Studio で Visual Studio ソリューションを右クリックし、 **[追加]** をポイントして、 **[新しいプロジェクト]** をクリックします。
4. **[Visual C#]** を展開し、 **[Web]** を選択して **[ASP.NET Web アプリケーション]** をクリックします。

5. **[名前]** ボックスに「**AppBackend**」と入力し、 **[OK]** を選択します。

    ![[新しいプロジェクト] ウィンドウ][B1]

6. **[新しい ASP.NET プロジェクト]** ウィンドウで、 **[Web API]** チェック ボックスをオンにし、 **[OK]** を選択します。

    ![[新しい ASP.NET プロジェクト] ウィンドウ][B2]

7. **[Microsoft Azure Web App の構成]** ウィンドウで、サブスクリプションを選択し、 **[App Service プラン]** の一覧で次のいずれかの操作を実行します。

    * 作成済みの App Service プランを選択します。
    * **[新しい App Service プランの作成]** を選択し、App Service プランを作成します。

   このチュートリアルではデータベースは必要ありません。 App Service プランを選択したら、 **[OK]** を選択して、プロジェクトを作成します。

    ![[Microsoft Azure Web App の構成] ウィンドウ][B5]

    App Service プランを構成するためのこのページが表示されない場合、チュートリアルを続行してください。 これはアプリの発行時に後で構成できます。 

## <a name="authenticate-clients-to-the-webapi-backend"></a>WebAPI バックエンドに対してクライアントを認証する

このセクションでは、新しいバックエンドに対して **AuthenticationTestHandler** という名前の新しいメッセージ ハンドラー クラスを作成します。 このクラスは [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) から派生し、メッセージ ハンドラーとして追加されるため、バックエンドに送信されるすべての要求を処理できます。

1. ソリューション エクスプローラーで、**AppBackend** プロジェクトを右クリックし、 **[追加]** 、 **[クラス]** の順に選択します。
2. 新しいクラスに「**AuthenticationTestHandler.cs**」という名前を付け、 **[追加]** を選択して、クラスを生成します。 説明を簡単にするために、このクラスでは、*基本認証*を使用してユーザーを認証します。 実際のアプリでは、任意の認証スキームを使用できます。
3. AuthenticationTestHandler.cs に次の `using` ステートメントを追加します。

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. AuthenticationTestHandler.cs で、`AuthenticationTestHandler` クラス定義を次のコードに置き換えます。

    このハンドラーは、次の 3 つの条件を満たす場合に要求を承認します。

   * 要求に *Authorization* ヘッダーが含まれている。
   * 要求に *基本* 認証が使用されている。
   * ユーザー名の文字列とパスワードの文字列が同じである。

   それ以外の場合、要求は拒否されます。 この認証は、認証と認可の正規のアプローチではありません。 このチュートリアルのための単なる例にすぎません。

   要求メッセージが認証され、`AuthenticationTestHandler` によって承認される場合、基本認証ユーザーは [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx) の現在の要求に添付されます。 HttpContext のユーザー情報は、後で別のコントローラー (RegisterController) で使用され、通知登録の要求に[タグ](https://msdn.microsoft.com/library/azure/dn530749.aspx)を追加します。

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
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
    ```

    > [!NOTE]
    > セキュリティに関する注意: `AuthenticationTestHandler` クラスは、本当の認証を提供するわけではありません。 基本認証を模倣するためだけに使用されるため、安全ではありません。 実稼働のアプリケーションとサービスでは、セキュリティで保護された認証メカニズムを実装する必要があります。
5. メッセージ ハンドラーを登録するには、**App_Start/WebApiConfig.cs** クラスの `Register` メソッドの末尾に次のコードを追加します。

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. 変更を保存します。

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>WebAPI バックエンドを使用して通知に登録する

このセクションでは、Notification Hubs のクライアント ライブラリを使用して、通知用にユーザーとデバイスを登録する要求を処理する新しいコントローラーを WebAPI バックエンドに追加します。 コントローラーでは、`AuthenticationTestHandler` で認証され、HttpContext に添付されたユーザーのユーザー タグを追加します。 タグは、`"username:<actual username>"` という形式の文字列になります。

1. ソリューション エクスプローラーで **AppBackend** プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。

2. 左側のウィンドウにある **[オンライン]** を選択し、 **[検索]** ボックスに「**Microsoft.Azure.NotificationHubs**」と入力します。

3. 結果の一覧で、 **[Microsoft Azure Notification Hubs]** を選択してから、 **[インストール]** を選択します。 インストールが完了したら、NuGet パッケージ マネージャーのウィンドウを閉じます。

    この操作によって、[Microsoft.Azure.Notification Hubs NuGet パッケージ](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)を使用して Azure Notification Hubs SDK に参照が追加されます。

4. 通知の送信に使用する通知ハブとの接続を表す新しいクラス ファイルを作成します。 ソリューション エクスプローラーで、**Models** フォルダーを右クリックし、 **[追加]** 、 **[クラス]** の順に選択します。 新しいクラスに「**Notifications.cs**」という名前を付け、 **[追加]** を選択して、クラスを生成します。

    ![[新しい項目の追加] ウィンドウ][B6]

5. Notifications.cs で、ファイルの先頭に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. `Notifications` クラス定義を以下のコードに置き換えます。2 つのプレースホルダーは、通知ハブに対する (フル アクセス権を持つ) 接続文字列と、ハブ名 ([Azure Portal](https://portal.azure.com)で確認できます) に置き換えます。

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
7. 次に、**RegisterController** という名前の新しいコントローラーを作成します。 ソリューション エクスプローラーで、**Controllers** フォルダーを右クリックし、 **[追加]** 、 **[コントローラー]** の順に選択します。

8. **[Web API 2 コントローラー - 空]** 、 **[追加]** の順に選択します。

    ![[スキャフォールディングの追加] ウィンドウ][B7]

9. **[コントローラー名]** ボックスに「**RegisterController**」と入力して、新しいクラスに名前を付け、 **[追加]** を選択します。

    ![[コントローラーの追加] ウィンドウ][B8]

10. RegisterController.cs に次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. `RegisterController` クラス定義内で、次のコードを追加します。 このコードでは、HttpContext に添付されるユーザーのユーザー タグを追加します。 ユーザーは認証され、追加したメッセージ フィルター `AuthenticationTestHandler` で HttpContext に添付されます。 また、要求されたタグを登録する権限をユーザーが持っていることを確認するコードをオプションで追加することもできます。

    ```csharp
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
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
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
    ```
12. 変更を保存します。

## <a name="send-notifications-from-the-webapi-backend"></a>WebAPI バックエンドから通知を送信する

このセクションでは、クライアント デバイスが通知を送信する方法を公開する新しいコントローラーを追加します。 この通知は、ASP.NET WebAPI バックエンドの Azure Notification Hubs .NET ライブラリを使用する username タグに基づいています。

1. 前のセクションで **RegisterController** を作成したときと同じ方法で、**NotificationsController** という別の新しいコントローラーを作成します。

2. NotificationsController.cs に次の `using` ステートメントを追加します。

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. **NotificationsController** クラスに次のメソッドを追加します。

    このコードでは、プラットフォーム通知サービス (PNS) の `pns` パラメーターに基づく通知の種類を送信します。 `to_tag` の値はメッセージの *ユーザー名* タグを設定するために使用します。 このタグは、アクティブな通知ハブ登録のユーザー名のタグと一致する必要があります。 通知メッセージは、POST 要求の本文からプルされ、ターゲット PNS に合わせた形式に設定されます。

    サポートされているデバイスで通知の受信に使用される PNS に応じて、各種形式で通知がサポートされています。 たとえば Windows デバイスの場合、別の PNS で直接はサポートされていない [WNS によるトースト通知](https://msdn.microsoft.com/library/windows/apps/br230849.aspx)を使用することもできます。 このような場合、バックエンドが、通知を、サポートを計画しているデバイスの PNS でサポートされている形式に設定する必要があります。 その後、[NotificationHubClient クラス](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)で適切な送信 API を使用します。

    ```csharp
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
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
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
    ```
4. アプリケーションを実行し、ここまでの作業に問題がないことを確認するために、**F5** キーを選択します。 アプリにより、Web ブラウザーが開き、ASP.NET ホーム ページに表示されます。

## <a name="publish-the-new-webapi-backend"></a>新しい WebAPI バックエンドを発行する

次に、このアプリを Azure の Web サイトにデプロイして、すべてのデバイスからアクセスできるようにします。

1. **AppBackend** プロジェクトを右クリックして **[発行]** を選択します。

2. 発行先として **[Microsoft Azure App Service]** を選択し、[発行] を選択します。 [App Service の作成] ウィンドウが開きます。 このウィンドウでは、Azure で ASP.NET Web アプリを実行するために必要なすべての Azure リソースを作成できます。

    ![[Microsoft Azure App Service] タイル][B15]

3. **[App Service の作成]** ウィンドウで、Azure アカウントを選択します。 **[変更の種類]**  >  **[Web アプリ]** の順に選択します。 既定の **Web アプリ名**をそのまま保持し、 **[サブスクリプション]** 、 **[リソース グループ]** 、 **[App Service プラン]** の順に選択します。

4. **作成** を選択します。

5. **[概要]** セクションの **[サイト URL]** プロパティをメモします。 この URL は、このチュートリアルの後半で使用する "*バックエンドエンドポイント*" です。

6. **[発行]** を選択します。

ウィザードの完了後に、Azure に ASP.NET Web アプリを発行してから、既定のブラウザーでアプリを開きます。  アプリケーションが Azure App Services に表示されます。

URL では、前に指定した Web アプリ名が http://<アプリ名>.azurewebsites.net という形式で使用されます。

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
