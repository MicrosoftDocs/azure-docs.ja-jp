---
title: "Azure Notification Hubs の安全なプッシュ"
description: "セキュリティで保護されたプッシュ通知を Azure から iOS アプリに送信する方法について説明します。 コード サンプルは Objective-C と C# で記述されています。"
documentationcenter: ios
author: ysxu
manager: erikre
editor: 
services: notification-hubs
ms.assetid: 17d42b0a-2c80-4e35-a1ed-ed510d19f4b4
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e5f09fb3716303bb21fe7442aa6fa8832174838e


---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs の安全なプッシュ
> [!div class="op_single_selector"]
> * [Windows ユニバーサル](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)
> 
> 

## <a name="overview"></a>概要
Microsoft Azure でプッシュ通知がサポートされたことで、マルチプラットフォームに対応し、簡単に使用できる、スケールアウトされたプッシュ通知インフラストラクチャを利用できるようになりました。これにより、モバイル プラットフォーム向けアプリケーション (コンシューマー用途およびエンタープライズ用途) にプッシュ通知機能を実装する作業が大幅に簡略化されます。

規制やセキュリティの制約により、アプリケーションでは、標準のプッシュ通知インフラストラクチャからは転送できないものを通知に含める必要がある場合があります。 このチュートリアルでは、クライアントのデバイスとアプリケーションのバックエンドとの間の安全で認証された接続を通して機密情報を送信することによって、同じエクスペリエンスを実現する方法について説明します。

大まかには、フローは次のようになります。

1. アプリケーションのバックエンド:
   * バックエンド データベースに安全なペイロードを格納します。
   * この通知の ID をデバイスに送信します (安全でない情報は送信しません)。
2. 通知を受け取ったときのデバイスのアプリケーション:
   * デバイスは、安全なペイロードを要求するバックエンドにアクセスします。
   * アプリケーションはデバイスに通知としてペイロードを表示できます。

重要なのは、前のフロー (およびこのチュートリアル) では、デバイスは、ユーザーがログインした後、認証トークンをローカル ストレージに保存すると想定していることです。 デバイスはこのトークンを使用して通知の安全なペイロードを取得できるため、これによって完全にシームレスなエクスペリエンスが保証されます。 アプリケーションがデバイスに認証トークンを格納しない、またはそれらのトークンが期限切れの場合、デバイスのアプリケーションは、通知を受け取ったときにアプリケーションの起動を促す一般的な通知を表示する必要があります。 その後、アプリケーションはユーザーを認証し、通知ペイロードを表示します。

この安全なプッシュのチュートリアルでは、プッシュ通知を安全に送信する方法を説明します。 このチュートリアルは「 [ユーザーへの通知](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 」チュートリアルに基づいて記述されているため、先にそのチュートリアルでの手順を完了してください。

> [!NOTE]
> このチュートリアルでは、「 [Notification Hubs の使用 (iOS)](notification-hubs-ios-apple-push-notification-apns-get-started.md)」での説明に従って通知が作成され、構成されていると想定しています。
> 
> 

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>iOS プロジェクトを変更する
通知の *id* だけを送信するようにアプリケーション バックエンドを変更したため、iOS アプリケーションがその通知を処理し、バックエンドをコールバックしてから安全なメッセージを取得して表示するように変更する必要があります。

そのためには、アプリケーション バックエンドから安全なコンテンツを取得するロジックを作成する必要があります。

1. **AppDelegate.m**で、バックエンドから送信された通知 ID を処理できるように、必ずアプリをサイレント通知に登録します。 **UIRemoteNotificationTypeNewsstandContentAvailability** オプションを didFinishLaunchingWithOptions 内に追加します。
   
        [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
2. **AppDelegate.m** で、次の宣言を使用して実装セクションを先頭に追加します。
   
        @interface AppDelegate ()
        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        @end
3. 次に、実装セクションに次のコードを追加します。プレースホルダー `{back-end endpoint}` を前に取得したバックエンドのエンドポイントに置き換えます。

```
        NSString *const GetNotificationEndpoint = @"{back-end endpoint}/api/notifications";

        - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
        {
            // check if authenticated
            ANHViewController* rvc = (ANHViewController*) self.window.rootViewController;
            NSString* authenticationHeader = rvc.registerClient.authenticationHeader;
            if (!authenticationHeader) return;


            NSURLSession* session = [NSURLSession
                                     sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                                     delegate:nil
                                     delegateQueue:nil];


            NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/%d", GetNotificationEndpoint, payloadId]];
            NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
            [request setHTTPMethod:@"GET"];
            NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@", authenticationHeader];
            [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (!error && httpResponse.statusCode == 200)
                {
                    NSLog(@"Received secure payload: %@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);

                    NSMutableDictionary *json = [NSJSONSerialization JSONObjectWithData:data options: NSJSONReadingMutableContainers error: &error];

                    completion([json objectForKey:@"Payload"], nil);
                }
                else
                {
                    NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                    if (error)
                        completion(nil, error);
                    else {
                        completion(nil, [NSError errorWithDomain:@"APICall" code:httpResponse.statusCode userInfo:nil]);
                    }
                }
            }];
            [dataTask resume];
        }
```

    This method calls your app back-end to retrieve the notification content using the credentials stored in the shared preferences.

1. ここでは、受信通知を処理し、上記のメソッドを使用して表示するコンテンツを取得する必要があります。 最初に、プッシュ通知を受信するときに iOS アプリケーションがバックグラウンドで実行されるようにします。 **XCode** で、左側のパネルのアプリケーション プロジェクトを選択し、中央のウィンドウの **[ターゲット]** セクションでメイン アプリケーション ターゲットをクリックします。
2. 次に、中央ウィンドウの上部で **[機能]** タブをクリックし、**[リモート通知]** チェック ボックスをオンにします。
   
    ![][IOS1]
3. **AppDelegate.m** で、次のメソッドを追加してプッシュ通知を処理します。
   
        -(void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo fetchCompletionHandler:(void (^)(UIBackgroundFetchResult))completionHandler
        {
            NSLog(@"%@", userInfo);
   
            [self retrieveSecurePayloadWithId:[[userInfo objectForKey:@"secureId"] intValue] completion:^(NSString * payload, NSError *error) {
                if (!error) {
                    // show local notification
                    UILocalNotification* localNotification = [[UILocalNotification alloc] init];
                    localNotification.fireDate = [NSDate dateWithTimeIntervalSinceNow:0];
                    localNotification.alertBody = payload;
                    localNotification.timeZone = [NSTimeZone defaultTimeZone];
                    [[UIApplication sharedApplication] scheduleLocalNotification:localNotification];
   
                    completionHandler(UIBackgroundFetchResultNewData);
                } else {
                    completionHandler(UIBackgroundFetchResultFailed);
                }
            }];
   
        }
   
    認証ヘッダー プロパティが不明な場合やバックエンドによって拒否された場合などに対応できるようにすることをお勧めします。 こうしたケースに対する特定の処理は、ターゲット ユーザーのエクスペリエンスによって異なります。 1 つのオプションとしては、ユーザーに通知と共に認証を求める一般的なプロンプトを表示して、実際の通知を取得する方法があります。

## <a name="run-the-application"></a>アプリケーションの実行
アプリケーションを実行するには、以下の手順に従います。

1. XCode を使用して、物理 iOS デバイスでアプリケーションを実行します (プッシュ通知はシミュレーターでは機能しません)。
2. iOS アプリケーションの UI で、ユーザー名とパスワードを入力します。 文字列は任意ですが、値は同じである必要があります。
3. iOS アプリケーションの UI で、 **[ログイン]**をクリックします。 次に、 **[プッシュを送信する]**をクリックします。 通知センターに安全な通知が表示されます。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png



<!--HONumber=Dec16_HO2-->


