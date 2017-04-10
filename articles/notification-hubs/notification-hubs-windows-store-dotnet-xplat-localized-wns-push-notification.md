---
title: "Notification Hubs ローカライズ ニュース速報のチュートリアル"
description: "ローカライズ ニュース速報通知を Azure Notification Hubs を使用して送信する方法について説明します。"
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: 
ms.assetid: c454f5a3-a06b-45ac-91c7-f91210889b25
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e864e832b4c50644bf4062dee29d34ff9fe2774e


---
# <a name="use-notification-hubs-to-send-localized-breaking-news"></a>通知ハブを使用したローカライズ ニュース速報の送信
> [!div class="op_single_selector"]
> * [Windows ストア C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>Overview
このトピックでは、Azure 通知ハブの **テンプレート** 機能を使用して、言語およびデバイスごとにローカライズしたニュース速報通知をブロードキャストする方法について説明します。 このチュートリアルでは、「 [通知ハブを使用したニュース速報の送信]」で作成した Windows ストア アプリケーションを使用します。 完了すると、興味のあるニュース速報カテゴリに登録して受信する通知の言語を指定し、選択したカテゴリのその言語のプッシュ通知だけを受信できるようになります。

このシナリオは次の 2 つに分けられます。

* Windows ストア アプリケーションで、クライアント デバイスが言語を指定し、さまざまなニュース速報カテゴリを購読できるようになります。
* Azure Notification Hubs の**タグ**機能と**テンプレート**機能を使用して、バックエンドから通知がブロードキャストされます。

## <a name="prerequisites"></a>前提条件
「[通知ハブを使用したニュース速報の送信]」のチュートリアルを完了し、コードが使用可能な状態になっている必要があります。このチュートリアルでは、そのコードに基づいているためです。

Visual Studio 2012 以降も必要です。

## <a name="template-concepts"></a>テンプレートの概念
「 [通知ハブを使用したニュース速報の送信] 」では、 **タグ** を使用してさまざまなニュース カテゴリの通知を購読するアプリケーションを構築しました。
しかし、多くのアプリケーションは複数の市場をターゲットとしており、ローカライズが必要です。 これは、通知自体の内容をローカライズし、適切なデバイス セットに配信する必要があることを意味します。
このトピックでは、Notification Hubs の **テンプレート** 機能を使用して、ローカライズしたニュース速報通知を簡単に配信する方法について説明します。

注: ローカライズした通知を送信する 1 つの方法として、各タグの複数のバージョンを作成する方法があります。 たとえば、ワールド ニュースで英語、フランス語、標準中国語をサポートするには、3 つのタグ ("world_en"、"world_fr"、"world_ch") が必要です。 その後、ワールド ニュースのローカライズ バージョンをこれらの各タグに送信する必要があります。 このトピックでは、テンプレートを使用することでタグの増加を抑制し、複数のメッセージを送信しなくてもよいようにします。

大まかに言えば、テンプレートとは、特定のデバイスが通知をどのように受信するかを特定するための手段です。 テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。 このケースでは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

次に、デバイスが、適切なプロパティを参照するテンプレートを登録するようにします。 たとえば、簡単なトースト メッセージを受信する Windows ストア アプリケーションは、次の対応するタグを持つテンプレートを登録します。

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">$(News_English)</text>
        </binding>
      </visual>
    </toast>



テンプレートは有用な機能です。詳細については、「[テンプレート](notification-hubs-templates-cross-platform-push-messages.md)」の記事を参照してください。 

## <a name="the-app-user-interface"></a>アプリケーションのユーザー インターフェイス
ここでは、「 [通知ハブを使用したニュース速報の送信] 」で作成したニュース速報アプリケーションを変更し、テンプレートを使用してローカライズしたニュース速報を送信します。

Windows ストア アプリケーションで、以下の手順を実行します。

MainPage.xaml を変更してロケール コンボボックスを追加します。

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

## <a name="building-the-windows-store-client-app"></a>Windows ストア クライアント アプリケーションを構築する
1. Notifications クラスで、*StoreCategoriesAndSubscribe* メソッドと *SubscribeToCateories* メソッドにロケール パラメーターを追加します。
   
        public async Task<Registration> StoreCategoriesAndSubscribe(string locale, IEnumerable<string> categories)
        {
            ApplicationData.Current.LocalSettings.Values["categories"] = string.Join(",", categories);
            ApplicationData.Current.LocalSettings.Values["locale"] = locale;
            return await SubscribeToCategories(categories);
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
   
    *RegisterNativeAsync* メソッドを呼び出す代わりに *RegisterTemplateAsync* を呼び出す点に注意してください。テンプレートがロケールに依存している特定の通知形式を登録します。 さらに、テンプレートの名前 ("localizedWNSTemplateExample") も指定します。複数のテンプレートを登録する可能性があり (たとえば、トースト通知用のテンプレートとタイル通知用のテンプレートなど)、それらを更新または削除するには名前を付ける必要があるためです。
   
    同じタグを持つ複数のテンプレートがデバイスによって登録された場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信される点に注意してください (テンプレートごとに 1 つずつ)。 この動作は、同じ論理メッセージによって複数のビジュアル通知を生成する必要がある場合に役立ちます。たとえば、Windows ストア アプリケーションにバッジとトーストの両方を表示する場合などです。
2. 次のメソッドを追加して、格納されたロケールを取得します。
   
        public string RetrieveLocale()
        {
            var locale = (string) ApplicationData.Current.LocalSettings.Values["locale"];
            return locale != null ? locale : "English";
        }
3. 次に示すように、MainPage.xaml.cs でロケール コンボ ボックスの現在の値を取得し、それを Notifications クラスへの呼び出しに提供することで、ボタン クリック ハンドラーを更新します。
   
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
4. 最後に、App.xaml.cs ファイルで `InitNotificationsAsync` メソッドを必ず更新してロケールを取得し、購読しているときに使用します。
   
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

## <a name="send-localized-notifications-from-your-back-end"></a>バックエンドからローカライズした通知を送信する
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

<!-- Anchors. -->
[テンプレートの概念]: #concepts
[アプリケーションのユーザー インターフェイス]: #ui
[Windows ストア クライアント アプリケーションを構築する]: #building-client
[バックエンドから通知を送信する]: #send
[Next Steps]:#next-steps

<!-- Images. -->

<!-- URLs. -->
[モバイル サービス]: /develop/mobile/tutorials/get-started
[Notification Hubs によるユーザーへの通知: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs によるユーザーへの通知: Mobile Services]: /manage/services/notification-hubs/notify-users
[通知ハブを使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-dotnet

[アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mobile Services の使用]: /develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /develop/mobile/tutorials/get-started-with-data-dotnet
[認証の概要]: /develop/mobile/tutorials/get-started-with-users-dotnet
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-dotnet
[アプリケーション ユーザーへのプッシュ通知]: /develop/mobile/tutorials/push-notifications-to-app-users-dotnet
[スクリプトを使用したユーザーの認証]: /develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript と HTML]: /develop/mobile/tutorials/get-started-with-push-js

[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS 向けの Notification Hubs の使用方法]: http://msdn.microsoft.com/library/jj927168.aspx
[方法: Notification Hubs (Windows ストア)]: http://msdn.microsoft.com/library/jj927172.aspx



<!--HONumber=Nov16_HO3-->


