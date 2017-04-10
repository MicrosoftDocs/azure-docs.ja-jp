---
title: "Notification Hubs ローカライズ ニュース速報のチュートリアル (iOS 向け)"
description: "Azure Service Bus Notification Hubs を使用して、ローカライズしたニュース速報通知を送信する方法を説明します (iOS)。"
services: notification-hubs
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
ms.assetid: 484914b5-e081-4a05-a84a-798bbd89d428
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd2b7d9dfd4f432bbcbaa3ed76f8bec0b9677e17


---
# <a name="use-notification-hubs-to-send-localized-breaking-news-to-ios-devices"></a>Notification Hubs を使用した iOS デバイスへのローカライズ ニュース速報の送信
> [!div class="op_single_selector"]
> * [Windows ストア C#](notification-hubs-windows-store-dotnet-xplat-localized-wns-push-notification.md)
> * [iOS](notification-hubs-ios-xplat-localized-apns-push-notification.md)
> 
> 

## <a name="overview"></a>概要
このトピックでは、Azure Notification Hubs の [テンプレート](notification-hubs-templates-cross-platform-push-messages.md) 機能を使用して、言語およびデバイスごとにローカライズしたニュース速報通知をブロードキャストする方法について説明します。 このチュートリアルでは、「 [通知ハブを使用したニュース速報の送信]」で作成した iOS アプリケーションを使用します。 完了すると、興味のあるニュース速報カテゴリに登録して受信する通知の言語を指定し、選択したカテゴリのその言語のプッシュ通知だけを受信できるようになります。

このシナリオは次の 2 つに分けられます。

* iOS アプリケーションで、クライアント デバイスが言語を指定し、さまざまなニュース速報カテゴリを購読できるようになります。
* Azure Notification Hubs の**タグ**機能と**テンプレート**機能を使用して、バックエンドから通知がブロードキャストされます。

## <a name="prerequisites"></a>前提条件
「[通知ハブを使用したニュース速報の送信]」のチュートリアルを完了し、コードが使用可能な状態になっている必要があります。このチュートリアルでは、そのコードに基づいているためです。

必要に応じて Visual Studio 2012 以降を使用できます。

## <a name="template-concepts"></a>テンプレートの概念
「 [通知ハブを使用したニュース速報の送信] 」では、 **タグ** を使用してさまざまなニュース カテゴリの通知を購読するアプリケーションを構築しました。
しかし、多くのアプリケーションは複数の市場をターゲットとしており、ローカライズが必要です。 これは、通知自体の内容をローカライズし、適切なデバイス セットに配信する必要があることを意味します。
このトピックでは、Notification Hubs の **テンプレート** 機能を使用して、ローカライズしたニュース速報通知を簡単に配信する方法について説明します。

注: ローカライズした通知を送信する 1 つの方法として、各タグの複数のバージョンを作成する方法があります。 たとえば、ワールド ニュースで英語、フランス語、標準中国語をサポートするには、3 つのタグ ("world_en"、"world_fr"、"world_ch") が必要です。 その後、ワールド ニュースのローカライズ バージョンをこれらの各タグに送信する必要があります。 このトピックでは、テンプレートを使用することでタグの増加を抑制し、複数のメッセージを送信しなくてもよいようにします。

大まかに言えば、テンプレートとは、特定のデバイスが通知をどのように受信するかを特定するための手段です。 テンプレートは、アプリケーション バックエンドにより送信されるメッセージの一部となっているプロパティを参照することで、正確なペイロードを指定します。 このケースでは、サポートされるすべての言語を含む、ロケールにとらわれないメッセージを送信します。

    {
        "News_English": "...",
        "News_French": "...",
        "News_Mandarin": "..."
    }

次に、デバイスが、適切なプロパティを参照するテンプレートを登録するようにします。 たとえば、フランス語ニュースを登録する iOS アプリケーションは、次のテンプレートを登録します。

    {
        aps:{
            alert: "$(News_French)"
        }
    }

テンプレートは有用な機能です。詳細については、「[テンプレート](notification-hubs-templates-cross-platform-push-messages.md)」の記事を参照してください。

## <a name="the-app-user-interface"></a>アプリケーションのユーザー インターフェイス
ここでは、「 [通知ハブを使用したニュース速報の送信] 」で作成したニュース速報アプリケーションを変更し、テンプレートを使用してローカライズしたニュース速報を送信します。

MainStoryboard_iPhone.storyboard で、サポートする 3 つの言語 (英語、フランス語、標準中国語) を備えた Segmented Control を追加します。

![][13]

次に、以下に示すように ViewController.h に IBOutlet を追加します。

![][14]

## <a name="building-the-ios-app"></a>iOS アプリケーションを構築する
1. Notification.h で、 *retrieveLocale* メソッドを追加し、以下に示すように store メソッドと subscribe メソッドを変更します。
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet*) categories completion: (void (^)(NSError* error))completion;
   
        - (void) subscribeWithLocale:(int) locale categories:(NSSet*) categories completion:(void (^)(NSError *))completion;
   
        - (NSSet*) retrieveCategories;
   
        - (int) retrieveLocale;
   
    Notification.m で、ロケール パラメーターを追加してユーザーの既定の設定に格納することで、 *storeCategoriesAndSubscribe* メソッドを変更します。
   
        - (void) storeCategoriesAndSubscribeWithLocale:(int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
            [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];
            [defaults setInteger:locale forKey:@"BreakingNewsLocale"];
            [defaults synchronize];
   
            [self subscribeWithLocale: locale categories:categories completion:completion];
        }
   
    次に、 *subscribe* メソッドを変更してロケールを追加します。
   
        - (void) subscribeWithLocale: (int) locale categories:(NSSet *)categories completion:(void (^)(NSError *))completion{
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:@"<connection string>" notificationHubPath:@"<hub name>"];
   
            NSString* localeString;
            switch (locale) {
                case 0:
                    localeString = @"English";
                    break;
                case 1:
                    localeString = @"French";
                    break;
                case 2:
                    localeString = @"Mandarin";
                    break;
            }
   
            NSString* template = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"$(News_%@)\"},\"inAppMessage\":\"$(News_%@)\"}", localeString, localeString];
   
            [hub registerTemplateWithDeviceToken:self.deviceToken name:@"localizednewsTemplate" jsonBodyTemplate:template expiryTemplate:@"0" tags:categories completion:completion];
        }
   
    ここで、*registerNativeWithDeviceToken* の代わりに *registerTemplateWithDeviceToken* メソッドをどのように使用しているかに注目してください。 テンプレートを登録するときは、json テンプレートに加えて、テンプレートの名前も指定する必要があります (アプリケーションにより複数のテンプレートが登録されることがあるため)。 それらのニュースの通知を確実に受信できるようにするため、カテゴリをタグとして登録してください。
   
    ユーザーの既定の設定からロケールを取得するメソッドを追加します。
   
        - (int) retrieveLocale {
            NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
   
            int locale = [defaults integerForKey:@"BreakingNewsLocale"];
   
            return locale < 0?0:locale;
        }
2. ここまでで、Notifications クラスを変更したので、ViewController が新しい UISegmentControl を使用することを確認する必要があります。 *viewDidLoad* メソッドに次の行を追加し、現在選択されているロケールが表示されるようにします。
   
        self.Locale.selectedSegmentIndex = [notifications retrieveLocale];
   
    次に、*subscribe* メソッドで、*storeCategoriesAndSubscribe* への呼び出しを次のように変更します。
   
        [notifications storeCategoriesAndSubscribeWithLocale: self.Locale.selectedSegmentIndex categories:[NSSet setWithArray:categories] completion: ^(NSError* error) {
            if (!error) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                                      @"Subscribed!" delegate:nil cancelButtonTitle:
                                      @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"Error subscribing: %@", error);
            }
        }];
3. 最後に、アプリケーションの起動時に登録を正しく更新できるように、AppDelegate.m で *didRegisterForRemoteNotificationsWithDeviceToken* メソッドを更新します。 通知の *subscribe* メソッドへの呼び出しを、次のように変更します。
   
        NSSet* categories = [self.notifications retrieveCategories];
        int locale = [self.notifications retrieveLocale];
        [self.notifications subscribeWithLocale: locale categories:categories completion:^(NSError* error) {
            if (error != nil) {
                NSLog(@"Error registering for notifications: %@", error);
            }
        }];

