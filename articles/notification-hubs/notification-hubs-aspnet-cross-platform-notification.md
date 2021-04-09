---
title: Azure Notification Hubs を使用してユーザーにクロスプラットフォーム通知を送信する (ASP.NET)
description: Notification Hubs のテンプレートを使用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を説明します。
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: thsomasu
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 09/14/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 10/02/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 3f8f9357fff4773467dd93046ece8533514eb444
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92313900"
---
# <a name="send-cross-platform-notifications-with-azure-notification-hubs"></a>Azure Notification Hubs を使用してクロスプラットフォーム通知を送信する

このチュートリアルは、前のチュートリアル「[Azure Notification Hubs を使用して特定のユーザーに通知を送信する]」に基づいています。 そのチュートリアルでは、認証された特定のユーザーに登録されているすべてのデバイスにプッシュ通知を行う方法について説明しました。 その方法では、サポートされる各クライアント プラットフォームに通知を送信するため、複数の要求が必要でした。 Azure Notification Hubs ではテンプレートがサポートされています。テンプレートを使用して、特定のデバイスが通知を受信する方法を指定できます。 この方法により、クロスプラットフォーム通知の送信が簡単になります。

この記事では、テンプレートを活用して、すべてのプラットフォームをターゲットとする通知を送信する方法を示します。 この記事では、単一の要求を使用して、プラットフォームに依存しない通知を送信します。 テンプレートの詳細については、「[Notification Hubs の概要][Templates]」を参照してください。

> [!IMPORTANT]
> Visual Studio 2019 では、Windows Phone 8.1 以前のプロジェクトはサポートされていません。 詳細については、「[Visual Studio 2019 の対象プラットフォームと互換性](/visualstudio/releases/2019/compatibility)」を参照してください。

> [!NOTE]
> Notification Hubs では、デバイスが同じタグを使用して複数のテンプレートを登録できます。 この場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信されます (テンプレートごとに 1 つずつ)。 このプロセスにより、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリでバッジとトースト通知の両方として表示するなど)。により、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリでバッジとトースト通知の両方として表示するなど)。

## <a name="send-cross-platform-notifications-using-templates"></a>テンプレートを使用したクロスプラットフォーム通知の送信

このセクションでは、「[Azure Notification Hubs を使用して特定のユーザーに通知を送信する]」で作成したサンプル コードを使用します。 [GitHub から完全なサンプルをダウンロード](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers)することができます。

テンプレートを使用してクロスプラットフォーム通知を送信するには、次の手順を実行します。

1. Visual Studio の **ソリューション エクスプローラー** で、**Controllers** フォルダーを展開して、*RegisterController.cs* ファイルを開きます。

1. `Put` メソッドで新しい登録を作成するコード ブロックを検索し、`switch` の内容を次のコードに置き換えます。

    ```csharp
    switch (deviceUpdate.Platform)
    {
        case "mpns":
            var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                    "<wp:Toast>" +
                        "<wp:Text1>$(message)</wp:Text1>" +
                    "</wp:Toast> " +
                "</wp:Notification>";
            registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "wns":
            toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
            registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
            break;
        case "apns":
            var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
            registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
            break;
        case "fcm":
            var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
            registration = new FcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }
    ```

    このコードは、プラットフォーム固有のメソッドを呼び出して、ネイティブ登録の代わりにテンプレート登録を作成します。 テンプレート登録はネイティブ登録から派生しているため、既存の登録を変更する必要はありません。

1. **ソリューション エクスプローラー** で **Controllers** フォルダーを展開し、**NotificationsController.cs** ファイルを開きます。 `Post` メソッドを次のコードに置き換えます。

    ```csharp
    public async Task<HttpResponseMessage> Post()
    {
        var user = HttpContext.Current.User.Identity.Name;
        var userTag = "username:" + user;

        var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
        await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

        return Request.CreateResponse(HttpStatusCode.OK);
    }
    ```

    このコードでは、すべてのプラットフォームに同時に通知が送信されます。 ネイティブ ペイロードは指定してません。 Notification Hubs では、登録したテンプレートでの指定内容に従って、提供されたタグ値を使用して適切なペイロードが作成され各デバイスに配信されます。

1. ご利用の Web API プロジェクトを再発行します。

1. もう一度クライアント アプリケーションを実行して、登録に成功したことを確認します。

1. 必要に応じて、クライアント アプリケーションを 2 つ目のデバイスにデプロイしてから、アプリケーションを実行します。 通知が各デバイスに表示されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルはこれで終了です。Notification Hubs とテンプレートの詳細については、次の記事を参照してください。

* テンプレートを使用する場合の別のシナリオについては、[ユニバーサル Windows プラットフォーム アプリケーションを実行している特定の Windows デバイスへのプッシュ通知の送信][Use Notification Hubs to send breaking news]に関するチュートリアルを参照してください。
* テンプレートの詳細については、「[Azure Notification Hubs の概要][Templates]」を参照してください。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://visualstudio.microsoft.com/downloads/

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Azure Notification Hubs を使用して特定のユーザーに通知を送信する]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Notification Hub How to for Windows Store]: /previous-versions/azure/azure-services/jj927170(v=azure.100)