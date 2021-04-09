---
title: 特定のデバイスに通知を送信する (ユニバーサル Windows プラットフォーム) | Microsoft Docs
description: Azure Notification Hubs と登録内のタグを使用して、ユニバーサル Windows プラットフォーム アプリにニュース速報を送信します。
services: notification-hubs
documentationcenter: windows
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 09/30/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/22/2019
ms.openlocfilehash: 2c77eba69fd914e8ecc7d08a1b16f61ceefe101b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92320571"
---
# <a name="tutorial-send-notifications-to-specific-devices-running-universal-windows-platform-applications"></a>チュートリアル: ユニバーサル Windows プラットフォーム アプリケーションを実行している特定のデバイスに通知を送信する

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概要

このチュートリアルでは、Azure Notification Hubs を使用してニュース速報通知をブロードキャストする方法を示します。 このチュートリアルでは、Windows ストアまたは Windows Phone 8.1 (Silverlight 以外) アプリケーションについて取り上げます。 Windows Phone 8.1 Silverlight をターゲットにしている場合は、「[Azure Notification Hubs を使用して特定の Windows Phone デバイスにプッシュ通知を送信する](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)」を参照してください。

このチュートリアルでは、Azure Notification Hubs を使用して、ユニバーサル Windows プラットフォーム (UWP) アプリケーションを実行している特定の Windows デバイスにプッシュ通知を送信する方法について学習します。 このチュートリアルの完了後、興味があるニュース速報のカテゴリを登録できます。 それらのカテゴリのプッシュ通知のみが届きます。

ブロードキャストのシナリオに対応するには、通知ハブに登録を作成するときに "*タグ*" (複数可) を追加します。 通知がタグに送信されると、タグに登録されたすべてのデバイスが通知を受信します。 タグの詳細については、「[ルーティングとタグ式](notification-hubs-tags-segment-push-message.md)」を参照してください。

> [!NOTE]
> Visual Studio 2019 では、Windows ストアと Windows Phone のバージョン 8.1 以前のプロジェクトはサポートされていません。 詳細については、「[Visual Studio 2019 の対象プラットフォームと互換性](/visualstudio/releases/2019/compatibility)」を参照してください。

このチュートリアルでは、次のタスクを行います。

> [!div class="checklist"]
> * モバイル アプリにカテゴリ選択を追加する
> * 通知を登録する
> * タグ付けされた通知を送信する
> * アプリケーションを実行して通知を生成する

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、[Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する][get-started]方法に関するチュートリアルを完了してください。  

## <a name="add-category-selection-to-the-app"></a>アプリケーションにカテゴリ選択を追加する

最初の手順は、既存のメイン ページに UI 要素を追加して、登録するカテゴリをユーザーが選択できるようにすることです。 選択されたカテゴリはデバイスに格納されます。 アプリが起動すると、通知ハブにデバイス登録が作成され、選択されたカテゴリがタグとして追加されます。

1. *MainPage.xaml* プロジェクト ファイルを開き、`Grid` 要素に次のコードをコピーします。

    ```xml
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
            <RowDefinition/>
        </Grid.RowDefinitions>
        <Grid.ColumnDefinitions>
            <ColumnDefinition/>
            <ColumnDefinition/>
        </Grid.ColumnDefinitions>
        <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  TextWrapping="Wrap" Text="Breaking News" FontSize="42" VerticalAlignment="Top" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="World" Name="WorldToggle" Grid.Row="1" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Politics" Name="PoliticsToggle" Grid.Row="2" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Business" Name="BusinessToggle" Grid.Row="3" Grid.Column="0" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Technology" Name="TechnologyToggle" Grid.Row="1" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Science" Name="ScienceToggle" Grid.Row="2" Grid.Column="1" HorizontalAlignment="Center"/>
        <ToggleSwitch Header="Sports" Name="SportsToggle" Grid.Row="3" Grid.Column="1" HorizontalAlignment="Center"/>
        <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click"/>
    </Grid>
    ```

1. **ソリューション エクスプローラー** で、プロジェクトを右クリックし、**[追加]** > **[クラス]** の順に選択します。 **[新しい項目の追加]** で、クラスに *Notifications* という名前を付けて、**[追加]** を選択します。 必要に応じてクラス定義に `public` 修飾子を追加します。

1. 次の `using` ステートメントを新しいファイルに追加します。

    ```csharp
    using Windows.Networking.PushNotifications;
    using Microsoft.WindowsAzure.Messaging;
    using Windows.Storage;
    using System.Threading.Tasks;
    ```

