---
title: iOS 用の Azure Notification Hubs の安全なプッシュ
description: セキュリティで保護されたプッシュ通知を Azure から iOS アプリに送信する方法について説明します。 コード サンプルは Objective-C と C# で記述されています。
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/17/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: a6c85ba017656bd312ddfe3d5f6d98014a3dc89a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90090348"
---
# <a name="azure-notification-hubs-secure-push"></a>Azure Notification Hubs の安全なプッシュ

> [!div class="op_single_selector"]
> * [Windows ユニバーサル](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

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

重要なのは、前のフロー (およびこのチュートリアル) では、デバイスは、ユーザーがログインした後、認証トークンをローカル ストレージに保存すると想定していることです。 これにより、デバイスはこのトークンを使用して通知のセキュリティ保護されたペイロードを取得できるため、シームレスなエクスペリエンスが保証されます。 アプリケーションがデバイスに認証トークンを格納しない、またはそれらのトークンが期限切れの場合、デバイスのアプリは、通知を受け取ったときに、ユーザーにアプリの起動を促す一般的な通知を表示する必要があります。 その後、アプリケーションはユーザーを認証し、通知ペイロードを表示します。

この安全なプッシュのチュートリアルでは、プッシュ通知を安全に送信する方法を説明します。 このチュートリアルは「 [ユーザーへの通知](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 」チュートリアルに基づいて記述されているため、先にそのチュートリアルでの手順を完了してください。

> [!NOTE]
> このチュートリアルでは、「[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md)」の説明のとおり通知ハブを作成し、構成したと想定しています。

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-ios-project"></a>iOS プロジェクトを変更する

これで、通知の *ID* のみを送信するようにアプリ バックエンドが変更されたので、その通知を処理し、バックエンドをコールバックして、表示されるセキュリティ保護されたメッセージを取得するように iOS アプリを変更する必要があります。

そのためには、アプリケーション バックエンドから安全なコンテンツを取得するロジックを作成する必要があります。

1. `AppDelegate.m` で、アプリが確実にサイレント通知に登録して、バックエンドから送信された通知 ID を処理するようにしてください。 didFinishLaunchingWithOptions に `UIRemoteNotificationTypeNewsstandContentAvailability` オプションを追加します。

    ```objc
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound | UIRemoteNotificationTypeNewsstandContentAvailability];
    ```
2. `AppDelegate.m` で、次の宣言を使用して実装セクションを先頭に追加します。

    ```objc
    @interface AppDelegate ()
    - (void) retrieveSecurePayloadWithId:(int)payloadId completion: (void(^)(NSString*, NSError*)) completion;
    @end
    ```
3. 次に、実装セクションに次のコードを追加します。プレースホルダー `{back-end endpoint}` を前に取得したバックエンドのエンドポイントに置き換えます。

    ```objc
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

    このメソッドは、共有設定に格納された資格情報によってアプリケーション バックエンドを呼び出して通知コンテンツを取得します。

4. ここでは、受信通知を処理し、上記のメソッドを使用して、表示するコンテンツを取得します。 最初に、プッシュ通知を受信するときに iOS アプリがバックグラウンドで実行されるようにします。 **XCode** で、左側のパネルのアプリケーション プロジェクトを選択し、中央のウィンドウの **[ターゲット]** セクションでメイン アプリケーション ターゲットをクリックします。
5. 次に、中央ペインの上部にある **[機能]** タブをクリックし、 **[リモート通知]** ボックスをオンにします。

    ![アプリ プロジェクトが選択され、[機能] タブが開いている、XCode のスクリーンショット。 [リモート通知] チェック ボックスがオンになっている。][IOS1]

6. `AppDelegate.m` で、次のメソッドを追加してプッシュ通知を処理します。

    ```objc
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
    ```

    認証ヘッダー プロパティが不明な場合やバックエンドによって拒否された場合などに対応できるようにすることをお勧めします。 これらの場合への具体的な対処は、ほぼターゲット ユーザーのエクスペリエンスに依存します。 1 つのオプションとしては、ユーザーに通知と共に認証を求める一般的なプロンプトを表示して、実際の通知を取得する方法があります。

## <a name="run-the-application"></a>アプリケーションの実行

アプリケーションを実行するには、以下の手順に従います。

1. XCode を使用して、物理 iOS デバイスでアプリケーションを実行します (プッシュ通知はシミュレーターでは機能しません)。
2. iOS アプリケーションの UI で、ユーザー名とパスワードを入力します。 文字列は任意ですが、値は同じである必要があります。
3. iOS アプリケーションの UI で、 **[ログイン]** をクリックします。 次に、 **[プッシュを送信する]** をクリックします。 通知センターに安全な通知が表示されます。

[IOS1]: ./media/notification-hubs-aspnet-backend-ios-secure-push/secure-push-ios-1.png
