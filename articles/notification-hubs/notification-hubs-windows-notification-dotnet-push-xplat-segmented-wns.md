---
title: "Notification Hubs を使用したニュース速報の送信 (Windows ユニバーサル)"
description: "Azure Notification Hubs と登録の際のタグを使用して、ユニバーサル Windows アプリにニュース速報を送信します。"
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 994d2eed-f62e-433c-bf65-4afebf1c0561
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a1367739c87138afb5b1b3e136acd8620ac56468


---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Notification Hubs を使用したニュース速報の送信
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概要
このトピックでは、Azure Notification Hubs を使用してニュース速報通知を Windows Phone 8.1 (Silverlight 以外) アプリにブロードキャストする方法について説明します。 Windows Phone 8.1 Silverlight を対象としている場合は、 [Windows Phone](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md) バージョンを参照してください。 完了すると、興味のあるニュース速報カテゴリに登録し、それらのカテゴリのプッシュ通知だけを受信できるようになります。 このシナリオは、既に興味があると宣言しているユーザーのグループに通知を送信する必要がある多くのアプリケーション (RSS リーダー、音楽ファン向けアプリケーションなど) で一般的なパターンです。 

ブロードキャスト シナリオは、通知ハブでの登録の作成時に 1 つ以上の "*タグ*" を追加することで有効にします。 通知がタグに送信されると、タグに登録されたすべてのデバイスが通知を受信します。 タグは文字列にすぎないため、事前にプロビジョニングする必要はありません。 タグの詳細については、「 [Notification Hubs のルーティングとタグ式](notification-hubs-tags-segment-push-message.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このトピックは、「[Notification Hubs の使用][get-started]」で作成したアプリケーションが基になります。 このチュートリアルを開始する前に、「[Notification Hubs の使用][get-started]」を完了している必要があります。

## <a name="add-category-selection-to-the-app"></a>アプリケーションにカテゴリ選択を追加する
最初の手順として、既存のメイン ページに UI 要素を追加して、ユーザーが登録するカテゴリを選択できるようにします。 ユーザーにより選択されるカテゴリは、デバイスに格納されます。 アプリが起動すると、通知ハブにデバイス登録が作成され、選択されたカテゴリがタグとして追加されます。

1. MainPage.xaml プロジェクト ファイルを開き、 **Grid** 要素で次のコードをコピーします。
   
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
2. **共有**プロジェクトを右クリックして、**Notifications** という名前の新しいクラスを作成して、クラス定義に **public** 修飾子を追加し、新しいコード ファイルに次の **using** ステートメントを追加します。
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.Storage;
        using System.Threading.Tasks;
3. 新しい **Notifications** クラスに次のコードをコピーします。
   
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
   
    このクラスは、このデバイスが受信するニュースのカテゴリを格納するためにローカル ストレージを使用します。 *RegisterNativeAsync* メソッドを呼び出す代わりに、*RegisterTemplateAsync* を呼び出して、テンプレート登録を使用してカテゴリに登録することに注意してください。 
   
    さらに、テンプレートの名前 ("simpleWNSTemplateExample") も指定します。複数のテンプレートを登録する可能性があり (たとえば、トースト通知用のテンプレートとタイル通知用のテンプレートなど)、それらを更新または削除するには名前を付ける必要があるためです。
   
    同じタグを持つ複数のテンプレートがデバイスによって登録された場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信される点に注意してください (テンプレートごとに 1 つずつ)。 この動作は、同じ論理メッセージによって複数のビジュアル通知を生成する必要がある場合に役立ちます。たとえば、Windows ストア アプリケーションにバッジとトーストの両方を表示する場合などです。
   
    テンプレートの詳細については、「 [テンプレート](notification-hubs-templates-cross-platform-push-messages.md)」を参照してください。
4. App.xaml.cs プロジェクト ファイルで、次のプロパティを **App** クラスに追加します。
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
    このプロパティは、 **Notifications** インスタンスの作成とアクセスに使用されます。
   
    上のコード内で、`<hub name>` と `<connection string with listen access>` のプレースホルダーを、通知ハブの名前と既に取得してある *DefaultListenSharedAccessSignature* の接続文字列に置き換えます。
   
   > [!NOTE]
   > クライアント アプリケーションを使用して配布される資格情報は一般にセキュリティで保護されないため、クライアント アプリケーションではリッスン アクセス用のキーだけを配布してください。 リッスン アクセスにより、アプリケーションが通知を登録できるようになりますが、既存の登録を変更することはできないため、通知を送信できません。 通知を送信して既存の登録を変更するセキュリティで保護されたバックエンド サービスでは、フル アクセス キーが使用されます。
   > 
   > 
5. MainPage.xaml.cs で、次の行を追加します。
   
        using Windows.UI.Popups;
6. MainPage.xaml.cs プロジェクト ファイルで、次のメソッドを追加します。
   
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
   
    このメソッドは、カテゴリのリストを作成し、 **Notifications** クラスを使用してそのリストをローカル ストレージに格納し、対応するタグを通知ハブに登録します。 カテゴリが変更されると、新しいカテゴリで登録が再作成されます。

これで、アプリケーションがデバイス上のローカル ストレージに一連のカテゴリを格納したり、ユーザーがカテゴリの選択を変更したときに通知ハブに登録できるようになりました。

## <a name="register-for-notifications"></a>通知を登録する
この手順では、ローカル ストレージに格納されたカテゴリを使用して、起動時に通知ハブに通知します。

> [!NOTE]
> Windows Notification Service (WNS) によって割り当てられたチャネル URI はいつでも変更される可能性があるために、通知エラーを回避するため通知を頻繁に登録してください。 この例では、アプリケーションが起動するたびに通知を登録します。 頻繁に実行されるアプリケーションの場合 (1 日に複数回など)、帯域幅を節約するため、前回の登録から 1 日経過していない場合は登録をスキップできます。
> 
> 

1. App.xaml.cs ファイルを開き、**InitNotificationsAsync** メソッドを更新して、`notifications` クラスを使用してカテゴリに基づいて購読します。
   
        // *** Remove or comment out these lines *** 
        //var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
        //var hub = new NotificationHub("your hub name", "your listen connection string");
        //var result = await hub.RegisterNativeAsync(channel.Uri);
   
        var result = await notifications.SubscribeToCategories();
   
    これにより、アプリケーションが起動するたびに、ローカル ストレージからカテゴリを取得し、これらのカテゴリの登録を要求するようになります。 **InitNotificationsAsync** メソッドは「[Notification Hubs の使用][get-started]」チュートリアルの一部として作成されました。
2. MainPage.xaml.cs プロジェクト ファイルで、 *OnNavigatedTo* メソッドに次のコードを追加します。
   
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
   
    これにより、以前に保存されたカテゴリの状態に基づいてメイン ページが更新されます。

これで、アプリケーションが完成し、デバイスのローカル ストレージに一連のカテゴリを格納できるようになりました。ローカル ストレージは、ユーザーがカテゴリの選択を変更したときに通知ハブに登録するために使用されます。 次に、このアプリケーションにカテゴリ通知を送信できるバックエンドを定義します。

## <a name="sending-tagged-notifications"></a>タグ付けされた通知の送信
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>アプリケーションを実行して通知を生成する
1. Visual Studio で、F5 キーを押してアプリケーションをコンパイルおよび起動します。
   
    ![][1]
   
    アプリケーションの UI には、購読するカテゴリを選択できる一連の切り替えボタンが表示されている点に注目してください。
2. 1 つ以上のカテゴリ切り替えボタンを有効にし、 **[Subscribe]**をクリックします。
   
    アプリケーションにより、選択されたカテゴリがタグに変換され、選択されたタグの新しいデバイス登録が通知ハブから要求されます。 登録されたカテゴリが返され、ダイアログに表示されます。
   
    ![][19]
3. 新しい通知は、次のいずれかの方法でバックエンドから送信します。
   
   * **コンソール アプリケーション:** コンソール アプリケーションを起動します。
   * **Java/PHP:** アプリケーションとスクリプトを実行します。
     
     選択されたカテゴリの通知がトースト通知として表示されます。
     
     ![][14]

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ニュース速報をカテゴリごとにブロードキャストする方法について説明しました。 他の高度な Notification Hubs シナリオを取り上げている、次のいずれかのチュートリアルを行うことをお勧めします。

* [Notification Hubs を使用したローカライズ ニュース速報のブロードキャスト]
  
    ニュース速報アプリケーションを拡張して、ローカライズした通知を送信できるようにする方法について説明します。

<!-- Anchors. -->
[アプリケーションにカテゴリ選択を追加する]: #adding-categories
[通知を登録する]: #register
[バックエンドから通知を送信する]: #send
[アプリケーションを実行して通知を生成する]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-breakingnews-win1.png

[14]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-toast-2.png


[19]: ./media/notification-hubs-windows-store-dotnet-send-breaking-news/notification-hub-windows-reg-2.png

<!-- URLs.-->
[get-started]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[Notification Hubs を使用したローカライズ ニュース速報のブロードキャスト]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notification Hubs によるユーザーへの通知]: /manage/services/notification-hubs/notify-users
[モバイル サービス]: /develop/mobile/tutorials/get-started/
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[方法: Notification Hubs (Windows ストア)]: http://msdn.microsoft.com/library/jj927172.aspx
[アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591



<!--HONumber=Nov16_HO3-->


