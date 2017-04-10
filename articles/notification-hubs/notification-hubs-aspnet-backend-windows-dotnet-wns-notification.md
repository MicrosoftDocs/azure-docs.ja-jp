---
title: "Azure Notification Hubs と .NET バックエンドによるユーザーへの通知"
description: "Azure でセキュリティで保護されたプッシュ通知を送信する方法について説明します。 コード サンプルは .NET API を使用して C# で記述されています。"
documentationcenter: windows
author: ysxu
manager: erikre
services: notification-hubs
editor: 
ms.assetid: 012529f2-fdbc-43c4-8634-2698164b5880
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c0b963ef661612b1a176dd8e5f01d56e61eb5acb


---
# <a name="azure-notification-hubs-notify-users-with-net-backend"></a>Azure Notification Hubs と .NET バックエンドによるユーザーへの通知
[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>Overview
Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。 このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。 クライアントを認証するために ASP.NET WebAPI バックエンドが使用されます。 認証されたクライアント ユーザーを使用して、タグがバックエンドによって通知登録に自動的に追加されます。 このタグは、バックエンドが特定のユーザーに対する通知を生成するための送信で使用されます。 アプリ バックエンドを使用した通知登録の詳細については、ガイド トピック「[アプリ バックエンドからの登録](http://msdn.microsoft.com/library/dn743807.aspx)」をご覧ください。 このチュートリアルは、「[Notification Hubs の使用]」チュートリアルで作成した通知ハブを基に作成されています。

また、「 [安全なプッシュ] 」チュートリアルの前提条件でもあります。 このチュートリアルの手順を完了した後は、このチュートリアルのコードにプッシュ通知を安全に送信するための変更を加える方法を示した「 [安全なプッシュ] 」チュートリアルに進むことができます。

## <a name="before-you-begin"></a>開始する前に
マイクロソフトでは、お客様からのフィードバックを真剣に受け止めています。 このトピックを完了するうえで問題がある場合、またはこのコンテンツを改善するためのご提案がある場合には、ページの下部でフィードバックを送信できます。

このチュートリアルの完成したコードについては、GitHub の [こちら](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers)を参照してください。 

## <a name="prerequisites"></a>前提条件
このチュートリアルの前に、次の Mobile Services のチュートリアルを完了している必要があります。

* [Notification Hubs の使用]<br/>通知ハブを作成し、アプリ名を予約して、このチュートリアルの通知を受け取るための登録をします。 このチュートリアルでは、これらの手順を既に完了することを前提としています。 完了していない場合は、「[Notification Hubs の使用 (Windows ストア)](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」の手順に従ってください。特に、「[アプリを Windows ストアに登録する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#register-your-app-for-the-windows-store)」と「[通知ハブを構成する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub)」を完了してください。 特に、ポータルで通知ハブの **[構成]** タブに、**[パッケージ SID]** と **[クライアント シークレット]** の値を入力してください。 この構成手順は、セクション「[通知ハブを構成する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md#configure-your-notification-hub)」で説明しています。 これは重要な手順です。ポータルの資格情報が、選択したアプリケーション名に指定した資格情報と一致しない場合、プッシュ通知は成功しません。

> [!NOTE]
> バックエンド サービスとして Azure App Service で Mobile Apps を使用している場合は、このチュートリアルの [Mobile Apps バージョン](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md)に関するページをご覧ください。
> 
> 

&nbsp;

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-client-project"></a>クライアント プロジェクトのコードを更新する
このセクションでは、「 [Notification Hubs の使用] 」チュートリアルで完了したプロジェクトのコードを更新します。 コードは、既にストアに関連付けられ、通知ハブ用に構成されているはずです。 このセクションでは、新しい WebAPI バックエンドを呼び出すコードを追加し、それを使用して通知の登録と送信を行います。

1. Visual Studio で、「 [Notification Hubs の使用] 」チュートリアルで作成したソリューションを開きます。
2. ソリューション エクスプローラーで、**(Windows 8.1)** プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。
3. 左側で、 **[オンライン]**をクリックします。
4. **[検索]** ボックスに、「**Http Client**」と入力します。
5. 結果の一覧で、**[Microsoft HTTP Client Libraries]**、**[インストール]** の順にクリックします。 インストールを完了します。
6. NuGet **[検索]** ボックスに戻り、「**Json.net**」と入力します。 **Json.NET** パッケージをインストールし、NuGet パッケージ マネージャーのウィンドウを閉じます。
7. **(Windows Phone 8.1)** プロジェクトに対して上の手順を繰り返して、Windows Phone プロジェクト用の **JSON.NET** NuGet パッケージをインストールします。
8. ソリューション エクスプローラーで、**(Windows Phone 8.1)** プロジェクトの **MainPage.xaml** をダブルクリックして Visual Studio エディターで開きます。
9. **MainPage.xaml XML** コードの `<Grid>` セクションを次のコードで置き換えます。 このコードは、ユーザーを認証するためのユーザー名とパスワードのテキスト ボックスを追加します。 さらに、通知メッセージと通知を受け取るユーザー名タグのテキスト ボックスも追加します。
   
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>
   
            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>
   
            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
   
                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>
   
                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />
   
                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>
10. ソリューション エクスプローラーで、**(Windows Phone 8.1)** プロジェクトの **MainPage.xaml** を開き、Windows Phone 8.1 の `<Grid>` セクションを上記と同じコードで置き換えます。 次のようなインターフェイスが表示されます。
    
    ![][13]
11. ソリューション エクスプローラーで、**(Windows 8.1)** プロジェクトと **(Windows Phone 8.1)** プロジェクトの **MainPage.xaml.cs** ファイルを開きます。 次の `using` ステートメントを両方のファイルの先頭に追加します。
    
        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;
        using System.Threading.Tasks;
12. **(Windows 8.1)** プロジェクトと **(Windows Phone 8.1)** プロジェクトの **MainPage.xaml.cs** で、次のメンバーを `MainPage` クラスに追加します。 必ず、前に取得したバックエンド エンドポイントで `<Enter Your Backend Endpoint>` を置き換えてください。 たとえば、「 `http://mybackend.azurewebsites.net`」のように入力します。
    
        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
13. 次のコードを、**(Windows 8.1)** プロジェクトと **(Windows Phone 8.1)** プロジェクトの **MainPage.xaml.cs** 内の MainPage クラスに追加します。
    
    `PushClick` メソッドは、 **[プッシュを送信する]** ボタン用のクリック ハンドラーです。 それは、バックエンドを呼び出して、ユーザー名タグが `to_tag` パラメーターと一致するすべてのデバイスへの通知をトリガーします。 通知メッセージは、要求本文で JSON コンテンツとして送信されます。
    
    `LoginAndRegisterClick` メソッドは、 **[ログインして登録]** ボタン用のクリック ハンドラーです。 それは、基本認証トークンをローカル ストレージに保存し (これは認証スキームが使用する任意のトークンを表すことに注意してください)、その後で `RegisterClient` を使用して、バックエンドを使用する通知を登録します。

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                // The device handle used will be different depending on the device and PNS. 
                // Windows devices use the channel uri as the PNS handle.
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



1. ソリューション エクスプローラーで、**共有**プロジェクトの **App.xaml.cs** ファイルを開きます。  `InitNotificationsAsync()` in the `OnLaunched()` への呼び出しを見つけます。 `InitNotificationsAsync()`への呼び出しをコメント アウトするか削除します。 上で追加したボタン ハンドラーが通知登録を初期化します。

        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


1. ソリューション エクスプローラーで、**共有**プロジェクトを右クリックし、**[追加]**、**[クラス]** の順にクリックします。 クラスに **RegisterClient.cs** という名前を付け、**[OK]** をクリックしてクラスを生成します。
   
   このクラスは、アプリ バックエンドに接続してプッシュ通知を登録するために必要な REST 呼び出しをラップします。 「 *アプリ バックエンドからの登録* 」で説明しているとおり、Notification Hubs によって作成された [registrationIds](http://msdn.microsoft.com/library/dn743807.aspx)もローカルに格納されます。 **[ログインして登録]** をクリックすると、ローカル ストレージに格納した認証トークンが使用されることに注意してください。
2. 次の `using` ステートメントを、RegisterClient.cs file ファイルの先頭に追加します。
   
       using Windows.Storage;
       using System.Net;
       using System.Net.Http;
       using System.Net.Http.Headers;
       using Newtonsoft.Json;
       using System.Threading.Tasks;
       using System.Linq;
3. `RegisterClient` クラス定義内で、次のコードを追加します。
   
       private string POST_URL;
   
       private class DeviceRegistration
       {
           public string Platform { get; set; }
           public string Handle { get; set; }
           public string[] Tags { get; set; }
       }
   
       public RegisterClient(string backendEndpoint)
       {
           POST_URL = backendEndpoint + "/api/register";
       }
   
       public async Task RegisterAsync(string handle, IEnumerable<string> tags)
       {
           var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
   
           var deviceRegistration = new DeviceRegistration
           {
               Platform = "wns",
               Handle = handle,
               Tags = tags.ToArray<string>()
           };
   
           var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
   
           if (statusCode == HttpStatusCode.Gone)
           {
               // regId is expired, deleting from local storage & recreating
               var settings = ApplicationData.Current.LocalSettings.Values;
               settings.Remove("__NHRegistrationId");
               regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
               statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
           }
   
           if (statusCode != HttpStatusCode.Accepted)
           {
               // log or throw
               throw new System.Net.WebException(statusCode.ToString());
           }
       }
   
       private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
       {
           using (var httpClient = new HttpClient())
           {
               var settings = ApplicationData.Current.LocalSettings.Values;
               httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);
   
               var putUri = POST_URL + "/" + regId;
   
               string json = JsonConvert.SerializeObject(deviceRegistration);
                               var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
               return response.StatusCode;
           }
       }
   
       private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
       {
           var settings = ApplicationData.Current.LocalSettings.Values;
           if (!settings.ContainsKey("__NHRegistrationId"))
           {
               using (var httpClient = new HttpClient())
               {
                   httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);
   
                   var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                   if (response.IsSuccessStatusCode)
                   {
                       string regId = await response.Content.ReadAsStringAsync();
                       regId = regId.Substring(1, regId.Length - 2);
                       settings.Add("__NHRegistrationId", regId);
                   }
                   else
                   {
                       throw new System.Net.WebException(response.StatusCode.ToString());
                   }
               }
           }
           return (string)settings["__NHRegistrationId"];
   
       }
4. すべての変更を保存します。

## <a name="testing-the-application"></a>アプリケーションのテスト
1. Windows 8.1 と Windows Phone 8.1 の両方でアプリケーションを起動します。 Windows Phone 8.1 では、エミュレーターまたは実際のデバイスでインスタンスを実行できます。
2. アプリの Windows 8.1 インスタンスで、次の画面に示すように、**ユーザー名**と**パスワード**を入力します。 Windows Phone で入力するユーザー名とパスワードとは異なるユーザー名とパスワードを入力する必要があります。
3. **[ログインして登録]** をクリックし、ログインしたことを示すダイアログを確認します。 **[プッシュを送信する]** ボタンも有効になります。
   
    ![][14]
4. Windows Phone 8.1 インスタンスで、**[ユーザー名]** フィールドと **[パスワード]** フィールドの両方にユーザー名文字列を入力し、**[ログインして登録]** をクリックします。
5. 次に、 **[受信ユーザー タグ]** フィールドに、Windows 8.1 に登録されているユーザー名を入力します。 通知メッセージを入力し、 **[プッシュを送信する]**をクリックします。
   
    ![][16]
6. 一致するユーザー名タグが登録されているデバイスだけが通知メッセージを受信します。
   
    ![][15]

## <a name="next-steps"></a>次のステップ
* 対象グループごとにユーザーを区分する場合は、「 [通知ハブを使用したニュース速報の送信]」を参照してください。
* Notification Hubs の使用方法の詳細については、「 [Notification Hubs の概要]」を参照してください。

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Notification Hubs の使用]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[安全なプッシュ]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx



<!--HONumber=Nov16_HO3-->


