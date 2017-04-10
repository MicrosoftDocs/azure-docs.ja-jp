このセクションでは、Mobile Apps の既存のバックエンド プロジェクトのコードを更新して、新しい項目が追加されるたびにプッシュ通知を送信するようにします。 これは、Azure Notification Hubs の[テンプレート](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md)機能を使用しており、クロスプラットフォームのプッシュを有効にします。 テンプレートを使用してさまざまなクライアントがプッシュ通知に登録されるため、1 つの汎用プッシュがすべてのクライアント プラットフォームに届きます。

バックエンド プロジェクトの種類 ([.NET バックエンド](#dotnet)または [Node.js バックエンド](#nodejs)) に応じた次のいずれかの手順を選択します。

### <a name="a-namedotnetanet-back-end-project"></a><a name="dotnet"></a>.NET バックエンド プロジェクト
1. Visual Studio でサーバー プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 `Microsoft.Azure.NotificationHubs` を検索し、**[インストール]** をクリックします。 これにより、バックエンドから通知を送信するために Notification Hubs ライブラリがインストールされます。
2. サーバー プロジェクトで、**Controllers** > **TodoItemController.cs** の順に開き、次の using ステートメントを追加します。

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
3. **PostTodoItem** メソッドで、次のコードを **InsertAsync** に対する呼び出しの後ろに追加します。  

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

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
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

    これにより、新しい項目が挿入された場合には item.Text を含むテンプレート通知が送信されます。
4. サーバー プロジェクトを発行します。

### <a name="a-namenodejsanodejs-back-end-project"></a><a name="nodejs"></a>Node.js バックエンド プロジェクト
1. これをまだ行っていない場合は、[クイック スタート バックエンド プロジェクトをダウンロードする](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)か、[Azure Portal でオンライン エディター](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor)を使用します。
2. todoitem.js 内の既存のコードを次のコードに置き換えます。

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
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
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;  

    これにより、新しい項目が挿入された場合には item.Text を含むテンプレート通知が送信されます。
3. ローカル コンピューターでファイルを編集するときは、サーバー プロジェクトを再発行します。


<!--HONumber=Dec16_HO2-->


