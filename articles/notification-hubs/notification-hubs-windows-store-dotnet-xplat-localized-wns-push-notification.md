---
title: Azure Notification Hubs を使用して Windows アプリにローカライズした通知を送信する | Microsoft Docs
description: ローカライズ ニュース速報通知を Azure Notification Hubs を使用して送信する方法について説明します。
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 03/22/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: d91320d8f78942ca916084cbf29a88818d95f531
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92314634"
---
# <a name="tutorial-send-localized-push-notifications-to-windows-apps-using-azure-notification-hubs"></a>チュートリアル: Azure Notification Hubs を使用して Windows アプリにローカライズしたプッシュ通知を送信する

> [!div class="op_single_selector"]
> * [Windows ストア C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)

## <a name="overview"></a>概要

このチュートリアルでは、Notification Hubs サービスに登録されているモバイル デバイスにローカライズしたプッシュ通知を送信する方法を示します。 このチュートリアルでは、「[チュートリアル: 特定のデバイスに通知を送信する (ユニバーサル Windows プラットフォーム)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)」で作成されたアプリケーションを、次のシナリオをサポートするように更新します。

- Windows ストア アプリを使用すると、クライアント デバイスは言語を指定したり、さまざまなニュース速報カテゴリにサブスクライブしたりできます。
- バックエンド アプリは、Azure Notification Hubs の **タグ** および **テンプレート** 機能を使用して通知をブロードキャストします。

このチュートリアルを完了すると、モバイル アプリケーションを使用して、関心のあるカテゴリに登録したり、また通知を受信する言語を指定したりすることもできます。 バックエンド アプリケーションは、言語およびデバイスごとにローカライズされた通知を送信します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ロケール情報をサポートするように Windows アプリを更新する
> * ローカライズした通知を送信するようにバックエンド アプリを更新する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

「[チュートリアル: 特定のデバイスに通知を送信する (ユニバーサル Windows プラットフォーム)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)」を完了してください。

「[チュートリアル: 特定のデバイスに通知を送信する (ユニバーサル Windows プラットフォーム)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)」では、**タグ** を使用してさまざまなニュース **カテゴリ** の通知にサブスクライブするアプリを構築しました。 このチュートリアルでは、Notification Hubs の **テンプレート** 機能を使用して、**ローカライズした** ニュース速報通知を簡単に配信します。

高いレベルでは、テンプレートは、特定のデバイスが通知を受信する形式を指定するための方法です。 テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。 このチュートリアルでは、バックエンド アプリケーションは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

```json
{
    "News_English": "...",
    "News_French": "...",
    "News_Mandarin": "..."
}
```

各デバイスは、適切なプロパティを参照するテンプレートに登録します。 たとえば、トースト メッセージを英語で受信しようとする Windows ストア アプリは、対応するタグを含む次のテンプレートに登録します。

```xml
<toast>
    <visual>
    <binding template=\"ToastText01\">
        <text id=\"1\">$(News_English)</text>
    </binding>
    </visual>
</toast>
```

テンプレートの詳細については、[プッシュ テンプレート](notification-hubs-templates-cross-platform-push-messages.md)に関するページを参照してください。

## <a name="update-windows-app-to-support-locale-information"></a>ロケール情報をサポートするように Windows アプリを更新する

1. 「[チュートリアル: 特定のデバイスに通知を送信する (ユニバーサル Windows プラットフォーム)](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)」のために作成した Visual Studio ソリューションを開きます。
2. ソリューションの `MainPage.xaml` ファイルを更新してロケール コンボ ボックスを追加します。

    ```xml
    <Grid Margin="120, 58, 120, 80"  
            Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
        <Grid.RowDefinitions>
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
            <RowDefinition />
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition />
            <ColumnDefinition />
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top"/>
        <ComboBox Name="Locale" HorizontalAlignment="Left" VerticalAlignment="Center" Width="200" Grid.Row="1" Grid.Column="0">
            <x:String>English</x:String>
            <x:String>French</x:String>
            <x:String>Mandarin</x:String>
        </ComboBox>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="2" Grid.Column="0"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="3" Grid.Column="0"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="4" Grid.Column="0"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="2" Grid.Column="1"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="3" Grid.Column="1"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="4" Grid.Column="1"/>
        <Button Content="Subscribe" HorizontalAlignment="Center" Grid.Row="5" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
    </Grid>
    ```
