
このセクションでは、.NET コンソール アプリケーションからタグ付けされたテンプレート通知としてニュース速報を送信する方法について説明します。

Mobile Apps を使用している場合は、[Mobile Apps へのプッシュ通知の追加]に関するチュートリアルを参照し、ページの上部でご使用のプラットフォームを選択してください。

Java または PHP を使用する場合は、[Java または PHP から Notification Hubs を使用する方法]に関するページを参照してください。 [Notification Hubs の REST インターフェイス]を使用することで、任意のバックエンドから通知を送信できます。

「[Notification Hubs の使用]」の完了時に、通知を送信するためのコンソール アプリケーションを作成した場合は、手順 1. ～ 3. をスキップします。

1. Visual Studio で、Visual C# の新しいコンソール アプリケーションを作成します。
   
       ![][13]
2. Visual Studio のメイン メニューで、**[ツール]**、**[ライブラリ パッケージ マネージャー]**、**[パッケージ マネージャー コンソール]** の順にクリックし、コンソール ウィンドウで次のコマンドを入力して、**Enter** キーを押します。
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    これにより [Microsoft.Azure.Notification Hubs NuGet パッケージ]を利用して Azure Notification Hubs SDK に参照が追加されます。
3. Program.cs ファイルを開き、次の `using` ステートメントを追加します。
   
        using Microsoft.Azure.NotificationHubs;
4. `Program` クラス内で、次のメソッドを追加するか、既にメソッドが指定されている場合は置き換えます。
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Sending the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    このコードでは、文字列の配列の 6 つのタグのそれぞれに対するテンプレート通知が送信されます。 タグを使用することで、デバイスは登録されているカテゴリに関する通知のみを確実に受信できます。
5. 上のコードで、プレースホルダー `<hub name>` と `<connection string with full access>` を、通知ハブの名前と通知ハブのダッシュボードの *DefaultFullSharedAccessSignature* の接続文字列に置き換えます。
6. **Main** メソッド内に、次の行を追加します。
   
         SendTemplateNotificationAsync();
         Console.ReadLine();
7. コンソール アプリケーションをビルドします。

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Notification Hubs の使用]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Notification Hubs の REST インターフェイス]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Mobile Apps へのプッシュ通知の追加]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[Java または PHP から Notification Hubs を使用する方法]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[Microsoft.Azure.Notification Hubs NuGet パッケージ]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/


<!--HONumber=Nov16_HO3-->


