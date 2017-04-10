

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Apps などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドで利用できない場合、REST API を直接使用して通知メッセージを送信できます。 

通知の送信方法を確認できるチュートリアルの一覧を次に示します。

* Azure Mobile Apps: Notification Hubs に統合されている Mobile Apps バックエンドから通知を送信する方法の例については、「 [iOS アプリへのプッシュ通知の追加](../articles/app-service-mobile/app-service-mobile-ios-get-started-push.md)」を参照してください。  
* ASP.NET : [Notification Hubs を使用したユーザーへのプッシュ通知](../articles/notification-hubs/notification-hubs-aspnet-backend-ios-apple-apns-notification.md)
* Azure Notification Hub Java SDK: Java からの通知を送信するには「 [Java から Notification Hubs を使用する方法](../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md) 」を参照してください。 これは Android の開発用に Eclipse でテストされています。
* PHP: [PHP から Notification Hubs を使用する方法](../articles/notification-hubs/notification-hubs-php-push-notification-tutorial.md)

チュートリアルの次のセクションでは、 [Notification Hub REST インターフェイス](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) を使用してアプリで直接通知メッセージを送信する方法について説明します。 すべての登録デバイスは、任意のデバイスから送信された通知を受信します。  



<!--HONumber=Jan17_HO1-->


