---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 346f63109e673692d425ede6bdbcb6f633af80bb
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857432"
---
**.NET バックエンド (C#)** :
  
1. Visual Studio でサーバー プロジェクトを右クリックし、 **[NuGet パッケージの管理]** をクリックして `Microsoft.Azure.NotificationHubs` を見つけ、 **[インストール]** をクリックします。 これにより、バックエンドから通知を送信するために必要な Notification Hubs ライブラリがインストールされます。
2. バックエンドの Visual Studio プロジェクトで、 **[コントローラー]**  >  **[TodoItemController.cs]** の順に開きます。 ファイルの先頭に、次の `using` ステートメントを追加します。

    ```csharp
    using Microsoft.Azure.Mobile.Server.Config;
    using Microsoft.Azure.NotificationHubs;
    ```

3. `PostTodoItem` メソッドを次のコードに置き換えます。  

    ```csharp
    public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
    {
        TodoItem current = await InsertAsync(item);
        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;

        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // iOS payload
        var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendAppleNativeNotificationAsync(appleNotificationPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }
        return CreatedAtRoute("Tables", new { id = current.Id }, current);
    }
    ```

4. サーバー プロジェクトを発行します。

**Node.js バックエンド**:

1. バックエンド プロジェクトを設定します。    

2. todoitem.js テーブル スクリプトを次のコードで置き換えます。

    ```javascript
    var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

    var table = azureMobileApps.table();

    // When adding record, send a push notification via APNS
    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK, 
        // see https://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Create a payload that contains the new item Text.
        var payload = "{\"aps\":{\"alert\":\"" + context.item.text + "\"}}";

        // Execute the insert; Push as a post-execute action when results are returned as a Promise.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.apns.send(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });

    module.exports = table;
    ```

3. ローカル コンピューターでファイルを編集するときは、サーバー プロジェクトを再発行します。
