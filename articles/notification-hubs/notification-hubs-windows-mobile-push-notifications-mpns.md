---
title: "Windows Phone での Azure Notification Hubs を使用したプッシュ通知の送信 | Microsoft Docs"
description: "このチュートリアルでは、Azure Notification Hubs を使用して Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。"
services: notification-hubs
documentationcenter: windows
keywords: "プッシュ通知,プッシュ通知,Windows Phone プッシュ通知"
author: ysxu
manager: erikre
editor: erikre
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.openlocfilehash: f0bfe81f849813d146d644b32490af657b1071b5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="sending-push-notifications-with-azure-notification-hubs-on-windows-phone"></a>Windows Phone での Azure Notification Hubs を使用したプッシュ通知の送信
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要
> [!NOTE]
> このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F)を参照してください。
> 
> 

このチュートリアルでは、Azure Notification Hubs を使用して Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。 Windows Phone 8.1 (Silverlight 以外) を対象としている場合は、 [Windows Universal](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) バージョンを参照してください。
このチュートリアルでは、Microsoft プッシュ通知サービス (MPNS) を使用してプッシュ通知を受信する空の Windows Phone 8 アプリケーションを作成します。 完了すると、通知ハブを使用して、アプリケーションを実行するすべてのデバイスにプッシュ通知をブロードキャストできるようになります。

> [!NOTE]
> Notification Hubs Windows Phone SDK では、Windows Phone 8.1 Silverlight アプリで Windows プッシュ通知サービス (WNS) を使用できません。 Windows Phone 8.1 Silverlight アプリで (MPNS ではなく) WNS を使用する場合は、REST API を使用する [Notification Hubs - Windows Phone Silverlight チュートリアル]の手順に従ってください。
> 
> 

このチュートリアルでは、Notification Hubs を使用した簡単なブロードキャスト シナリオのデモンストレーションを行います。

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* [Visual Studio 2012 Express for Windows Phone]以降のバージョン

このチュートリアルを完了することは、Windows Phone 8 アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。

## <a name="create-your-notification-hub"></a>通知ハブを作成する
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p><b>[Notification Services]</b> セクション (<i>[設定]</i> 内にあります)、<b>[Windows Phone (MPNS)]</b> の順にクリックし、<b>[非認証プッシュを有効にする]</b> チェック ボックスをオンにします。</p>
</li>
</ol>

&emsp;&emsp;![Azure Portal - Enable unathenticated push notifications](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

これでハブが作成され、Windows Phone に向けて非認証通知を送信するように構成されました。

> [!NOTE]
> このチュートリアルでは、非認証モードで MPNS を使用します。 MPNS 非認証モードでは、各チャネルに送信できる通知に制限があります。 Notification Hubs では、証明書のアップロードを可能にすることで、 [MPNS 認証モード](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx) をサポートします。
> 
> 

## <a name="connecting-your-app-to-the-notification-hub"></a>Notification Hub にアプリを接続する
1. Visual Studio で、新しい Windows Phone 8 アプリケーションを作成します。
   
       ![Visual Studio - New Project - Windows Phone App][13]
   
    Visual Studio 2013 Update 2 以降では、代わりに Windows Phone Silverlight アプリケーションを作成します。
   
    ![Visual Studio - New Project - Blank App - Windows Phone Silverlight][11]
2. Visual Studio でソリューションを右クリックし、 **[NuGet パッケージの管理]**をクリックします。
   
    **[NuGet パッケージの管理]** ダイアログ ボックスが表示されます。
3. `WindowsAzure.Messaging.Managed` を検索し、 **[インストール]**をクリックして、使用条件に同意します。
   
    ![Visual Studio - NuGet Package Manager][20]
   
    <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet パッケージ</a>を使用して、Windows の Azure メッセージング ライブラリに参照がダウンロードされ、インストールされ、追加されます。
4. App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。
   
        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;
5. App.xaml.cs 内の **Application_Launching** メソッドの先頭に次のコードを追加します。
   
        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }
   
        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());
   
            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });
   
   > [!NOTE]
   > 値 **MyPushChannel** は、 [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) コレクションで既存のチャネルを参照するために使用されるインデックスです。 ない場合は、その名前で新しいエントリを作成します。
   > 
   > 
   
    必ず、ハブの名前と、前のセクションで取得した **DefaultListenSharedAccessSignature** という接続文字列を挿入してください。
    このコードにより、MPNS からアプリケーションのチャネル URI が取得され、そのチャネル URI が通知ハブに登録されます。 これにより、アプリケーションが起動するたびに必ずチャネル URI も通知ハブに登録されます。
   
   > [!NOTE]
   > このチュートリアルでは、トースト通知がデバイスに送信されます。 タイル通知を送信する場合は、チャネルの **BindToShellTile** メソッドを呼び出す必要があります。 トースト通知とタイル通知の両方をサポートするには、**BindToShellTile** と **BindToShellToast** の両方を呼び出します。
   > 
   > 
