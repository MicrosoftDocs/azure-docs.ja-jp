---
title: "Notification Hubs によるユーザーへのクロスプラットフォーム通知の送信 (ASP.NET)"
description: "Notification Hubs のテンプレートを使用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を説明します。"
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 11d2131b-f683-47fd-a691-4cdfc696f62b
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: multiple
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: ef971fcfe68978ea9ce0810c69efbe134bb15f8a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>通知ハブによるユーザーへのクロスプラットフォーム通知の送信
前のチュートリアル「[通知ハブによるユーザーへの通知]」では、認証された特定のユーザーにより登録されたすべてのデバイスにプッシュ通知を行う方法について説明しました。 そのチュートリアルでは、サポートされる各クライアント プラットフォームに通知を送信するため、複数の要求が必要でした。 通知ハブでは、テンプレートがサポートされています。テンプレートを使用すると、特定のデバイスが通知を受信する方法を指定できます。 これにより、クロスプラットフォーム通知の送信が簡単になります。 このトピックでは、テンプレートを活用して、すべてのプラットフォームをターゲットとするプラットフォームにとらわれない通知を 1 つの要求で送信する方法を示します。 テンプレートの詳細については、「[Azure Notification Hubs の概要][Templates]」を参照してください。
> [!IMPORTANT]
> Visual Studio 2017 を使用している場合、Windows Phone 8.1 以前のプロジェクトはサポートされていません。 詳細については、「[Visual Studio 2017 の対象プラットフォームと互換性](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)」を参照してください。

> [!NOTE]
> Notification Hubs を使用すると、デバイスでは同じタグを持つ複数のテンプレートを登録できます。 この場合、そのタグをターゲットとするメッセージが受信されると複数の通知がデバイスに配信されます (テンプレートごとに 1 つずつ)。 これにより、複数のビジュアル通知に同じメッセージを表示することが可能になります (Windows ストア アプリケーションでバッジおよびトースト通知の両方として表示するなど)。
> 
> 

次の手順を実行し、テンプレートを使用してクロスプラットフォーム通知を送信します。

1. Visual Studio のソリューション エクスプローラーで、 **Controllers** フォルダーを展開し、RegisterController.cs ファイルを開きます。
2. **Put** メソッドで新しい登録を作成するコード ブロックを見つけて、`switch` コンテンツを次のコードに置き換えます。
   
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
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
   
    このコードは、プラットフォーム固有のメソッドを呼び出して、ネイティブ登録の代わりにテンプレート登録を作成します。 テンプレート登録がネイティブ登録から派生している場合、既存の登録を変更する必要はありません。
3. **Notifications** コントローラーで、**sendNotification** メソッドを次のコードに置き換えます。
   
        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;
   
            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);
   
            return Request.CreateResponse(HttpStatusCode.OK);
        }
   
    このコードは、ネイティブ ペイロードを指定しなくても、すべてのプラットフォームに通知を同時に送信します。 Notification Hubs は、登録されたテンプレートでの指定内容に従って、提供された "*タグ*" 値を使用して適切なペイロードを作成して各デバイスに配信します。
4. WebApi バックエンド プロジェクトを再発行します。
5. もう一度クライアント アプリケーションを実行し、登録に成功したことを確認します。
6. (省略可能) クライアント アプリケーションを 2 つ目のデバイスにデプロイし、アプリケーションを実行します。
   
    各デバイスに通知が表示される点に注目してください。

## <a name="next-steps"></a>次のステップ
このチュートリアルを完了したら、以下のトピックで通知ハブとテンプレートの詳細を参照してください。

* **[Notification Hubs を使用したニュース速報の送信]** <br/>別のテンプレート使用シナリオのデモンストレーションを行います。
* **[Azure Notification Hubs の概要][Templates]**<br/>この概要トピックでは、テンプレートについて詳細に説明されています。

<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Notification Hubs を使用したニュース速報の送信]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[通知ハブによるユーザーへの通知]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx

