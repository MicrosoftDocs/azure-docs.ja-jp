---
title: Azure Notification Hubs を使用して Windows Phone アプリにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知を送信する方法について説明します。
services: notification-hubs
documentationcenter: windows
keywords: プッシュ通知,プッシュ通知,Windows Phone プッシュ通知
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: d872d8dc-4658-4d65-9e71-fa8e34fae96e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: e91d250b8cc9b80f2c97910c7fa972af32fa9104
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88998341"
---
# <a name="tutorial-send-push-notifications-to-windows-phone-apps-using-notification-hubs"></a>チュートリアル: Notification Hubs を使用して Windows Phone アプリにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

このチュートリアルは、Azure Notification Hubs を使用して Windows Phone 8 または Windows Phone 8.1 Silverlight アプリケーションにプッシュ通知を送信する方法を示しています。 Windows Phone 8.1 (Silverlight 以外) を対象にしている場合は、このチュートリアルの [Windows ユニバーサル](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) バージョンを参照してください。

このチュートリアルでは、Microsoft プッシュ通知サービス (MPNS) を使用してプッシュ通知を受信する空の Windows Phone 8 アプリケーションを作成します。 アプリを作成したら、通知ハブを使用して、アプリを実行しているすべてのデバイスにプッシュ通知をブロードキャストします。

> [!NOTE]
> Notification Hubs Windows Phone SDK では、Windows Phone 8.1 Silverlight アプリで Windows プッシュ通知サービス (WNS) を使用できません。 Windows Phone 8.1 Silverlight アプリで (MPNS ではなく) WNS を使用する場合は、REST API を使用する [Notification Hubs - Windows Phone Silverlight チュートリアル]の手順に従ってください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 通知ハブを作成する
> * Windows Phone アプリケーションを作成する
> * 通知をテスト送信する

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。
* [モバイル開発コンポーネントを含む Visual Studio 2015 Express](https://www.visualstudio.com/vs/older-downloads/)

このチュートリアルを完了することは、Windows Phone 8 アプリケーションの他のすべての Notification Hubs チュートリアルの前提条件です。

## <a name="create-your-notification-hub"></a>通知ハブを作成する

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-windows-phone-mpns-settings"></a>Windows Phone (MPNS) の設定を構成する

1. **[通知設定]** で、**[Windows Phone (MPNS)]** を選択します。
2. **[Enable authentication push]\(認証プッシュを有効にする)** を選択します。
3. ツールバーの **[保存]** を選択します。

    ![Azure Portal - [非認証プッシュを有効にする]](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

    これでハブが作成され、Windows Phone に向けて非認証通知を送信するように構成されました。

    > [!NOTE]
    > このチュートリアルでは、非認証モードで MPNS を使用します。 MPNS 非認証モードでは、各チャネルに送信できる通知に制限があります。 Notification Hubs では、証明書のアップロードを可能にすることで、 [MPNS 認証モード](/previous-versions/windows/apps/ff941099(v=vs.105)) をサポートします。

## <a name="create-a-windows-phone-application"></a>Windows Phone アプリケーションを作成する

このセクションでは、自身を通知ハブに登録する Windows Phone アプリケーションを作成します。

1. Visual Studio で、新しい Windows Phone 8 アプリケーションを作成します。

    ![Visual Studio - New Project - Windows Phone App][13]

    Visual Studio 2013 Update 2 以降では、代わりに Windows Phone Silverlight アプリケーションを作成します。

    ![Visual Studio - New Project - Blank App - Windows Phone Silverlight][11]
2. Visual Studio でソリューションを右クリックし、 **[NuGet パッケージの管理]** をクリックします。
3. `WindowsAzure.Messaging.Managed` を検索し、 **[インストール]** をクリックして、使用条件に同意します。

    ![Visual Studio - NuGet Package Manager][20]
4. App.xaml.cs ファイルを開き、次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Phone.Notification;
    using Microsoft.WindowsAzure.Messaging;
    ```

5. `App.xaml.cs` の `Application_Launching` メソッドの先頭に次のコードを追加します。

    ```csharp
    private void Application_Launching(object sender, LaunchingEventArgs e)
    {

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
    }
    ```

   > [!NOTE]
   > 値 `MyPushChannel` は、[HttpNotificationChannel](/previous-versions/ff402781(v=vs.110)) コレクションで既存のチャネルを参照するために使用されるインデックスです。 ない場合は、その名前で新しいエントリを作成します。

    ハブの名前と、前のセクションでメモした `DefaultListenSharedAccessSignature` という接続文字列を挿入します。
    このコードにより、MPNS からアプリケーションのチャネル URI が取得され、そのチャネル URI が通知ハブに登録されます。 これにより、アプリケーションが起動するたびに必ずチャネル URI も通知ハブに登録されます。

   > [!NOTE]
   > このチュートリアルでは、トースト通知がデバイスに送信されます。 タイル通知を送信する場合は、チャネルの `BindToShellTile` メソッドを呼び出す必要があります。 トースト通知とタイル通知の両方をサポートするには、`BindToShellTile` と `BindToShellToast` の両方を呼び出します。

6. ソリューション エクスプローラーで **[プロパティ]** を展開して `WMAppManifest.xml` ファイルを開き、**[機能]** タブで **ID_CAP_PUSH_NOTIFICATION** 機能がオンであることを確認します。 これで、アプリはプッシュ通知を受信できます。

    ![Visual Studio - Windows Phone App Capabilities][14]
7. `F5` キーを押してアプリケーションを実行します。 登録メッセージがアプリに表示されます。
8. アプリを閉じるか、またはホーム ページに切り替えます。

   > [!NOTE]
   > トースト プッシュ通知を受信するには、アプリケーションがフォアグラウンドで実行されていない必要があります。

## <a name="test-send-a-notification"></a>通知をテスト送信する

1. Azure Portal で、[概要] タブに切り替えます。
2. **[テスト送信]** を選択します。

    ![[テスト送信] ボタン](./media/notification-hubs-windows-phone-get-started/test-send-button.png)
3. **[テスト送信]** ウィンドウで、次の手順を実行します。

    1. **[プラットフォーム]** として、**[Windows Phone]** を選択します。
    2. **通知の種類** として、**[トースト]** を選択します。
    3. **[送信]** を選択します。
    4. ウィンドウの一番下の一覧にある **結果** を確認します。

        ![[テスト送信] ウィンドウ](./media/notification-hubs-windows-phone-get-started/test-send-window.png)
4. Windows Phone エミュレーターまたは Windows Phone で、通知メッセージが表示されることを確認します。

    ![Windows Phone 上の通知](./media/notification-hubs-windows-phone-get-started/notification-on-windows-phone.png)

## <a name="next-steps"></a>次のステップ

この簡単な例では、すべての Windows Phone 8 デバイスにプッシュ通知をブロードキャストしました。 特定のデバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md)

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
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[MPNS authenticated mode]: /previous-versions/windows/apps/ff941099(v=vs.105)
[Use Notification Hubs to push notifications to users]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Use Notification Hubs to send breaking news]: notification-hubs-windows-phone-push-xplat-segmented-mpns-notification.md
[toast catalog]: /previous-versions/windows/apps/jj662938(v=vs.105)
[tile catalog]: /previous-versions/windows/apps/hh202948(v=vs.105)
[Notification Hubs - Windows Phone Silverlight チュートリアル]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari
