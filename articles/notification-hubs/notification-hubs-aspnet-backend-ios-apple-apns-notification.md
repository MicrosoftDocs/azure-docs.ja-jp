---
title: Azure Notification Hubs を使用して特定のユーザーにプッシュ通知を送信する | Microsoft Docs
description: Azure Notification Hubs を使用して特定の iOS ユーザーにプッシュ通知を送信する方法について説明します。
documentationcenter: ios
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 167c666c536ee33531fd069dbd1edb530331a9f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016951"
---
# <a name="tutorial-send-push-notifications-to-specific-users-using-azure-notification-hubs"></a>チュートリアル:Azure Notification Hubs を使用して特定のユーザーにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]

このチュートリアルでは、Azure Notification Hubs を使用して特定のデバイスで特定のアプリケーション ユーザーにプッシュ通知を送信する方法について説明します。 [アプリ バックエンドからの登録管理](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)に関するガイダンス トピックに示すように、ASP.NET WebAPI バックエンドを使用してクライアントを認証し、通知を生成します。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * Web API プロジェクトを作成する
> * WebAPI バックエンドに対してクライアントを認証する
> * WebAPI バックエンドを使用して通知に登録する
> * WebAPI バックエンドから通知を送信する
> * 新しい WebAPI バックエンドを発行する
> * iOS アプリを変更する
> * アプリケーションをテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルでは、「[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md)」の説明のとおり通知ハブを作成し、構成したと想定しています。 また、「 [安全なプッシュ (iOS)](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md) 」チュートリアルの前提条件でもあります。
バックエンド サービスとして Mobile Apps を使用する場合は、「 [iOS アプリへのプッシュ通知の追加](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)」を参照してください。

[!INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## <a name="modify-your-ios-app"></a>iOS アプリを変更する

1. 「[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md)」で作成した、単一ページの表示アプリを開きます。

   > [!NOTE]
   > このセクションでは、プロジェクトは空の組織名で構成されていると想定しています。 そうでない場合は、すべてのクラス名の先頭に組織名を追加します。

2. `Main.storyboard` ファイルで、オブジェクト ライブラリから、スクリーンショットに表示されているコンポーネントを追加します。

    ![Xcode Interface Builder でストーリーボードを編集する][1]

   * **[ユーザー名]** : "*Enter Username*" というプレースホルダー テキストが入っている UI テキスト フィールドです。結果の送信ラベルのすぐ下にあり、左右の余白と、結果の送信ラベルの下の余白による制約が適用されます。
   * **パスワード**:"*Enter Password*" というプレースホルダー テキストが入っている UI テキスト フィールドです。ユーザー名のテキスト フィールドのすぐ下にあり、左右の余白と、ユーザー名のテキスト フィールドの下の余白による制約が適用されます。 Attributes Inspector で *[Return Key]* の下にある **[Secure Text Entry]** オプションをオンにします。
   * **Log in**: パスワードのテキスト フィールドのすぐ下にあるラベル付きの UI ボタンです。Attributes Inspector の *[Control-Content]* の下にある **[Enabled]** オプションをオフにします
   * **WNS**: Windows Notification Service に通知を送信するためのスイッチとそのラベルです。ハブ上でセットアップを済ませておく必要があります。 詳しくは、[Windows で通知ハブを使用する方法に関するチュートリアル](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)をご覧ください。
   * **GCM**: Google Cloud Messaging に通知を送信するためのスイッチとそのラベルです。ハブ上でセットアップを済ませておく必要があります。 詳しくは、[Android で通知ハブを使用する方法に関するチュートリアル](notification-hubs-android-push-notification-google-gcm-get-started.md)をご覧ください。
   * **APNS**: Apple Platform Notification Service に通知を送信するためのスイッチとそのラベルです。
   * **Recipient Username**: "*Recipient username tag*" というプレースホルダー テキストが入っている UI テキスト フィールドです。GCM ラベルのすぐ下にあり、左右の余白と、GCM ラベルの下にあることの制約が適用されます。

     「[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md)」チュートリアルで、いくつかのコンポーネントが追加されました。

3. ビューに表示されているコンポーネントを **Ctrl** キーを押しながら `ViewController.h` までドラッグし、次の新しいアウトレットを追加します。

    ```objc
    @property (weak, nonatomic) IBOutlet UITextField *UsernameField;
    @property (weak, nonatomic) IBOutlet UITextField *PasswordField;
    @property (weak, nonatomic) IBOutlet UITextField *RecipientField;
    @property (weak, nonatomic) IBOutlet UITextField *NotificationField;

    // Used to enable the buttons on the UI
    @property (weak, nonatomic) IBOutlet UIButton *LogInButton;
    @property (weak, nonatomic) IBOutlet UIButton *SendNotificationButton;

    // Used to enabled sending notifications across platforms
    @property (weak, nonatomic) IBOutlet UISwitch *WNSSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *GCMSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *APNSSwitch;

    - (IBAction)LogInAction:(id)sender;
    ```

4. `ViewController.h` で、インポート ステートメントの後ろに次の `#define` を追加します。 `<Your backend endpoint>` のプレースホルダーは、前のセクションでアプリのバックエンドのデプロイに使用した宛先 URL に置き換えます。 `http://your_backend.azurewebsites.net` の例を次に示します。

    ```objc
    #define BACKEND_ENDPOINT @"<Your backend endpoint>"
    ```

5. プロジェクトで、作成済みの ASP.NET バックエンドとのインターフェイスになる、`RegisterClient` という名前の新しい Cocoa Touch クラスを作成します。 `NSObject` から継承するクラスを作成したら、 `RegisterClient.h` の中に次のコードを追加します。

    ```objc
    @interface RegisterClient : NSObject

    @property (strong, nonatomic) NSString* authenticationHeader;

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
        andCompletion:(void(^)(NSError*))completion;

    -(instancetype) initWithEndpoint:(NSString*)Endpoint;

    @end
    ```

6. `RegisterClient.m` で、`@interface` セクションを更新します。

    ```objc
    @interface RegisterClient ()

    @property (strong, nonatomic) NSURLSession* session;
    @property (strong, nonatomic) NSURLSession* endpoint;

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion;
    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion;
    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSString*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion;

    @end
    ```

7. RegisterClient.m の `@implementation` セクションを、次のコードで置き換えます。

    ```objc
    @implementation RegisterClient

    // Globals used by RegisterClient
    NSString *const RegistrationIdLocalStorageKey = @"RegistrationId";

    -(instancetype) initWithEndpoint:(NSString*)Endpoint
    {
        self = [super init];
        if (self) {
            NSURLSessionConfiguration* config = [NSURLSessionConfiguration defaultSessionConfiguration];
            _session = [NSURLSession sessionWithConfiguration:config delegate:nil delegateQueue:nil];
            _endpoint = Endpoint;
        }
        return self;
    }

    -(void) registerWithDeviceToken:(NSData*)token tags:(NSSet*)tags
                andCompletion:(void(^)(NSError*))completion
    {
        [self tryToRegisterWithDeviceToken:token tags:tags retry:YES andCompletion:completion];
    }

    -(void) tryToRegisterWithDeviceToken:(NSData*)token tags:(NSSet*)tags retry:(BOOL)retry
                andCompletion:(void(^)(NSError*))completion
    {
        NSSet* tagsSet = tags?tags:[[NSSet alloc] init];

        NSString *deviceTokenString = [[token description]
            stringByTrimmingCharactersInSet:[NSCharacterSet characterSetWithCharactersInString:@"<>"]];
        deviceTokenString = [[deviceTokenString stringByReplacingOccurrencesOfString:@" " withString:@""]
                                uppercaseString];

        [self retrieveOrRequestRegistrationIdWithDeviceToken: deviceTokenString
            completion:^(NSString* registrationId, NSError *error) {
            NSLog(@"regId: %@", registrationId);
            if (error) {
                completion(error);
                return;
            }

            [self upsertRegistrationWithRegistrationId:registrationId deviceToken:deviceTokenString
                tags:tagsSet andCompletion:^(NSURLResponse * response, NSError *error) {
                if (error) {
                    completion(error);
                    return;
                }

                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
                if (httpResponse.statusCode == 200) {
                    completion(nil);
                } else if (httpResponse.statusCode == 410 && retry) {
                    [self tryToRegisterWithDeviceToken:token tags:tags retry:NO andCompletion:completion];
                } else {
                    NSLog(@"Registration error with response status: %ld", (long)httpResponse.statusCode);

                    completion([NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }

            }];
        }];
    }

    -(void) upsertRegistrationWithRegistrationId:(NSString*)registrationId deviceToken:(NSData*)token
                tags:(NSSet*)tags andCompletion:(void(^)(NSURLResponse*, NSError*))completion
    {
        NSDictionary* deviceRegistration = @{@"Platform" : @"apns", @"Handle": token,
                                                @"Tags": [tags allObjects]};
        NSData* jsonData = [NSJSONSerialization dataWithJSONObject:deviceRegistration
                            options:NSJSONWritingPrettyPrinted error:nil];

        NSLog(@"JSON registration: %@", [[NSString alloc] initWithData:jsonData
                                            encoding:NSUTF8StringEncoding]);

        NSString* endpoint = [NSString stringWithFormat:@"%@/api/register/%@", _endpoint,
                                registrationId];
        NSURL* requestURL = [NSURL URLWithString:endpoint];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"PUT"];
        [request setHTTPBody:jsonData];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];
        [request setValue:@"application/json" forHTTPHeaderField:@"Content-Type"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            if (!error)
            {
                completion(response, error);
            }
            else
            {
                NSLog(@"Error request: %@", error);
                completion(nil, error);
            }
        }];
        [dataTask resume];
    }

    -(void) retrieveOrRequestRegistrationIdWithDeviceToken:(NSString*)token
                completion:(void(^)(NSString*, NSError*))completion
    {
        NSString* registrationId = [[NSUserDefaults standardUserDefaults]
                                    objectForKey:RegistrationIdLocalStorageKey];

        if (registrationId)
        {
            completion(registrationId, nil);
            return;
        }

        // request new one & save
        NSURL* requestURL = [NSURL URLWithString:[NSString stringWithFormat:@"%@/api/register?handle=%@",
                                _endpoint, token]];
        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
                                                self.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        NSURLSessionDataTask* dataTask = [self.session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (!error && httpResponse.statusCode == 200)
            {
                NSString* registrationId = [[NSString alloc] initWithData:data
                    encoding:NSUTF8StringEncoding];

                // remove quotes
                registrationId = [registrationId substringWithRange:NSMakeRange(1,
                                    [registrationId length]-2)];

                [[NSUserDefaults standardUserDefaults] setObject:registrationId
                    forKey:RegistrationIdLocalStorageKey];
                [[NSUserDefaults standardUserDefaults] synchronize];

                completion(registrationId, nil);
            }
            else
            {
                NSLog(@"Error status: %ld, request: %@", (long)httpResponse.statusCode, error);
                if (error)
                    completion(nil, error);
                else {
                    completion(nil, [NSError errorWithDomain:@"Registration" code:httpResponse.statusCode
                                userInfo:nil]);
                }
            }
        }];
        [dataTask resume];
    }

    @end
    ```

    このコードは、アプリケーション バックエンドへの REST 呼び出しを実行するための NSURLSession、および通信ハブによって返される registrationId をローカルに格納するための NSUserDefaults の使用についてのガイダンス記事「[アプリ バックエンドからの登録](notification-hubs-push-notification-registration-management.md#registration-management-from-a-backend)」で説明したロジックを実装します。

    このクラスが適切に機能するためには、プロパティ `authorizationHeader` を設定する必要があります。 このプロパティは、ログイン後に `ViewController` クラスによって設定されます。

8. `ViewController.h` で、`RegisterClient.h` のための `#import` ステートメントを追加します。 デバイス トークンの宣言と、`@interface` セクションの`RegisterClient` インスタンスに対する参照を追加します。

    ```objc
    #import "RegisterClient.h"

    @property (strong, nonatomic) NSData* deviceToken;
    @property (strong, nonatomic) RegisterClient* registerClient;
    ```

9. ViewController.m で、以下のように `@interface` セクションのプライベート メソッドの宣言を追加します。

    ```objc
    @interface ViewController () <UITextFieldDelegate, NSURLConnectionDataDelegate, NSXMLParserDelegate>

    // create the Authorization header to perform Basic authentication with your app back-end
    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;

    @end
    ```

    > [!NOTE]
    > 次のスニペットは安全な認証スキームではないため、`createAndSetAuthenticationHeaderWithUsername:AndPassword:` の実装を、OAuth や Active Directory などの登録クライアント クラスによって使用される認証トークンを生成する特定の認証メカニズムで置き換える必要があります。

10. 次に、`ViewController.m` の `@implementation` セクションに以下のコードを追加します。これは、デバイス トークンと認証ヘッダーの設定の実装を追加するものです。

    ```objc
    -(void) setDeviceToken: (NSData*) deviceToken
    {
        _deviceToken = deviceToken;
        self.LogInButton.enabled = YES;
    }

    -(void) createAndSetAuthenticationHeaderWithUsername:(NSString*)username
                    AndPassword:(NSString*)password;
    {
        NSString* headerValue = [NSString stringWithFormat:@"%@:%@", username, password];

        NSData* encodedData = [[headerValue dataUsingEncoding:NSUTF8StringEncoding] base64EncodedDataWithOptions:NSDataBase64EncodingEndLineWithCarriageReturn];

        self.registerClient.authenticationHeader = [[NSString alloc] initWithData:encodedData
                                                    encoding:NSUTF8StringEncoding];
    }

    -(BOOL)textFieldShouldReturn:(UITextField *)textField
    {
        [textField resignFirstResponder];
        return YES;
    }
    ```

    デバイス トークンの設定によって、どのように **[Log in]** ボタンが有効になるかに注目してください。 これは、ログイン アクションの一部として、View Controller がアプリケーション バックエンドでプッシュ通知を登録するためです。 デバイス トークンが適切に設定されるまで、**ログイン** アクションにアクセスできないようにします。 プッシュ登録の前にログインが発生する場合には、プッシュ登録からログインを切り離す必要があります。

11. ViewController.m で以下のスニペットを使用して、 **[Log in]** ボタンのアクション メソッドを実装し、ASP.NET バックエンドを使って通知メッセージを送信するメソッドを実装します。

    ```objc
    - (IBAction)LogInAction:(id)sender {
        // create authentication header and set it in register client
        NSString* username = self.UsernameField.text;
        NSString* password = self.PasswordField.text;

        [self createAndSetAuthenticationHeaderWithUsername:username AndPassword:password];

        __weak ViewController* selfie = self;
        [self.registerClient registerWithDeviceToken:self.deviceToken tags:nil
            andCompletion:^(NSError* error) {
            if (!error) {
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    selfie.SendNotificationButton.enabled = YES;
                    [self MessageBox:@"Success" message:@"Registered successfully!"];
                });
            }
        }];
    }

    - (void)SendNotificationASPNETBackend:(NSString*)pns UsernameTag:(NSString*)usernameTag
                Message:(NSString*)message
    {
        NSURLSession* session = [NSURLSession
            sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:nil
            delegateQueue:nil];

        // Pass the pns and username tag as parameters with the REST URL to the ASP.NET backend
        NSURL* requestURL = [NSURL URLWithString:[NSString
            stringWithFormat:@"%@/api/notifications?pns=%@&to_tag=%@", BACKEND_ENDPOINT, pns,
            usernameTag]];

        NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:requestURL];
        [request setHTTPMethod:@"POST"];

        // Get the mock authenticationheader from the register client
        NSString* authorizationHeaderValue = [NSString stringWithFormat:@"Basic %@",
            self.registerClient.authenticationHeader];
        [request setValue:authorizationHeaderValue forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
        [request setHTTPBody:[message dataUsingEncoding:NSUTF8StringEncoding]];

        // Execute the send notification REST API on the ASP.NET Backend
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
            completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
        {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
            if (error || httpResponse.statusCode != 200)
            {
                NSString* status = [NSString stringWithFormat:@"Error Status for %@: %d\nError: %@\n",
                                    pns, httpResponse.statusCode, error];
                dispatch_async(dispatch_get_main_queue(),
                ^{
                    // Append text because all 3 PNS calls may also have information to view
                    [self.sendResults setText:[self.sendResults.text stringByAppendingString:status]];
                });
                NSLog(status);
            }

            if (data != NULL)
            {
                xmlParser = [[NSXMLParser alloc] initWithData:data];
                [xmlParser setDelegate:self];
                [xmlParser parse];
            }
        }];
        [dataTask resume];
    }
    ```

12. **[Send Notification]** ボタンのアクションを更新し、ASP.NET バックエンドを使用すると共に、スイッチで有効になっている PNS があればそれに通知を送信するようにします。

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        //[self SendNotificationRESTAPI];
        [self SendToEnabledPlatforms];
    }

    -(void)SendToEnabledPlatforms
    {
        NSString* json = [NSString stringWithFormat:@"\"%@\"",self.notificationMessage.text];

        [self.sendResults setText:@""];

        if ([self.WNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"wns" UsernameTag:self.RecipientField.text Message:json];

        if ([self.GCMSwitch isOn])
            [self SendNotificationASPNETBackend:@"gcm" UsernameTag:self.RecipientField.text Message:json];

        if ([self.APNSSwitch isOn])
            [self SendNotificationASPNETBackend:@"apns" UsernameTag:self.RecipientField.text Message:json];
    }
    ```

13. `ViewDidLoad` 関数で、以下を追加して `RegisterClient` インスタンスをインスタンス化し、テキスト フィールドに対するデリゲートを設定します。

    ```objc
    self.UsernameField.delegate = self;
    self.PasswordField.delegate = self;
    self.RecipientField.delegate = self;
    self.registerClient = [[RegisterClient alloc] initWithEndpoint:BACKEND_ENDPOINT];
    ```

14. ここで `AppDelegate.m` 内で、`application:didRegisterForPushNotificationWithDeviceToken:` メソッドの内容をすべて削除し、以下に置き換えます (ビュー コントローラーに APNs から取得した最新のデバイス トークンが含まれることを確認するため)。

    ```objc
    // Add import to the top of the file
    #import "ViewController.h"

    - (void)application:(UIApplication *)application
                didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken
    {
        ViewController* rvc = (ViewController*) self.window.rootViewController;
        rvc.deviceToken = deviceToken;
    }
    ```

15. 最後に `AppDelegate.m` で、次のメソッドが含まれていることを確認します。

    ```objc
    - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
        NSLog(@"%@", userInfo);
        [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
    }
    ```

## <a name="test-the-application"></a>アプリケーションをテストする

1. XCode を使用して、物理 iOS デバイスでアプリケーションを実行します (プッシュ通知はシミュレーターでは機能しません)。
2. iOS アプリケーションの UI で、ユーザー名とパスワードの両方に同じ値を入力します。 次に、 **[Log in]** をクリックします。

    ![iOS テスト アプリケーション][2]

3. 登録の成功を通知するポップアップが表示されます。 **[OK]** をクリックします。

    ![iOS テスト通知の表示][3]

4. **Recipient username tag* というテキストが表示されているフィールドに、別のデバイスから登録するときに使用したユーザー名のタグを入力します。
5. 通知メッセージを入力して **Send Notification** をクリックします。 入力したタグが登録されているデバイスのみ、通知メッセージを受信します。 通知は、該当するユーザーにのみ送信されます。

    ![iOS テストのタグ付けされた通知][4]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、タグが登録に関連付けられている特定のユーザーにプッシュ通知を送信する方法を学習しました。 場所に基づいたプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[場所に基づいたプッシュ通知を送信する](notification-hubs-push-bing-spatial-data-geofencing-notification.md)

[1]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-interface.png
[2]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-user-pwd.png
[3]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-registered.png
[4]: ./media/notification-hubs-aspnet-backend-ios-notify-users/notification-hubs-ios-notify-users-enter-msg.png
