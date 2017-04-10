---
title: "Azure Mobile Apps を使用した、iOS アプリへのプッシュ通知の追加"
description: "Azure Mobile Apps を使用して iOS アプリにプッシュ通知を送信する方法について説明します。"
services: app-service\mobile
documentationcenter: ios
manager: yochayk
editor: 
author: ysxu
ms.assetid: fa503833-d23e-4925-8d93-341bb3fbab7d
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/10/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a21ae4b13479f66ada053153834a2b3cc5687e5a


---
# <a name="add-push-notifications-to-your-ios-app"></a>iOS アプリへのプッシュ通知の追加
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要
このチュートリアルでは、[iOS クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

[iOS シミュレーターでは、プッシュ通知はサポートされていません](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html)。 物理 iOS デバイスと [Apple Developer Program メンバーシップ](https://developer.apple.com/programs/ios/)が必要です。

## <a name="a-nameconfigure-hubaconfigure-notification-hub"></a><a name="configure-hub"></a>通知ハブを構成する
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraregister-app-for-push-notifications"></a><a id="register"></a>アプリをプッシュ通知用に登録する
[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

## <a name="configure-azure-to-send-push-notifications"></a>プッシュ通知を送信するように Azure を構成する
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

## <a name="a-idupdate-serveraupdate-backend-to-send-push-notifications"></a><a id="update-server"></a>プッシュ通知を送信するようにバックエンドを更新する
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-apns](../../includes/app-service-mobile-dotnet-backend-configure-push-apns.md)]

## <a name="a-idadd-pushaadd-push-notifications-to-app"></a><a id="add-push"></a>プッシュ通知をアプリに追加する
[!INCLUDE [app-service-mobile-add-push-notifications-to-ios-app.md](../../includes/app-service-mobile-add-push-notifications-to-ios-app.md)]

## <a name="a-idtestatest-push-notifications"></a><a id="test"></a>プッシュ通知をテストする
[!INCLUDE [Test Push Notifications in App](../../includes/test-push-notifications-in-app.md)]

## <a name="a-idmoreamore"></a><a id="more"></a>詳細
* テンプレートを利用すれば、プラットフォーム間のプッシュやローカライズされたプッシュを柔軟に送信できます。 [Azure Mobile Services 向け iOS クライアント ライブラリの使用方法](app-service-mobile-ios-how-to-use-client-library.md#templates) 」に記載されています。

<!-- Anchors.  -->

<!-- Images. -->

<!-- URLs. -->
[iOS クイック スタート]: app-service-mobile-ios-get-started.md



<!--HONumber=Nov16_HO3-->


