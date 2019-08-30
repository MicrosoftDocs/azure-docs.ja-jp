---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 675ad278cb8bdc0ced4eff3bd77572f44c9808fc
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857431"
---
このセクションでは、Mobile Apps の既存のバックエンド プロジェクトのコードを更新して、新しい項目が追加されるたびにプッシュ通知を送信するようにします。 このプロセスは、Azure Notification Hubs の[テンプレート](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)機能を使用しており、クロスプラットフォームのプッシュを有効にします。 テンプレートを使用してさまざまなクライアントがプッシュ通知に登録されるため、1 つの汎用プッシュがすべてのクライアント プラットフォームに届きます。

バックエンド プロジェクトの種類 ([.NET バックエンド](#dotnet)または [Node.js バックエンド](#nodejs)) に応じた次のいずれかの手順を選択します。

### <a name="dotnet"></a>.NET バックエンド プロジェクト

1. Visual Studio で、サーバー プロジェクトを右クリックします。 次に、 **[NuGet パッケージの管理]** を選択します。 `Microsoft.Azure.NotificationHubs` を検索し、 **[インストール]** を選択します。 このプロセスにより、バックエンドから通知を送信するための Notification Hubs ライブラリがインストールされます。
2. サーバー プロジェクトで、 **[コントローラー]**  >  **[TodoItemController.cs]** の順に開きます。 次の using ステートメントを追加します。

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

    // Get the Notification Hubs credentials for the mobile app.
    string notificationHubName = settings.NotificationHubName;
    string notificationHubConnection = settings
        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

    // Create a new Notification Hub client.
    NotificationHubClient hub = NotificationHubClient
    .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

    // Send the message so that all template registrations that contain "messageParam"
    // receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string,string> templateParams = new Dictionary<string,string>();
    templateParams["messageParam"] = item.Text + " was added to the list.";

    try
    {
        // Send the push notification and log the results.
        var result = await hub.SendTemplateNotificationAsync(templateParams);

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

    このプロセスにより、新しい項目が挿入されたときに、item.Text を含むテンプレート通知が送信されます。

4. サーバー プロジェクトを発行します。

### <a name="nodejs"></a>Node.js バックエンド プロジェクト

1. バックエンド プロジェクトを設定します。
2. todoitem.js 内の既存のコードを、次のコードに置き換えます。

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
    promises = require('azure-mobile-apps/src/utilities/promises'),
    logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    table.insert(function (context) {
    // For more information about the Notification Hubs JavaScript SDK,
    // see https://aka.ms/nodejshubs.
    logger.info('Running TodoItem.insert');

    // Define the template payload.
    var payload = '{"messageParam": "' + context.item.text + '" }';  

    // Execute the insert. The insert returns the results as a promise.
    // Do the push as a post-execute action within the promise flow.
    return context.execute()
        .then(function (results) {
            // Only do the push if configured.
            if (context.push) {
                // Send a template notification.
                context.push.send(null, payload, function (error) {
                    if (error) {
                        logger.error('Error while sending push notification: ', error);
                    } else {
                        logger.info('Push notification sent successfully!');
                    }
                });
            }
            // Don't forget to return the results from the context.execute().
            return results;
        })
        .catch(function (error) {
            logger.error('Error while running context.execute: ', error);
        });
    });

    module.exports = table;  
    ```

    このプロセスにより、新しい項目が挿入されたときに、item.text を含むテンプレート通知が送信されます。

3. ローカル コンピューターでファイルを編集するときは、サーバー プロジェクトを再発行します。