3. `Notifications` クラスの `StoreCategoriesAndSubscribe` メソッドと `SubscribeToCategories` メソッドにロケール パラメーターを追加します。

    ```csharp
    public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        ApplicationData.Current.LocalSettings.Values["locale"] = locale;
        return await SubscribeToCategories(locale, categories);
    }

    public async Task<Registration> SubscribeToCategories(string locale, IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration. This makes supporting notifications across other platforms much easier.
        // Using the localized tags based on locale selected.
        string templateBodyWNS = String.Format("<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(News_{0})</text></binding></visual></toast>", locale);

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "localizedWNSTemplateExample", categories);
    }
    ```

    `RegisterNativeAsync` メソッドを呼び出す代わりに、`RegisterTemplateAsync` を呼び出します。 テンプレートがロケールによって異なる特定の通知形式を登録します。 複数のテンプレート (たとえば、トースト通知用に 1 つとタイル用に 1 つ) を登録することもできるため、テンプレートの名前 ("localizedWNSTemplateExample") も指定します。 これらの名前はまた、テンプレートを更新または削除するときにも指定する必要があります。

    デバイスが同じタグを持つ複数のテンプレートを登録している場合は、そのタグを対象にした受信メッセージにより、そのデバイスに複数の通知 (テンプレートごとに 1 つ) が配信されます。 この動作は、同じ論理メッセージによって複数のビジュアル通知を生成する必要がある場合に役立ちます。たとえば、Windows ストア アプリケーションにバッジとトーストの両方を表示する場合などです。
4. 次のメソッドを追加して、格納されたロケールを取得します。

    ```csharp
    public string RetrieveLocale()
    {
        var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
        return locale != null ? locale : "English";
    }
    ```

5. `MainPage.xaml.cs` ファイルで、ボタン クリック ハンドラーを更新します。ロケール コンボ ボックスの現在の値を取得して、それを `Notifications` クラスの呼び出しに渡すようにします。

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var locale = (string)Locale.SelectedItem;

        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(locale,
                categories);

        var dialog = new MessageDialog("Locale: " + locale + " Subscribed to: " + 
            string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```
6. 最後に、`App.xaml.cs` ファイルの `InitNotificationsAsync` メソッドを更新します。サブスクライブする際にロケールを取得して使用するようにします。

    ```csharp
    private async void InitNotificationsAsync()
    {
        var result = await notifications.SubscribeToCategories(notifications.RetrieveLocale());

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```



## <a name="run-the-uwp-application"></a>UWP アプリケーションを実行する

1. ユニバーサル Windows プラットフォーム アプリケーションを実行します。 **登録が成功しました** というメッセージが表示されるまで待ちます。

    ![モバイル アプリケーションと登録](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/registration-successful.png)
2. **カテゴリ** と **ロケール** を選択し、**[Subscribe]\(サブスクライブ)** をクリックします。 アプリケーションにより、選択されたカテゴリがタグに変換され、選択されたタグの新しいデバイス登録が通知ハブから要求されます。

    ![モバイル アプリケーション](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/mobile-app.png)
3. **サブスクリプション** に関する **確認** メッセージが表示されます。

    ![サブスクリプション メッセージ](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/subscription-message.png)

## <a name="update-console-app-to-send-localized-notifications"></a>ローカライズした通知を送信するようにコンソール アプリを更新する

テンプレート通知を送信する場合は、一連のプロパティを指定するだけで済みます。このチュートリアルでは、バックエンド アプリケーションが、現在のニュースのローカライズ版を含む一連のプロパティを送信します。次に例を示します。

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

このセクションでは、ソリューション内のコンソール アプリケーション プロジェクトを更新します。 次のコードを使用して、前に作成したコンソール アプリ内の `SendTemplateNotificationAsync` メソッドを変更します。

> [!IMPORTANT]
> コード内で、通知ハブに対するフル アクセスを持つ名前と接続文字列を指定します。

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(
        "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications.
    // This includes APNS, FCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

通知ハブが適切なネイティブ ペイロードを構築し、特定のタグにサブスクライブしているすべてのデバイスに配信するため、この単純な呼び出しにより、プラットフォームには関係なく、ローカライズされたニュースが **すべての** デバイスに配信されます。

## <a name="run-console-app-to-send-localized-notification"></a>ローカライズした通知を送信するコンソール アプリを実行する
**コンソール アプリ** を実行して、カテゴリごとの通知をサポートされる各言語で送信します。 サブスクライブしているカテゴリの通知のみを受信すること、およびメッセージが選択したロケールに対するものであることを確認します。

![Notification messages](./media/notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification/notifications.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、タグが登録に関連付けられている特定のデバイスにローカライズしたプッシュ通知を送信する方法を学習しました。 複数のデバイスを使用している可能性のある特定のユーザーにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[特定のユーザーにプッシュ通知を送信する](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)

<!-- Anchors. -->
[Template concepts]: #concepts
[The app user interface]: #ui
[Building the Windows Store client app]: #building-client
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs: ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Notify users with Notification Hubs: Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-dotnet
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-dotnet
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-dotnet
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)