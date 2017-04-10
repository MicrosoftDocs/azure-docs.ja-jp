---
title: "Xamarin.Android アプリへのプッシュ通知の追加 | Microsoft Docs"
description: "Azure App Service と Azure Notification Hubs を使用して、Xamarin.Android アプリにプッシュ通知を送信する方法について説明します。"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: adrianha
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 5c9dd6bd1f9e6338c297af07bfcd1c22f8d6405d
ms.lasthandoff: 03/09/2017


---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Xamarin.Android アプリへのプッシュ通知の追加
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要
このチュートリアルでは、[Xamarin.Android クイック スタート](app-service-mobile-windows-store-dotnet-get-started.md) プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このチュートリアルには、次のものが必要です。

* アクティブな Google アカウント。 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)で、Google アカウントにサインアップできます。
* [Google Cloud Messaging のクライアント コンポーネント](http://components.xamarin.com/view/GCMClient/)。

## <a name="configure-hub"></a>通知ハブを構成する
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a id="register"></a>Firebase Cloud Messaging を有効にする
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>プッシュ要求を送信するように Azure を構成する
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a id="update-server"></a>サーバー プロジェクトをプッシュ通知を送信するように更新する
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a id="configure-app"></a>プッシュ通知のクライアント プロジェクトを構成する
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a id="add-push"></a>アプリケーションにプッシュ通知コードを追加する
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="test"></a>アプリケーションでプッシュ通知をテストする
エミュレーターで仮想デバイスを使用して、アプリをテストできます。 エミュレーターで実行するときに必要な追加の構成手順があります。

1. 次に示すように Android Virtual Device (AVD) Manager で Google API がターゲットとして設定された仮想デバイスに対してデプロイまたはデバッグする必要があります。
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. **[アプリ]** > **[設定]** > **[アカウントの追加]** をクリックして Android デバイスに Google アカウントを追加した後、画面の指示に従います。
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. これまでと同様に ToDoList アプリを実行し、新しい ToDo 項目を挿入します。 今回は、通知領域に通知アイコンが表示されます。 通知ドロアーを開いて、通知の全文を表示できます。
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Xamarin.Android quick start]: app-service-mobile-xamarin-android-get-started.md
[Google Cloud Messaging Client Component]: http://components.xamarin.com/view/GCMClient/
[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/

