---
title: Azure Notification Hubs を使用してユーザーにクロスプラットフォーム通知を送信する (ASP.NET)
description: Notification Hubs のテンプレートを使用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を説明します。
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 0f92b49c9d77029a9624782b49eb23f7083c49aa
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57863096"
---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>通知ハブによるユーザーへのクロスプラットフォーム通知の送信

前のチュートリアル ([Notification Hubs によるユーザーへの通知]) では、認証された特定のユーザーにより登録されたすべてのデバイスにプッシュ通知を行う方法について説明しました。 そのチュートリアルでは、サポートされる各クライアント プラットフォームに通知を送信するため、複数の要求が必要でした。 Azure Notification Hubs ではテンプレートがサポートされています。テンプレートを使用して、特定のデバイスが通知を受信する方法を指定できます。 この方法により、クロスプラットフォーム通知の送信が簡単になります。

この記事では、テンプレートを活用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を示します。 テンプレートの詳細については、「[Azure Notification Hubs の概要][Templates]」を参照してください。

> [!IMPORTANT]
> Visual Studio 2017 では、Windows Phone 8.1 以前のプロジェクトはサポートされていません。 詳細については、「[Visual Studio 2017 の対象プラットフォームと互換性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)」を参照してください。

> [!NOTE]
> Notification Hubs では、デバイスは、複数のテンプレートを同じタグで登録できます。 この場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信されます (テンプレートごとに 1 つずつ)。 このプロセスにより、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリでバッジとトースト通知の両方として表示するなど)。により、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリでバッジとトースト通知の両方として表示するなど)。

## <a name="send-cross-platform-notifications-using-templates"></a>テンプレートを使用したクロスプラットフォーム通知の送信

テンプレートを使用してクロスプラットフォーム通知を送信するには、次の手順を実行します。

1. Visual Studio のソリューション エクスプローラーで、**Controllers** フォルダーを展開し、RegisterController.cs ファイルを開きます。

2. `Put` メソッドで新しい登録を作成するコード ブロックを検索し、`switch` の内容を次のコードに置き換えます。

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

3. `Notifications` コントローラーで、`sendNotification` メソッドのコードを次のコードに置き換えます。

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

    このコードは、ユーザーがネイティブ ペイロードを指定しなくても、すべてのプラットフォームに同時に通知を送信します。 Notification Hubs は、登録されたテンプレートでの指定内容に従って、提供された "*タグ*" 値を使用して適切なペイロードを作成して各デバイスに配信します。

4. WebApi バックエンド プロジェクトを再発行します。

5. もう一度クライアント アプリケーションを実行し、登録に成功したことを確認します。

6. (省略可能) クライアント アプリケーションを 2 つ目のデバイスにデプロイし、アプリケーションを実行します。
    通知が各デバイスに表示されます。

## <a name="next-steps"></a>次の手順

このチュートリアルを完了したら、以下のトピックで通知ハブとテンプレートの詳細を参照してください。

* [Use Notification Hubs to send breaking news]: Demonstrates another scenario for using templates.
* [Azure Notification Hubs の概要][Templates]:テンプレートに関する追加詳細が含まれています。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Push to users ASP.NET]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Push to users Mobile Services]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Visual Studio 2012 Express for Windows 8]: https://go.microsoft.com/fwlink/?LinkId=257546

[Use Notification Hubs to send breaking news]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: https://go.microsoft.com/fwlink/p/?LinkId=314257
[Notification Hubs によるユーザーへの通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: https://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: https://msdn.microsoft.com/library/windowsazure/jj927172.aspx