1. 新しい `Notifications` クラスに次のコードをコピーします。

    ```csharp
    private NotificationHub hub;

    public Notifications(string hubName, string listenConnectionString)
    {
        hub = new NotificationHub(hubName, listenConnectionString);
    }

    public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
    {
        ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
        return await SubscribeToCategories(categories);
    }

    public IEnumerable<string> RetrieveCategories()
    {
        var categories = (string) ApplicationData.Current.LocalSettings.Values["categories"];
        return categories != null ? categories.Split(','): new string[0];
    }

    public async Task<Registration> SubscribeToCategories(IEnumerable<string> categories = null)
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        if (categories == null)
        {
            categories = RetrieveCategories();
        }

        // Using a template registration to support notifications across platforms.
        // Any template notifications that contain messageParam and a corresponding tag expression
        // will be delivered for this registration.

        const string templateBodyWNS = "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

        return await hub.RegisterTemplateAsync(channel.Uri, templateBodyWNS, "simpleWNSTemplateExample",
                categories);
    }
    ```

    このクラスは、ローカル記憶域を使用して、このデバイスが受信する必要があるニュースのカテゴリを格納します。 `RegisterNativeAsync` メソッドを呼び出す代わりに、`RegisterTemplateAsync` を呼び出して、テンプレート登録を使用してカテゴリを登録します。

    1 つ以上のテンプレートを登録する場合、テンプレート名 (たとえば、*simpleWNSTemplateExample*) を指定します。 テンプレートを更新または削除できるように、テンプレートに名前を付けます。 複数 (トースト通知用に 1 つとタイル用に 1 つ) のテンプレートを登録することもあるでしょう。

    >[!NOTE]
    > Notification Hubs では、デバイスが同じタグを使用して複数のテンプレートを登録できます。 この場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信されます (テンプレートごとに 1 つずつ)。 このプロセスにより、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリでバッジとトースト通知の両方として表示するなど)。により、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリでバッジとトースト通知の両方として表示するなど)。

    詳細については、「 [テンプレート](notification-hubs-templates-cross-platform-push-messages.md)」を参照してください。

1. *App.xaml.cs* プロジェクト ファイルで、次のプロパティを `App` クラスに追加します。

    ```csharp
    public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
    ```

    このプロパティを使用して、`Notifications` インスタンスを作成してアクセスします。

    コードのプレースホルダーである `<hub name>` と `<connection string with listen access>` を、通知ハブの名前と既に取得してある **DefaultListenSharedAccessSignature** の接続文字列に置き換えます。

   > [!NOTE]
   > クライアント アプリを使用して配布される資格情報は一般にセキュリティで保護されないため、クライアント アプリでは *リッスン* アクセス用のキーだけを配布してください。 リッスン アクセスでは、通知用にアプリを登録できますが、既存の登録を変更することはできず、通知を送信することはできません。 通知を送信し既存の登録を変更するセキュリティで保護されたバックエンド サービスでは、フル アクセス キーが使用されます。

1. *MainPage.xaml.cs* ファイルに、次の行を追加します。

    ```csharp
    using Windows.UI.Popups;
    ```

1. *MainPage.xaml.cs* ファイルに、次のメソッドを追加します。

    ```csharp
    private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
    {
        var categories = new HashSet<string>();
        if (WorldToggle.IsOn) categories.Add("World");
        if (PoliticsToggle.IsOn) categories.Add("Politics");
        if (BusinessToggle.IsOn) categories.Add("Business");
        if (TechnologyToggle.IsOn) categories.Add("Technology");
        if (ScienceToggle.IsOn) categories.Add("Science");
        if (SportsToggle.IsOn) categories.Add("Sports");

        var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);

        var dialog = new MessageDialog("Subscribed to: " + string.Join(",", categories) + " on registration Id: " + result.RegistrationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    ```

    このメソッドは、カテゴリのリストを作成し、`Notifications` クラスを使用してリストをローカル記憶域に格納します。 さらに、対応するタグを通知ハブに登録します。 カテゴリが変更されると、新しいカテゴリで登録が再作成されます。

これで、アプリは、デバイス上のローカル記憶域にカテゴリ セットを格納できます。 アプリは、ユーザーがカテゴリの選択を変更するたびに、通知ハブに登録します。

## <a name="register-for-notifications"></a>通知を登録する

