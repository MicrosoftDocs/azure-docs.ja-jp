---
title: "Mobile Apps を使用した Android アプリへのプッシュ通知の追加 | Microsoft Docs"
description: "Mobile Apps を使用して Android アプリにプッシュ通知を送信する方法について説明します。"
services: app-service\mobile
documentationcenter: android
manager: adrianha
editor: 
author: ysxu
ms.assetid: 9058ed6d-e871-4179-86af-0092d0ca09d3
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: ef43d1df2a64d85f256b28cd7db5a4f4356a7ec0
ms.openlocfilehash: 055f55966de1e5c1bb41a6e0bf1c0da1758a3ed0
ms.lasthandoff: 12/13/2016


---
# <a name="add-push-notifications-to-your-android-app"></a>プッシュ通知を Android アプリに追加する
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>概要
このチュートリアルでは、[Android クイック スタート] プロジェクトにプッシュ通知を追加して、レコードが挿入されるたびにプッシュ通知が送信されるようにします。

ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、プッシュ通知拡張機能パッケージを追加する必要があります。 詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
以下のものが必要になります。

* プロジェクトのバックエンドに応じた IDE:

  * アプリに Node.js バックエンドがある場合は [Android Studio](https://developer.android.com/sdk/index.html)。
  * アプリに Microsoft .NET バックエンドがある場合は [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 以降。
* Firebase Cloud Messaging 用に Android 2.3 以降、Google Repository リビジョン 27 以降、Google Play Services 9.0.2 以降。
* [Android クイック スタート]を完了すること。

## <a name="create-a-project-that-supports-firebase-cloud-messaging"></a>Firebase Cloud Messaging をサポートするプロジェクトを作成する
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-notification-hub"></a>通知ハブを構成する
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="configure-azure-to-send-push-notifications"></a>プッシュ通知を送信するように Azure を構成する
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="enable-push-notifications-for-the-server-project"></a>サーバー プロジェクトのプッシュ通知を有効にする
[!INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

## <a name="add-push-notifications-to-your-app"></a>アプリケーションにプッシュ通知を追加する
このセクションでは、クライアントの Android アプリをプッシュ通知を処理するように更新します。

### <a name="verify-android-sdk-version"></a>Android SDK バージョンの検証
[!INCLUDE [app-service-mobile-verify-android-sdk-version](../../includes/app-service-mobile-verify-android-sdk-version.md)]

次の手順は、Google Play サービスをインストールすることです。 Google Cloud Messaging には、マニフェストの **minSdkVersion** プロパティが準拠する必要がある、開発およびテストに関する最小 API レベル要件があります。

古いデバイスを使用してテストしている場合は、[Google Play Services SDK のセットアップ]に関するページを参考に、どれだけ小さな値を設定できるか判断し、適切に設定してください。

### <a name="add-google-play-services-to-the-project"></a>プロジェクトへの Google Play Services の追加
[!INCLUDE [Add Play Services](../../includes/app-service-mobile-add-google-play-services.md)]

### <a name="add-code"></a>コードの追加
[!INCLUDE [app-service-mobile-android-getting-started-with-push](../../includes/app-service-mobile-android-getting-started-with-push.md)]

## <a name="test-the-app-against-the-published-mobile-service"></a>発行されたモバイル サービスに対してアプリケーションをテストする
Android フォンを USB ケーブルで直接接続するか、エミュレーターで仮想デバイスを使用する方法により、アプリケーションをテストできます。

## <a name="next-steps"></a>次のステップ
これでこのチュートリアルは終了です。次のいずれかのチュートリアルに進むことを検討してください。

* [Android アプリに認証を追加する](app-service-mobile-android-get-started-users.md)。
  サポートされている ID プロバイダーを使用して、Android で todolist クイック スタート プロジェクトに認証を追加する方法について説明します。
* [Android アプリのオフライン同期を有効にする](app-service-mobile-android-get-started-offline-data.md)。
  Mobile Apps バックエンドを使用してオフライン サポートをアプリに追加する方法について説明します。 オフライン同期を使用すると、ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリケーションとやり取りできます。

<!-- URLs -->
[Android クイック スタート]: app-service-mobile-android-get-started.md

[Google Play Services SDK のセットアップ]:https://developers.google.com/android/guides/setup

