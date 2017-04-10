



テンプレート通知を送信する場合、一連のプロファイルの指定だけが必要になります。この場合は、最新ニュースのローカライズされたバージョンを含む一連のプロパティを送信します。次に例を示します。

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


このセクションでは、コンソール アプリケーションを使用して通知を送信する方法について説明します。

使用されるコードは、Windows ストアと iOS デバイスの両方に対してブロードキャストされます。これは、バックエンドはサポートされているすべてのデバイスにブロードキャストできるためです。

### <a name="to-send-notifications-using-a-c-console-app"></a>C# コンソール アプリケーションを使用して通知を送信するには
次のコードを使用して、以前に作成したコンソール アプリケーションで `SendTemplateNotificationAsync` メソッドを変更します。 この場合、異なるロケールやプラットフォームに対して複数の通知を送信する必要はありません。

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


この単純な呼び出しでは、プラットフォームに関係なく、ローカライズされた各ニュースが**すべての**デバイスに配信されます。これは、通知ハブが適切なネイティブ ペイロードを作成し、そのペイロードを特定のタグにサブスクライブされているすべてのデバイスに配信するためです。

### <a name="sending-the-notification-with-mobile-services"></a>Mobile Services を使用した通知の送信
モバイル サービス スケジューラでは、次のスクリプトを使用できます。

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });




<!--HONumber=Nov16_HO3-->


