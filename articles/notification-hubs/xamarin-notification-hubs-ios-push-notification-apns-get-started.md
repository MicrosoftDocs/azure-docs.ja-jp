---
title: Azure Notification Hubs を使用して Xamarin にプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して Xamarin.iOS アプリケーションにプッシュ通知を送信する方法について学習します。
services: notification-hubs
keywords: ios push notifications、プッシュ メッセージ、プッシュ通知、プッシュ メッセージ
documentationcenter: xamarin
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/12/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: ff1e5edad05ebd7157f71ad2e099ea88905be4f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221138"
---
# <a name="tutorial-send-push-notifications-to-xamarinios-apps-using-azure-notification-hubs"></a>チュートリアル:Azure Notification Hubs を使用して Xamarin.iOS アプリにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>概要

このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。 [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) を使用してプッシュ通知を受信する空の Xamarin.iOS アプリを作成します。

完了すると、通知ハブを使用して、アプリを実行しているすべてのデバイスにプッシュ通知をブロードキャストできます。 完成したコードは、[NotificationHubs アプリ][GitHub] サンプルで参照できます。

このチュートリアルでは、次のタスクを実行するコードを作成および更新します。

> [!div class="checklist"]
> * 証明書の署名要求ファイルを生成する
> * アプリケーションをプッシュ通知に登録する
> * アプリケーションのプロビジョニング プロファイルを作成する
> * iOS プッシュ通知向けに通知ハブを構成する
> * テスト プッシュ通知を送信する

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。
* [Xcode][Install Xcode]
* iOS 10 以降のバージョンに対応するデバイス
* [Apple Developer Program](https://developer.apple.com/programs/) メンバーシップ
* [Visual Studio for Mac]
  
  > [!NOTE]
  > iOS Push Notifications の構成要件に従って、サンプル アプリケーションのデプロイとテストは、シミュレーターではなく、物理的な iOS デバイス (iPhone または iPad) で行う必要があります。

このチュートリアルを完了することは、Xamarin.iOS アプリの他のすべての Notification Hubs チュートリアルの前提条件です。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-app-to-the-notification-hub"></a>通知ハブにアプリケーションを接続する

### <a name="create-a-new-project"></a>新しいプロジェクトを作成する

1. Visual Studio で、新しい iOS プロジェクトを作成し、 **[単一ビュー アプリ]** テンプレートを選択し、 **[次へ]** をクリックします。

     ![Visual Studio - アプリケーションの種類を選択する][31]

2. アプリケーション名と組織の識別子を入力し、 **[次へ]** 、 **[作成]** の順にクリックします。

3. ソリューション ビューから *[Info.plist]* をダブルクリックし、 **[ID]** で、バンドル識別子がプロビジョニング プロファイルを作成するときに使用したものに一致することを確認します。 **[署名]** で、 **[チーム]** に Developer アカウントを選択し、[Automatically manage signing]\(署名を自動的に管理する\) をオンにし、署名証明書とプロビジョニング プロファイルが自動的に選択されることを確認します。

    ![Visual Studio - iOS アプリの構成][32]

4. ソリューション ビューで、`Entitlements.plist` をダブルクリックし、 **[プッシュ通知を有効にする]** チェック ボックスがオンになっていることを確認します。

    ![Visual Studio - iOS エンタイトルメントの構成][33]

5. Azure Messaging パッケージを追加します。 ソリューション ビューでプロジェクトを右クリックし、 **[追加]**  >  **[NuGet パッケージの追加]** を選択します。 **Xamarin.Azure.NotificationHubs.iOS** を検索し、パッケージをプロジェクトに追加します。

6. 新しいファイルをクラスに追加し、`Constants.cs` と名前を付け、次の変数を追加し、文字列リテラルのプレースホルダーを `hubname` とメモしておいた `DefaultListenSharedAccessSignature` に置き換えます。

    ```csharp
    // Azure app-specific connection string and hub path
    public const string ListenConnectionString = "<Azure DefaultListenSharedAccess Connection String>";
    public const string NotificationHubName = "<Azure Notification Hub Name>";
    ```

7. `AppDelegate.cs` に次の using ステートメントを追加します。

    ```csharp
    using WindowsAzure.Messaging.NotificationHubs;
    using UserNotifications
    ```

8. `AppDelegate.cs` に、`MSNotificationHubDelegate` の実装を作成します。

    ```csharp
    public class AzureNotificationHubListener : MSNotificationHubDelegate
    {
        public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
        {

        }
    }
    ```

9. `AppDelegate.cs` で、次のコードと一致するように `FinishedLaunching()` を更新します。

    ```csharp
    public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
    {
        // Set the Message listener
        MSNotificationHub.SetDelegate(new AzureNotificationHubListener());
        
        // Start the SDK
        MSNotificationHub.Start(ListenConnectionString, NotificationHubName);

        return true;
    }
    ```

10. `AppDelegate.cs` で、`AzureNotificationHubListener` クラスの `DidReceivePushNotification` メソッドを実装します。

    ```csharp
    public override void DidReceivePushNotification(MSNotificationHub notificationHub, MSNotificationHubMessage message)
    {
        // This sample assumes { aps: { alert: { title: "Hello", body: "World" } } }
        var alertTitle = message.Title ?? "Notification";
        var alertBody = message.Body;

        var myAlert = UIAlertController.Create(alertTitle, alertBody, UIAlertControllerStyle.Alert);
        myAlert.AddAction(UIAlertAction.Create("OK", UIAlertActionStyle.Default, null));
        UIApplication.SharedApplication.KeyWindow.RootViewController.PresentViewController(myAlert, true, null);
    }
    ```

11. デバイスでアプリケーションを実行します。

## <a name="send-test-push-notifications"></a>テスト プッシュ通知を送信する

アプリの通知の受信をテストするには、[Azure Portal] の *[テスト送信]* オプションを使用します。 これは、デバイスにテスト プッシュ通知を送信します。

![Azure Portal - テスト送信][30]

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Apps などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドに使用できない場合は、REST API を直接使用して通知メッセージを送信することもできます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、バックエンドに登録されているすべての iOS デバイスにブロードキャスト通知を送信しました。 特定の iOS デバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[特定のデバイスにプッシュ通知を送信する](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png
[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png
[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png
[33]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-entitlements-settings.png

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: https://go.microsoft.com/fwlink/p/?LinkId=272456
[Visual Studio for Mac]: https://visualstudio.microsoft.com/vs/mac/
[Local and Push Notification Programming Guide]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[Apple Push Notification Service fwlink]: https://go.microsoft.com/fwlink/p/?LinkId=272584
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
