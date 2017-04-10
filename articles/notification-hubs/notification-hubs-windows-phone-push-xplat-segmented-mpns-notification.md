---
title: "Notification Hubs を使用したニュース速報の送信 (Windows Phone)"
description: "Azure Notification Hubs と登録の際のタグを使用して、Windows Phone アプリにニュース速報を送信します。"
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: 42726bf5-cc82-438d-9eaa-238da3322d80
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3a6a69bf555c7267d3fbeb03ff6c03054991960f


---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Notification Hubs を使用したニュース速報の送信
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Overview
このトピックでは、Azure Notification Hubs を使用してニュース速報通知を Windows Phone 8.0/8.1 Silverlight アプリにブロードキャストする方法について説明します。 Windows ストアまたは Windows Phone 8.1 アプリを対象としている場合は、 [Windows ユニバーサル](notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md) バージョンを参照してください。 完了すると、興味のあるニュース速報カテゴリに登録し、それらのカテゴリのプッシュ通知だけを受信できるようになります。 このシナリオは、既に興味があると宣言しているユーザーのグループに通知を送信する必要がある多くのアプリケーション (RSS リーダー、音楽ファン向けアプリケーションなど) で一般的なパターンです。

ブロードキャスト シナリオは、通知ハブでの登録の作成時に 1 つ以上の "*タグ*" を追加することで有効にします。 通知がタグに送信されると、タグに登録されたすべてのデバイスが通知を受信します。 タグは文字列にすぎないため、事前にプロビジョニングする必要はありません。 タグの詳細については、「 [Notification Hubs のルーティングとタグ式](notification-hubs-tags-segment-push-message.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このトピックは、「 [Notification Hubs の使用]」で作成したアプリケーションが基になります。 このチュートリアルを開始する前に、「 [Notification Hubs の使用]」を完了している必要があります。

## <a name="add-category-selection-to-the-app"></a>アプリケーションにカテゴリ選択を追加する
最初の手順として、既存のメイン ページに UI 要素を追加して、ユーザーが登録するカテゴリを選択できるようにします。 ユーザーにより選択されるカテゴリは、デバイスに格納されます。 アプリが起動すると、通知ハブにデバイス登録が作成され、選択されたカテゴリがタグとして追加されます。

1. MainPage.xaml プロジェクト ファイルを開き、`TitlePanel` と `ContentPanel` という名前の **Grid** 要素を次のコードに置き換えます。
   
        <StackPanel x:Name="TitlePanel" Grid.Row="0" Margin="12,17,0,28">
            <TextBlock Text="Breaking News" Style="{StaticResource PhoneTextNormalStyle}" Margin="12,0"/>
            <TextBlock Text="Categories" Margin="9,-7,0,0" Style="{StaticResource PhoneTextTitle1Style}"/>
        </StackPanel>
   
        <Grid Name="ContentPanel" Grid.Row="1" Margin="12,0,12,0">
            <Grid.RowDefinitions>
                <RowDefinition Height="auto"/>
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
                <RowDefinition Height="auto" />
            </Grid.RowDefinitions>
            <Grid.ColumnDefinitions>
                <ColumnDefinition />
                <ColumnDefinition />
            </Grid.ColumnDefinitions>
            <CheckBox Name="WorldCheckBox" Grid.Row="0" Grid.Column="0">World</CheckBox>
            <CheckBox Name="PoliticsCheckBox" Grid.Row="1" Grid.Column="0">Politics</CheckBox>
            <CheckBox Name="BusinessCheckBox" Grid.Row="2" Grid.Column="0">Business</CheckBox>
            <CheckBox Name="TechnologyCheckBox" Grid.Row="0" Grid.Column="1">Technology</CheckBox>
            <CheckBox Name="ScienceCheckBox" Grid.Row="1" Grid.Column="1">Science</CheckBox>
            <CheckBox Name="SportsCheckBox" Grid.Row="2" Grid.Column="1">Sports</CheckBox>
            <Button Name="SubscribeButton" Content="Subscribe" HorizontalAlignment="Center" Grid.Row="3" Grid.Column="0" Grid.ColumnSpan="2" Click="SubscribeButton_Click" />
        </Grid>
2. プロジェクトで、**Notifications** という名前の新しいクラスを作成して、クラス定義に **public** 修飾子を追加し、新しいコード ファイルに次の **using** ステートメントを追加します。
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
        using System.IO.IsolatedStorage;
        using System.Windows;
3. 新しい **Notifications** クラスに次のコードをコピーします。
   
        private NotificationHub hub;
   
        // Registration task to complete registration in the ChannelUriUpdated event handler
        private TaskCompletionSource<Registration> registrationTask;
   
        public Notifications(string hubName, string listenConnectionString)
        {
            hub = new NotificationHub(hubName, listenConnectionString);
        }
   
        public IEnumerable<string> RetrieveCategories()
        {
            var categories = (string)IsolatedStorageSettings.ApplicationSettings["categories"];
            return categories != null ? categories.Split(',') : new string[0];
        }
   
        public async Task<Registration> StoreCategoriesAndSubscribe(IEnumerable<string> categories)
        {
            var categoriesAsString = string.Join(",", categories);
            var settings = IsolatedStorageSettings.ApplicationSettings;
            if (!settings.Contains("categories"))
            {
                settings.Add("categories", categoriesAsString);
            }
            else
            {
                settings["categories"] = categoriesAsString;
            }
            settings.Save();
   
            return await SubscribeToCategories();
        }
   
        public async Task<Registration> SubscribeToCategories()
        {
            registrationTask = new TaskCompletionSource<Registration>();
   
            var channel = HttpNotificationChannel.Find("MyPushChannel");
   
            if (channel == null)
            {
                channel = new HttpNotificationChannel("MyPushChannel");
                channel.Open();
                channel.BindToShellToast();
                channel.ChannelUriUpdated += channel_ChannelUriUpdated;
   
                // This is optional, used to receive notifications while the app is running.
                channel.ShellToastNotificationReceived += channel_ShellToastNotificationReceived;
            }
   
            // If channel.ChannelUri is not null, we will complete the registrationTask here.  
            // If it is null, the registrationTask will be completed in the ChannelUriUpdated event handler.
            if (channel.ChannelUri != null)
            {
                await RegisterTemplate(channel.ChannelUri);
            }
   
            return await registrationTask.Task;
        }
   
        async void channel_ChannelUriUpdated(object sender, NotificationChannelUriEventArgs e)
        {
            await RegisterTemplate(e.ChannelUri);
        }
   
        async Task<Registration> RegisterTemplate(Uri channelUri)
        {
            // Using a template registration to support notifications across platforms.
            // Any template notifications that contain messageParam and a corresponding tag expression
            // will be delivered for this registration.
   
            const string templateBodyMPNS = "<wp:Notification xmlns:wp=\"WPNotification\">" +
                                                "<wp:Toast>" +
                                                    "<wp:Text1>$(messageParam)</wp:Text1>" +
                                                "</wp:Toast>" +
                                            "</wp:Notification>";
   
            // The stored categories tags are passed with the template registration.
   
            registrationTask.SetResult(await hub.RegisterTemplateAsync(channelUri.ToString(), 
                templateBodyMPNS, "simpleMPNSTemplateExample", this.RetrieveCategories()));
   
            return await registrationTask.Task;
        }
   
        // This is optional. It is used to receive notifications while the app is running.
        void channel_ShellToastNotificationReceived(object sender, NotificationEventArgs e)
        {
            StringBuilder message = new StringBuilder();
            string relativeUri = string.Empty;
   
            message.AppendFormat("Received Toast {0}:\n", DateTime.Now.ToShortTimeString());
   
            // Parse out the information that was part of the message.
            foreach (string key in e.Collection.Keys)
            {
                message.AppendFormat("{0}: {1}\n", key, e.Collection[key]);
   
                if (string.Compare(
                    key,
                    "wp:Param",
                    System.Globalization.CultureInfo.InvariantCulture,
                    System.Globalization.CompareOptions.IgnoreCase) == 0)
                {
                    relativeUri = e.Collection[key];
                }
            }
   
            // Display a dialog of all the fields in the toast.
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() => 
            { 
                MessageBox.Show(message.ToString()); 
            });
        }

    このクラスは、このデバイスが受信するニュースのカテゴリを格納するために分離されたストレージを使用します。 そのストレージには、 [テンプレート](notification-hubs-templates-cross-platform-push-messages.md) 通知登録を使用してこれらのカテゴリを登録するメソッドも格納されます。


1. App.xaml.cs プロジェクト ファイルで、次のプロパティを **App** クラスに追加します。 `<hub name>` プレースホルダーと `<connection string with listen access>` プレースホルダーを、通知ハブの名前と取得済みの *DefaultListenSharedAccessSignature* の接続文字列に置き換えます。
   
        public Notifications notifications = new Notifications("<hub name>", "<connection string with listen access>");
   
   > [!NOTE]
   > クライアント アプリケーションを使用して配布される資格情報は一般にセキュリティで保護されないため、クライアント アプリケーションではリッスン アクセス用のキーだけを配布してください。 リッスン アクセスにより、アプリケーションが通知を登録できるようになりますが、既存の登録を変更することはできないため、通知を送信できません。 通知を送信して既存の登録を変更するセキュリティで保護されたバックエンド サービスでは、フル アクセス キーが使用されます。
   > 
   > 
2. MainPage.xaml.cs で、次の行を追加します。
   
        using Windows.UI.Popups;
3. MainPage.xaml.cs プロジェクト ファイルで、次のメソッドを追加します。
   
        private async void SubscribeButton_Click(object sender, RoutedEventArgs e)
        {
          var categories = new HashSet<string>();
          if (WorldCheckBox.IsChecked == true) categories.Add("World");
          if (PoliticsCheckBox.IsChecked == true) categories.Add("Politics");
          if (BusinessCheckBox.IsChecked == true) categories.Add("Business");
          if (TechnologyCheckBox.IsChecked == true) categories.Add("Technology");
          if (ScienceCheckBox.IsChecked == true) categories.Add("Science");
          if (SportsCheckBox.IsChecked == true) categories.Add("Sports");
   
          var result = await ((App)Application.Current).notifications.StoreCategoriesAndSubscribe(categories);
   
          MessageBox.Show("Subscribed to: " + string.Join(",", categories) + " on registration id : " +
             result.RegistrationId);
        }
   
    このメソッドは、カテゴリのリストを作成し、 **Notifications** クラスを使用してそのリストをローカル ストレージに格納し、対応するタグを通知ハブに登録します。 カテゴリが変更されると、新しいカテゴリで登録が再作成されます。

