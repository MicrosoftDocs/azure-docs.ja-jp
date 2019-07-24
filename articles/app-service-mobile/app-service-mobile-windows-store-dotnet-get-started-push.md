---
title: ユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を追加する | Microsoft Docs
description: Azure App Service Mobile Apps と Azure Notification Hubs を使用してユニバーサル Windows プラットフォーム (UWP) アプリにプッシュ通知を送信する方法について説明します。
services: app-service\mobile,notification-hubs
documentationcenter: windows
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 6de1b9d4-bd28-43e4-8db4-94cd3b187aa3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: crdun
ms.openlocfilehash: 7efd853e7b66933cac811625d7510139864f41f3
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001847"
---
# <a name="add-push-notifications-to-your-windows-app"></a>Windows アプリにプッシュ通知を追加する

[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要

このチュートリアルでは、[Windows クイック スタート](app-service-mobile-windows-store-dotnet-get-started.md) プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="configure-hub"></a>通知ハブを構成する

[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="register-your-app-for-push-notifications"></a>アプリケーションをプッシュ通知に登録する

Microsoft Store にアプリを送信した後、サーバープロジェクトを [Windows Notification Services (WNS)](https://docs.microsoft.com/windows/uwp/design/shell/tiles-and-notifications/windows-push-notification-services--wns--overview) と統合してプッシュを送信するように構成する必要があります。

1. Visual Studio ソリューション エクスプローラーで、UWP アプリ プロジェクトを右クリックし、**[ストア]**  >  **[アプリケーションをストアと関連付ける]** の順にクリックします。

    ![Microsoft ストアにアプリを関連付けます](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)

2. ウィザードで **[次へ]** をクリックし、Microsoft アカウントでサインインします。次に、**[新しいアプリケーション名の予約]** にアプリの名前を入力し、**[予約]** をクリックします。
3. アプリの登録が正常に作成されたら、新しいアプリ名を選択し、**[次へ]** をクリックし、**[関連付け]** をクリックします。 この操作により、必要な Windows ストア登録情報がアプリケーション マニフェストに追加されます。
4. [アプリケーション登録ポータル](https://apps.dev.microsoft.com/)に移動し、Microsoft アカウントでサインインします。 前の手順で関連付けた Microsoft Store アプリをクリックします。
5. 登録ページで、**[アプリケーション シークレット]** と **[パッケージ SID]** の値を記録しておきます。モバイル アプリ バックエンドを構成するときに使用します。

    ![Microsoft ストアにアプリを関連付けます](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

   > [!IMPORTANT]
   > クライアント シークレットとパッケージ SID は、重要なセキュリティ資格情報です。 これらの値は、他のユーザーと共有したり、アプリケーションで配信したりしないでください。 **[アプリケーション ID]** は、Microsoft アカウント認証を構成するためにシークレットと共に使用されます。

[App Center](https://docs.microsoft.com/appcenter/sdk/push/uwp#prerequisite---register-your-app-for-windows-notification-services-wns) には、プッシュ通知用に UWP アプリを構成するための手順もあります。

## <a name="configure-the-backend-to-send-push-notifications"></a>プッシュ通知を送信するようにバックエンドを構成する

[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

## <a id="update-service"></a>プッシュ通知を送信するようにサーバーを更新する

バックエンド プロジェクトの種類 (&mdash;[.NET バックエンド](#dotnet)または [Node.js バックエンド](#nodejs)のいずれか) に一致する以下の手順を使用します。

### <a name="dotnet"></a>.NET バックエンド プロジェクト

1. Visual Studio で、サーバー プロジェクトを右クリックして **[NuGet パッケージの管理]** をクリックし、Microsoft.Azure.NotificationHubs を検索して、**[インストール]** をクリックします。 これにより、Notification Hubs のクライアント ライブラリがインストールされます。
2. **Controllers**を展開し、TodoItemController.cs を開いて、次の using ステートメントを追加します。

    ```csharp
    using System.Collections.Generic;
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.Mobile.Server.Config;
    ```

3. **PostTodoItem** メソッドで、次のコードを **InsertAsync** に対する呼び出しの後ろに追加します。

    ```csharp
    // Get the settings for the server project.
    HttpConfiguration config = this.Configuration;
    MobileAppSettingsDictionary settings =
        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

    // Get the Notification Hubs credentials for the Mobile App.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create the notification hub client.
    NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Define a WNS payload
    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                            + item.Text + @"</text></binding></visual></toast>";
    try
    {
        // Send the push notification.
        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

        // Write the success result to the logs.
        config.Services.GetTraceWriter().Info(result.State.ToString());
    }
    catch (System.Exception ex)
    {
        // Write the failure result to the logs.
        config.Services.GetTraceWriter()
            .Error(ex.Message, null, "Push.SendAsync Error");
    }
    ```

    このコードは、新しい項目が挿入された後、プッシュ通知を送信するように通知ハブに指示します。

4. サーバー プロジェクトを発行します。

### <a name="nodejs"></a>Node.js バックエンド プロジェクト
1. これをまだ行っていない場合は、[クイック スタート プロジェクト](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)をダウンロードするか、[Azure Portal でオンライン エディター](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)を使用します。
2. todoitem.js ファイル内の既存のコードを次のコードに置き換えます。

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs
    logger.info('Running TodoItem.insert');

    // Define the WNS payload that contains the new item Text.
    var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                + context.item.text + "</text></binding></visual></toast>";

    // Execute the insert.  The insert returns the results as a Promise,
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured
            if (context.push) {
                // Send a WNS native toast notification.
                context.push.wns.sendToast(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute()
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;
    ```

    これにより、新しい ToDo 項目が挿入されたときには item.text を含む WNS トースト通知が送信されます。

3. ローカル コンピューターでファイルを編集するときは、サーバー プロジェクトを再発行します。

## <a id="update-app"></a>アプリケーションにプッシュ通知を追加する
次に、アプリは起動時にプッシュ通知に登録する必要があります。 認証を既に有効にしている場合は、プッシュ通知を登録する前にユーザーがサインインしていることを確認します。

1. **App.xaml.cs** プロジェクト ファイルを開き、次の `using` ステートメントを追加します。

    ```csharp
    using System.Threading.Tasks;
    using Windows.Networking.PushNotifications;
    ```

2. 同じファイルで、次の **InitNotificationsAsync** メソッド定義を **App** クラスに追加します。

    ```csharp
    private async Task InitNotificationsAsync()
    {
        // Get a channel URI from WNS.
        var channel = await PushNotificationChannelManager
            .CreatePushNotificationChannelForApplicationAsync();

        // Register the channel URI with Notification Hubs.
        await App.MobileService.GetPush().RegisterAsync(channel.Uri);
    }
    ```

    このコードにより、WNS からアプリケーションの ChannelURI が取得され、その ChannelURI が App Service モバイル アプリに登録されます。

3. **App.xaml.cs** 内の **OnLaunched** イベント ハンドラーの先頭で、次の例に示すように、**async** 修飾子をメソッド定義に追加し、次の呼び出しを新しい **InitNotificationsAsync** メソッドに追加します。

    ```csharp
    protected async override void OnLaunched(LaunchActivatedEventArgs e)
    {
        await InitNotificationsAsync();

        // ...
    }
    ```

    これにより、アプリケーションが起動されるたびに、有効期限付きの ChannelURI が登録されます。

4. UWP アプリ プロジェクトをリビルドします。 これで、アプリケーションがトースト通知を受信する準備が整いました。

## <a id="test"></a>アプリケーションでプッシュ通知をテストする

[!INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

## <a id="more"></a>次のステップ

プッシュ通知についてさらに学習します。

* [Azure モーバイルアプリのために管理対象クライアントを利用する方法](app-service-mobile-dotnet-how-to-use-client-library.md#pushnotifications)テンプレートは、プラットフォーム間のプッシュやローカライズされたプッシュを柔軟に送信できるようにします。 この記事ではテンプレートの登録方法について説明しています。
* [プッシュ通知の発行の診断](../notification-hubs/notification-hubs-push-notification-fixer.md) 通知が破棄されたり、デバイスに届かなかったりするのにはさまざまな原因があります。 このトピックでは、プッシュ通知のエラーの根本原因を分析、解明する方法について説明しています。

次のチュートリアルのいずれかに進むことを検討してください。

* [アプリに認証を追加する](app-service-mobile-windows-store-dotnet-get-started-users.md) ID プロバイダーを使用してアプリのユーザーを認証する方法を学習します。
* [アプリのオフライン同期を有効にする](app-service-mobile-windows-store-dotnet-get-started-offline-data.md): Mobile App バックエンドを使用して、アプリにオフライン サポートを追加する方法について学びます。 オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->
