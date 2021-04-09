---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/07/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: bfd5d42d83046c9c5b0bc3a78fabec08da5da646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025842"
---
このセクションでは、.NET コンソール アプリから、タグ付けされたテンプレート通知としてニュース速報を送信します。

1. Visual Studio で、Visual C# の新しいコンソール アプリケーションを作成します。
    1. メニューで、 **[ファイル]**  >  **[新規作成]**  >  **[プロジェクト]** を選択します。
    1. **[新しいプロジェクトの作成]** で、テンプレートのリストで C# の **[コンソール アプリ (.NET Framework)]** を選択し、**[次へ]** を選択します。
    1. アプリの名前を入力します。
    1. **[ソリューション]** で、**[ソリューションに追加]** を選択し、**[作成]** を選択してプロジェクトを作成します。

1. **[ツール]**  >  **[NuGet Package Manager]**  >  **[パッケージ マネージャー コンソール]** の順に選択してから、コンソール ウィンドウで次のコマンドを実行します。

   ```powershell
   Install-Package Microsoft.Azure.NotificationHubs
   ```

   この操作によって、[Microsoft.Azure.NotificationHubs] パッケージを使用して Azure Notification Hubs SDK への参照が追加されます。

1. *Program.cs* ファイルを開き、次の `using` ステートメントを追加します。

   ```csharp
   using Microsoft.Azure.NotificationHubs;
   ```

1. `Program` クラス内で、次のメソッドを追加するか、既にメソッドが指定されている場合は置き換えます。

    ```csharp
    private static async void SendTemplateNotificationAsync()
    {
        // Define the notification hub.
        NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");

        // Apple requires the apns-push-type header for all requests
        var headers = new Dictionary<string, string> {{"apns-push-type", "alert"}};

        // Create an array of breaking news categories.
        var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};

        // Send the notification as a template notification. All template registrations that contain
        // "messageParam" and the proper tags will receive the notifications.
        // This includes APNS, GCM/FCM, WNS, and MPNS template registrations.

        Dictionary<string, string> templateParams = new Dictionary<string, string>();

        foreach (var category in categories)
        {
            templateParams["messageParam"] = "Breaking " + category + " News!";
            await hub.SendTemplateNotificationAsync(templateParams, category);
        }
    }
    ```

   このコードでは、文字列の配列の 6 つのタグのそれぞれに対するテンプレート通知が送信されます。 タグを使用することで、デバイスは登録されているカテゴリに関する通知のみを確実に受信できます。

1. 上記のコードでは、プレースホルダー `<hub name>` と `<connection string with full access>` を、通知ハブの名前と通知ハブのダッシュボードから得た *DefaultFullSharedAccessSignature* の接続文字列に置き換えます。

1. `Main()` メソッド内に、次の行を追加します。

   ```csharp
    SendTemplateNotificationAsync();
    Console.ReadLine();
    ```

1. コンソール アプリケーションをビルドします。

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs REST interface]: /previous-versions/azure/reference/dn223264(v=azure.100)
[Add push notifications for Mobile Apps]: /previous-versions/azure/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/