これで、アプリケーションがデバイス上のローカル ストレージに一連のカテゴリを格納したり、ユーザーがカテゴリの選択を変更したときに通知ハブに登録できるようになりました。

## <a name="register-for-notifications"></a>通知を登録する
この手順では、ローカル ストレージに格納されたカテゴリを使用して、起動時に通知ハブに通知します。

> [!NOTE]
> Microsoft プッシュ通知サービス (MPNS) によって割り当てられたチャネル URI はいつでも変更される可能性があるので、通知エラーを回避するために通知を頻繁に登録してください。 この例では、アプリケーションが起動するたびに通知を登録します。 頻繁に実行されるアプリケーションの場合 (1 日に複数回など)、帯域幅を節約するため、前回の登録から 1 日経過していない場合は登録をスキップできます。
> 
> 

1. App.xaml.cs ファイルを開き、**async** 修飾子を **Application_Launching** メソッドに追加し、「[Notification Hubs の使用]」で追加した Notification Hubs 登録コードを次のコードに置き換えます。
   
        private async void Application_Launching(object sender, LaunchingEventArgs e)
        {
            var result = await notifications.SubscribeToCategories();
   
            if (result != null)
                System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
                {
                    MessageBox.Show("Registration Id :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
                });
        }
   
    これにより、アプリケーションが起動するたびに、ローカル ストレージからカテゴリを取得し、これらのカテゴリの登録を要求するようになります。
2. MainPage.xaml.cs プロジェクト ファイルで、 **OnNavigatedTo** メソッドを実装する次のコードを追加します。
   
        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
            var categories = ((App)Application.Current).notifications.RetrieveCategories();
   
            if (categories.Contains("World")) WorldCheckBox.IsChecked = true;
            if (categories.Contains("Politics")) PoliticsCheckBox.IsChecked = true;
            if (categories.Contains("Business")) BusinessCheckBox.IsChecked = true;
            if (categories.Contains("Technology")) TechnologyCheckBox.IsChecked = true;
            if (categories.Contains("Science")) ScienceCheckBox.IsChecked = true;
            if (categories.Contains("Sports")) SportsCheckBox.IsChecked = true;
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
   
    ![][2]
3. カテゴリへのサブスクリプションが完了したことを確認するメッセージを受信した後、コンソール アプリを実行して、カテゴリごとに通知を送信します。 サブスクライブしたカテゴリの通知だけが受信されることを確認します。
   
    ![][3]

これで、このトピックは終了です。

<!--##Next steps

In this tutorial we learned how to broadcast breaking news by category. Consider completing one of the following tutorials that highlight other advanced Notification Hubs scenarios:

+ [Use Notification Hubs to broadcast localized breaking news]

    Learn how to expand the breaking news app to enable sending localized notifications.

+ [Notify users with Notification Hubs]

    Learn how to push notifications to specific authenticated users. This is a good solution for sending notifications only to specific users.
-->

<!-- Anchors. -->
[アプリケーションにカテゴリ選択を追加する]: #adding-categories
[通知を登録する]: #register
[バックエンドから通知を送信する]: #send
[アプリケーションを実行して通知を生成する]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-breakingnews.png
[2]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-registration.png
[3]: ./media/notification-hubs-windows-phone-send-breaking-news/notification-hub-toast.png



<!-- URLs.-->
[Notification Hubs の使用]: /manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Notification Hubs を使用したローカライズ ニュース速報のブロードキャスト]: ../breakingnews-localized-wp8.md
[Notification Hubs によるユーザーへの通知]: /manage/services/notification-hubs/notify-users/
[モバイル サービス]: /develop/mobile/tutorials/get-started
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[方法: Microsoft Azure Notification Hubs (Windows Phone 8 アプリ) に関するページ]: ??




<!--HONumber=Nov16_HO3-->


