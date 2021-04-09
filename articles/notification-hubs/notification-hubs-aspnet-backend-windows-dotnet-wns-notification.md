---
title: Azure Notification Hubs を使用して特定のユーザーに通知を送信する | Microsoft Docs
description: ユニバーサル Windows プラットフォーム (UWP) アプリケーションを使用して特定のユーザーに通知を送信する方法について説明します。
documentationcenter: windows
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 97a6a45ab01fc113b79a48ba7fcb246d528684be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019482"
---
# <a name="tutorial-send-notifications-to-specific-users-by-using-azure-notification-hubs"></a>チュートリアル:Azure Notification Hubs を使用して特定のユーザーに通知を送信する

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

## <a name="overview"></a>概要

このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。 クライアントを認証するために ASP.NET WebAPI バックエンドが使用されます。 バックエンドは、クライアント アプリケーション ユーザーを認証すると、通知の登録にタグを自動的に追加します。 バックエンドは、このタグを使用して特定のユーザーに通知を送信します。

> [!NOTE]
> このチュートリアルの完成したコードは、[GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers) にあります。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Web API プロジェクトを作成する
> * WebAPI バックエンドに対してクライアントを認証する
> * WebAPI バックエンドを使用して通知に登録する
> * WebAPI バックエンドから通知を送信する
> * 新しい WebAPI バックエンドを発行する
> * クライアント プロジェクトのコードを更新する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルは、「[チュートリアル: Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」のチュートリアルで完成したプロジェクト内のコードを更新します。 そのため、このチュートリアルを開始する前に、それを完了してください。

> [!NOTE]
> バックエンド サービスとして Azure App Service で Mobile Apps を使用している場合は、このチュートリアルの [Mobile Apps バージョン](/previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push)に関するページをご覧ください。

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="update-the-code-for-the-uwp-client"></a>UWP クライアントのコードを更新する

このセクションでは、「[チュートリアル: Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」のチュートリアルで完成したプロジェクト内のコードを更新します。 このプロジェクトは、既に Windows ストアに関連付けられている必要があります。 また、通知ハブを使用するように構成されている必要があります。 このセクションでは、新しい WebAPI バックエンドを呼び出すコードを追加し、それを通知の登録および送信に使用します。

1. Visual Studio で、「[チュートリアル: Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)」で作成したソリューションを開きます。
2. ソリューション エクスプローラーで、ユニバーサル Windows プラットフォーム (UWP) プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックします。
3. 左側で、 **[参照]** を選択します。
4. **[検索]** ボックスに、「**Http Client**」と入力します。
5. 結果の一覧で、 **[System.Net.Http]** をクリックし、 **[インストール]** をクリックします。 インストールを完了します。
6. NuGet **[検索]** ボックスに戻り、「**Json.net**」と入力します。 **Newtonsoft.json** パッケージをインストールしてから、NuGet パッケージ マネージャー ウィンドウを閉じます。
7. ソリューション エクスプローラーで、**WindowsApp** プロジェクトの **[MainPage.xaml]** をダブルクリックして、それを Visual Studio エディターで開きます。
8. `MainPage.xaml` ファイルの `<Grid>` セクションを次のコードで置き換えます。このコードは、ユーザーを認証するためのユーザー名とパスワードのテキスト ボックスを追加します。 さらに、通知メッセージと通知を受け取るユーザー名タグのテキスト ボックスも追加します。

    ```xml
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
                <ToggleButton Name="toggleFCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="FCM" />
                <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
            </Grid>
        </StackPanel>
    </Grid>
    ```

9. ソリューション エクスプローラーで、 **(Windows 8.1)** プロジェクトと **(Windows Phone 8.1)** プロジェクトの `MainPage.xaml.cs` ファイルを開きます。 次の `using` ステートメントを両方のファイルの先頭に追加します。

    ```csharp
    using System.Net.Http;
    using Windows.Storage;
    using System.Net.Http.Headers;
    using Windows.Networking.PushNotifications;
    using Windows.UI.Popups;
    using System.Threading.Tasks;
    ```

10. **WindowsApp** プロジェクトの `MainPage.xaml.cs` で、`MainPage` クラスに次のメンバーを追加します。 `<Enter Your Backend Endpoint>` を、前に取得した実際のバックエンド エンドポイントに必ず置き換えてください。 たとえば、「 `http://mybackend.azurewebsites.net` 」のように入力します。

    ```csharp
    private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";
    ```

11. 次のコードを、 **(Windows 8.1)** プロジェクトと **(Windows Phone 8.1)** プロジェクトの `MainPage.xaml.cs` 内の MainPage クラスに追加します。

    `PushClick` メソッドは、 **[プッシュを送信する]** ボタン用のクリック ハンドラーです。 それは、バックエンドを呼び出して、ユーザー名タグが `to_tag` パラメーターと一致するすべてのデバイスへの通知をトリガーします。 通知メッセージは、要求本文で JSON コンテンツとして送信されます。

    `LoginAndRegisterClick` メソッドは、 **[ログインして登録]** ボタン用のクリック ハンドラーです。 これは、基本的な認証トークン (認証スキームで使用される任意のトークンを表します) をローカル ストレージ内に格納してから、`RegisterClient` を使用してバックエンドを使用した通知に登録します。

    ```csharp
    private async void PushClick(object sender, RoutedEventArgs e)
    {
        if (toggleWNS.IsChecked.Value)
        {
            await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
        }
        if (toggleFCM.IsChecked.Value)
        {
            await sendPush("fcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
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
            // The device handle used is different depending on the device and PNS.
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
    ```

12. `App.xaml.cs` を開いて、`OnLaunched()` イベント ハンドラーから `InitNotificationsAsync()` の呼び出しを見つけます。 `InitNotificationsAsync()`への呼び出しをコメント アウトするか削除します。 ボタン ハンドラーは、通知の登録を初期化します。

    ```csharp
    protected override void OnLaunched(LaunchActivatedEventArgs e)
    {
        //InitNotificationsAsync();
    ```

13. **[WindowsApp]** プロジェクトを右クリックし、 **[追加]** をクリックしてから、 **[クラス]** をクリックします。 クラスに `RegisterClient.cs` という名前を付け、 **[OK]** をクリックしてクラスを生成します。

    このクラスは、プッシュ通知用に登録するために、アプリ バックエンドに接続するために必要な REST 呼び出しをラップします。 「 *アプリ バックエンドからの登録* 」で説明しているとおり、Notification Hubs によって作成された [registrationIds](/previous-versions/azure/azure-services/dn743807(v=azure.100))もローカルに格納されます。 **[ログインして登録]** ボタンをクリックすると、ローカル ストレージに格納されている承認トークンが使用されます。
14. 次の `using` ステートメントを、RegisterClient.cs file ファイルの先頭に追加します。

    ```csharp
    using Windows.Storage;
    using System.Net;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using Newtonsoft.Json;
    using System.Threading.Tasks;
    using System.Linq;
    ```

15. `RegisterClient` クラス定義内で、次のコードを追加します。

    ```csharp
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

        if (statusCode != HttpStatusCode.Accepted && statusCode != HttpStatusCode.OK)
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
    ```

16. すべての変更を保存します。

## <a name="test-the-application"></a>アプリケーションをテストする

1. 両方の Windows でアプリケーションを起動します。
2. 下の画面に示すように、 **[ユーザー名]** と **[パスワード]** を入力します。 Windows Phone で入力するユーザー名とパスワードとは異なるユーザー名とパスワードを入力する必要があります。
3. **[ログインして登録]** をクリックし、ログインしたことを示すダイアログを確認します。 このコードにより、 **[プッシュを送信する]** ボタンも有効になります。

    ![ユーザー名とパスワードが入力された Notification Hubs アプリケーションのスクリーンショット。][14]
4. 次に、 **[受信ユーザー タグ]** フィールドに、登録されているユーザー名を入力します。 通知メッセージを入力し、 **[プッシュを送信する]** をクリックします。
5. 一致するユーザー名タグが登録されているデバイスだけが通知メッセージを受信します。

    ![プッシュされたメッセージが表示された Notification Hubs アプリケーションのスクリーンショット。][15]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、タグが登録に関連付けられている特定のユーザーにプッシュ通知を送信する方法を学習しました。 場所に基づいたプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[場所に基づいたプッシュ通知を送信する](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png

<!-- URLs. -->
[Get started with Notification Hubs]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Secure Push]: notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