## <a name="optional-send-localized-template-notifications-from-net-console-app"></a>(省略可能) ローカライズされたテンプレート通知を .NET コンソール アプリケーションから送信します。
[!INCLUDE [notification-hubs-localized-back-end](../../includes/notification-hubs-localized-back-end.md)]

## <a name="optional-send-localized-template-notifications-from-the-device"></a>(省略可能) ローカライズされたテンプレート通知をデバイスから送信する
Visual Studio にアクセスできない場合でも、ローカライズされたテンプレート通知をデバイス上のアプリから直接送信することをテストできます。  単に、前のチュートリアルで定義した `SendNotificationRESTAPI` メソッドにローカライズされたテンプレートのパラメーターを追加できます。

        - (void)SendNotificationRESTAPI:(NSString*)categoryTag
        {
            NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                     defaultSessionConfiguration] delegate:nil delegateQueue:nil];

            NSString *json;

            // Construct the messages REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                               HUBNAME, API_VERSION]];

            // Generated the token to be used in the authorization header.
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

            //Create the request to add the template notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];

            // Add the category as a tag
            [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

            // Template notification
            json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\","
                    \"News_English\":\"Breaking %@ News in English : %@\","
                    \"News_French\":\"Breaking %@ News in French : %@\","
                    \"News_Mandarin\":\"Breaking %@ News in Mandarin : %@\","
                    categoryTag, self.notificationMessage.text,
                    categoryTag, self.notificationMessage.text,  // insert English localized news here
                    categoryTag, self.notificationMessage.text,  // insert French localized news here
                    categoryTag, self.notificationMessage.text]; // insert Mandarin localized news here

            // Signify template notification format
            [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

            // JSON Content-Type
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                       completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
               {
               NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                   if (error || httpResponse.statusCode != 200)
                   {
                       NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                   }
                   if (data != NULL)
                   {
                       //xmlParser = [[NSXMLParser alloc] initWithData:data];
                       //[xmlParser setDelegate:self];
                       //[xmlParser parse];
                   }
               }];

            [dataTask resume];
        }




## <a name="next-steps"></a>次のステップ
テンプレートの使用方法の詳細については、以下を参照してください。

* [Notification Hubs によるユーザーへの通知: ASP.NET]
* [Notification Hubs によるユーザーへの通知: Mobile Services]

<!-- Images. -->

[13]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized1.png
[14]: ./media/notification-hubs-ios-send-localized-breaking-news/ios_localized2.png






<!-- URLs. -->
[方法: Service Bus Notification Hubs (iOS アプリケーション)]: http://msdn.microsoft.com/library/jj927168.aspx
[通知ハブを使用したニュース速報の送信]: /manage/services/notification-hubs/breaking-news-ios
[モバイル サービス]: /develop/mobile/tutorials/get-started
[Notification Hubs によるユーザーへの通知: ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs によるユーザーへの通知: Mobile Services]: /manage/services/notification-hubs/notify-users
[アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Mobile Services の使用]: /develop/mobile/tutorials/get-started/#create-new-service
[データの使用]: /develop/mobile/tutorials/get-started-with-data-ios
[認証の概要]: /develop/mobile/tutorials/get-started-with-users-ios
[プッシュ通知の使用]: /develop/mobile/tutorials/get-started-with-push-ios
[アプリケーション ユーザーへのプッシュ通知]: /develop/mobile/tutorials/push-notifications-to-users-ios
[スクリプトを使用したユーザーの認証]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[JavaScript と HTML]: ../get-started-with-push-js.md

[Mobile Services の Windows 開発者プレビュー登録の手順]: ../mobile-services-windows-developer-preview-registration.md
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS 向けの Notification Hubs の使用方法]: http://msdn.microsoft.com/library/jj927168.aspx



<!--HONumber=Nov16_HO3-->