このセクションでは、ローカル記憶域に格納されたカテゴリを使用して、起動時に通知ハブに通知します。

> [!NOTE]
> Windows Notification Service (WNS) によって割り当てられたチャネル URI はいつでも変更される可能性があるため、通知を頻繁に登録して通知エラーを回避する必要があります。 この例では、アプリケーションが起動するたびに通知を登録します。 頻繁に (たとえば 1 日に複数回) 実行されるアプリでは、前回の登録から 1 日経過していない場合は、帯域幅を節約するために登録をスキップすることもできます。

1. `notifications` クラスを使用してカテゴリに基づいてサブスクライブするには、*App.xaml.cs* ファイルを開き、`InitNotificationsAsync` メソッドを更新します。

    ```csharp
    // *** Remove or comment out these lines ***
    //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    //var hub = new NotificationHub("your hub name", "your listen connection string");
    //var result = await hub.RegisterNativeAsync(channel.Uri);

    var result = await notifications.SubscribeToCategories();
    ```

    このプロセスにより、アプリが起動されたときに確実にローカル記憶域からカテゴリが取得されます。 その後、それらのカテゴリの登録が要求されます。 `InitNotificationsAsync` メソッドは、「[Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム アプリに通知を送信する][get-started]」のチュートリアルの中で作成しました。
2. *MainPage.xaml.cs* プロジェクト ファイルで、`OnNavigatedTo` メソッドに次のコードを追加します。

    ```csharp
    protected override void OnNavigatedTo(NavigationEventArgs e)
    {
        var categories = ((App)Application.Current).notifications.RetrieveCategories();

        if (categories.Contains("World")) WorldToggle.IsOn = true;
        if (categories.Contains("Politics")) PoliticsToggle.IsOn = true;
        if (categories.Contains("Business")) BusinessToggle.IsOn = true;
        if (categories.Contains("Technology")) TechnologyToggle.IsOn = true;
        if (categories.Contains("Science")) ScienceToggle.IsOn = true;
        if (categories.Contains("Sports")) SportsToggle.IsOn = true;
    }
    ```

    このコードは、以前に保存されたカテゴリの状態に基づいてメイン ページを更新します。

これでアプリは完成しました。 デバイスのローカル記憶域に一連のカテゴリを格納することができます。 ユーザーがカテゴリの選択を変更すると、保存済みのカテゴリを使用して通知ハブへの登録が行われます。 次のセクションでは、このアプリにカテゴリ通知を送信できるバックエンドを定義します。

## <a name="run-the-uwp-app"></a>UWP アプリを実行する

1. Visual Studio で、F5 キーを押して、アプリのコンパイルと起動を行います。 アプリの UI には、購読するカテゴリを選択できる一連の切り替えボタンが表示されます。

   ![ニュース速報アプリ](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breaking-news.png)

1. 1 つ以上のカテゴリ切り替えボタンを有効にし、**[購読]** を選択します。

   アプリケーションにより、選択されたカテゴリがタグに変換され、選択されたタグの新しいデバイス登録が通知ハブから要求されます。 登録済みのカテゴリがダイアログ ボックスに表示されます。

    ![カテゴリの切り替えと [購読] ボタン](./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast.png)

## <a name="create-a-console-app-to-send-tagged-notifications"></a>タグ付けされた通知を送信するコンソール アプリを作成する

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-console-app-to-send-tagged-notifications"></a>タグ付けされた通知を送信するコンソール アプリを実行する

前のセクションで作成したアプリを実行します。 選択されたカテゴリの通知がトースト通知として表示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ニュース速報をカテゴリごとにブロードキャストする方法について説明しました。 バックエンド アプリケーションは、そのタグの通知を受信するように登録されているデバイスに、タグ付けされた通知をプッシュします。 使用されているデバイスに関係なく、特定のユーザーにプッシュ通知を送信する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Notification Hubs を使用して Windows アプリにローカライズしたプッシュ通知を送信する](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)

<!-- Anchors. -->
[Add category selection to the app]: #adding-categories
[Register for notifications]: #register
[Send notifications from your back-end]: #send
[Run the app and generate notifications]: #test-app
[Next Steps]: #next-steps

<!-- URLs.-->
[get-started]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started/
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hubs How-To for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: https://go.microsoft.com/fwlink/p/?LinkId=262253
[wns object]: /previous-versions/azure/reference/jj860484(v=azure.100)