6. ソリューション エクスプローラーで **[プロパティ]** を展開して `WMAppManifest.xml` ファイルを開き、**[機能]** タブで **ID_CAP_PUSH_NOTIFICATION** 機能がオンであることを確認します。
   
       ![Visual Studio - Windows Phone App Capabilities][14]
   
       This ensures that your app can receive push notifications. Without it, any attempt to send a push notification to the app will fail.
7. `F5` キーを押してアプリケーションを実行します。
   
    登録メッセージがアプリに表示されます。
8. アプリケーションを閉じます。  
   
   > [!NOTE]
   > トースト プッシュ通知を受信するには、アプリケーションがフォアグラウンドで実行されていない必要があります。
   > 
   > 

## <a name="send-push-notifications-from-your-backend"></a>バックエンドからプッシュ通知を送信する
プッシュ通知は、パブリック <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST インターフェイス</a>を介してどのバックエンドからでも Notification Hubs を使用して送信できます。 このチュートリアルでは、.NET コンソール アプリケーションを使用してプッシュ通知を送信します。 

Notification Hubs に統合されている ASP.NET WebAPI バックエンドからプッシュ通知を送信する方法の例については、「[Azure Notification Hubs と .NET バックエンドによるユーザーへの通知](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md)」を参照してください。  

[REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx) を使用したプッシュ通知の送信方法の例については、「[How to use Notification Hubs from Java (Java から Notification Hubs を使用する方法)](notification-hubs-java-push-notification-tutorial.md)」と「[How to use Notification Hubs from PHP (PHP から Notification Hubs を使用する方法)](notification-hubs-php-push-notification-tutorial.md)」を参照してください。

1. ソリューションを右クリックして **[追加]**、**[新しいプロジェクト]** の順に選択し、**[Visual C#]** で **[Windows]**、**[コンソール アプリケーション]** の順にクリックして、**[OK]** をクリックします。
   
       ![Visual Studio - New Project - Console Application][6]
   
    これにより、新しい Visual C# コンソール アプリケーションがソリューションに追加されます。 個別のソリューションでこの操作を行うこともできます。
2. **[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックします。
   
    これで、パッケージ マネージャー コンソールが表示されます。
3. **[パッケージ マネージャー コンソール]** ウィンドウで **[既定のプロジェクト]** に新しいコンソール アプリケーション プロジェクトを設定した後、そのコンソール ウィンドウから次のコマンドを実行します。
   
       Install-Package Microsoft.Azure.NotificationHubs
   
   これにより <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs NuGet パッケージ</a>を利用して Azure Notification Hubs SDK に参照が追加されます。
4. `Program.cs` ファイルを開き、次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.NotificationHubs;
5. `Program` クラスで、次のメソッドを追加します。
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }
   
    `<hub name>` プレースホルダーは、ポータルに表示される通知ハブの名前に置き換えてください。 また、接続文字列プレースホルダーを、「通知ハブを構成する」で取得した **DefaultFullSharedAccessSignature** という接続文字列に置き換えます。
   
   > [!NOTE]
   > **リッスン** アクセスではなく**フル** アクセスを持つ接続文字列を使用してください。 リッスン アクセス文字列にはプッシュ通知を送信するアクセス許可はありません。
   > 
   > 
6. `Main` メソッドに次の行を追加します。
   
         SendNotificationAsync();
         Console.ReadLine();
7. Windows Phone エミュレーターを実行し、アプリを閉じて、コンソール アプリケーション プロジェクトを既定のスタートアップ プロジェクトとして設定します。次に、`F5` キーを押して、アプリを実行します。
   
    トースト プッシュ通知を受信します。 トースト バナーをタップすると、アプリが読み込まれます。

MSDN の[トースト カタログ]および[タイル カタログ]に関するトピックに、使用できるすべてのペイロードが記載されています。

## <a name="next-steps"></a>次のステップ
この簡単な例では、すべての Windows Phone 8 デバイスにプッシュ通知をブロードキャストしました。 

特定のユーザーをターゲットにする方法については、チュートリアル「 [Azure Notification Hubs と .NET バックエンドによるユーザーへの通知] 」を参照してください。 

対象グループごとにユーザーを区分する場合は、「 [通知ハブを使用したニュース速報の送信]」を参照してください。 

通知ハブの使用方法の詳細については、「 [Microsoft Azure 通知ハブの概要]」を参照してください。

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express for Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Microsoft Azure 通知ハブの概要]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Azure Notification Hubs と .NET バックエンドによるユーザーへの通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[通知ハブを使用したニュース速報の送信]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[トースト カタログ]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[タイル カタログ]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs - Windows Phone Silverlight チュートリアル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

