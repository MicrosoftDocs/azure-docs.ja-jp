---
title: Azure Notification Hubs を使用して特定の iOS デバイスにプッシュ通知を送信する | Microsoft Docs
description: このチュートリアルでは、Azure Notification Hubs を使用して特定の iOS デバイスにプッシュ通知を送信する方法について学習します。
services: notification-hubs
documentationcenter: ios
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 11/07/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 2cb979491e247a4d44b9ae9ae27c433fb3f436d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579217"
---
# <a name="tutorial-send-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>チュートリアル: Azure Notification Hubs を使用して特定の iOS デバイスにプッシュ通知を送信する

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概要

このチュートリアルでは、Azure Notification Hubs を使用して iOS アプリにニュース速報通知をブロードキャストする方法を示します。 完了すると、関心のあるニュース速報カテゴリに登録し、それらのカテゴリのプッシュ通知のみを受信できます。 このシナリオは、以前に関心があることを宣言しているユーザーのグループに通知を送信する必要のある多くのアプリ (RSS リーダーや、音楽ファン向けのアプリなど) にとって一般的なパターンです。

ブロードキャスト シナリオは、通知ハブでの登録の作成時に 1 つ以上の "*タグ*" を追加することで有効にします。 通知がタグに送信されると、そのタグに登録しているデバイスが通知を受信します。 タグは文字列にすぎないため、事前にプロビジョニングする必要はありません。 タグの詳細については、「[Notification Hubs のルーティングとタグ式](notification-hubs-tags-segment-push-message.md)」を参照してください。

このチュートリアルでは、次の手順を実行します。

> [!div class="checklist"]
> * アプリにカテゴリ選択を追加する
> * タグ付けされた通知を送信する
> * デバイスから通知を送信する
> * アプリケーションを実行して通知を生成する

## <a name="prerequisites"></a>前提条件

このトピックは、「[チュートリアル: Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する][get-started]」で作成したアプリに基づいて構築されています。 このチュートリアルを開始する前に、「[チュートリアル: Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する][get-started]」を既に完了している必要があります。

## <a name="add-category-selection-to-the-app"></a>アプリケーションにカテゴリ選択を追加する

最初の手順として、既存のストーリーボードに UI 要素を追加して、ユーザーが登録するカテゴリを選択できるようにします。 ユーザーにより選択されるカテゴリは、デバイスに格納されます。 アプリが起動すると、通知ハブにデバイス登録が作成され、選択されたカテゴリがタグとして追加されます。

1. **MainStoryboard_iPhone.storyboard** で、オブジェクト ライブラリから次のコンポーネントを追加します。

   * "Breaking News" というテキストが付いたラベル
   * "World"、"Politics"、"Business"、"Technology"、"Science"、"Sports" というカテゴリ テキストが付いたラベル
   * 6 つのスイッチ (カテゴリごとに 1 つ)。各スイッチの **[状態]** は、既定で **[Off]** に設定されます。
   * [Subscribe] というラベルが付いた 1 個のボタン

     ストーリーボードは次のようになります。

     ![Xcode インターフェイス ビルダー][3]

2. アシスタント エディターで、すべてのスイッチのアウトレットを作成し、"WorldSwitch"、"PoliticsSwitch"、"BusinessSwitch"、"TechnologySwitch"、"ScienceSwitch"、"SportsSwitch" という名前にします。

3. `subscribe` という名前のボタンのアクションを作成します。`ViewController.h` には次のコードを含める必要があります。

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. 新しい `Notifications` という名前の **Cocoa Touch クラス** を作成します。 Notifications.h の interface セクションに次のコードをコピーします。

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. 次の import ディレクティブを Notifications.m に追加します。

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Notifications.m の implementation セクションに次のコードをコピーします。

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    このクラスは、このデバイスが受信するニュースのカテゴリを格納したり、取得したりするためにローカル ストレージを使用します。 ローカル ストレージには、 [テンプレート](notification-hubs-templates-cross-platform-push-messages.md) 登録を使用してこれらのカテゴリを登録するメソッドも格納されます。

7. `AppDelegate.h` ファイルで、`Notifications.h` のインポート ステートメントを追加し、`Notifications` クラスのインスタンスのプロパティを追加します。

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. `AppDelegate.m` の `didFinishLaunchingWithOptions` メソッドで、通知インスタンスを初期化するコードをメソッドの先頭に追加します。  
    `HUBNAME` と `HUBLISTENACCESS` (`hubinfo.h` に定義されます) は、通知ハブの名前と取得済みの *DefaultListenSharedAccessSignature* の接続文字列によって置き換えが行われる `<hub name>` プレースホルダーと `<connection string with listen access>` プレースホルダーによって既に設定されています。

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > クライアント アプリケーションを使用して配布される資格情報は一般にセキュリティで保護されないため、クライアント アプリケーションではリッスン アクセス用のキーだけを配布してください。 リッスン アクセスにより、アプリケーションが通知を登録できるようになりますが、既存の登録を変更することはできないため、通知を送信できません。 通知を送信して既存の登録を変更するセキュリティで保護されたバックエンド サービスでは、フル アクセス キーが使用されます。

9. `AppDelegate.m` の `didRegisterForRemoteNotificationsWithDeviceToken` メソッドで、メソッドのコードを次のコードに置き換えて、デバイス トークンを `notifications` クラスに渡します。 `notifications` クラスでは、カテゴリを使用した通知への登録を実行します。 ユーザーがカテゴリ選択を変更した場合は、それらを更新するために、**subscribe** ボタンに応答して `subscribeWithCategories` メソッドを呼び出します。

    > [!NOTE]
    > Apple Push Notification Service (APNS) によって割り当てられたデバイス トークンはいつでも変更される可能性があるので、通知エラーを回避するために通知を頻繁に登録してください。 この例では、アプリケーションが起動するたびに通知を登録します。 頻繁に実行されるアプリケーションの場合 (1 日に複数回など)、帯域幅を節約するため、前回の登録から 1 日経過していない場合は登録をスキップできます。

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    この時点では、`didRegisterForRemoteNotificationsWithDeviceToken` メソッドに他のコードは存在しません。

10. [Notification Hubs の使用の開始][get-started]に関するチュートリアルを完了していれば、次のメソッドが既に `AppDelegate.m` に含まれています。 ない場合は、それらを追加します。

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    このメソッドにより、簡単な **UIAlert** を表示することでアプリケーションの実行中に受信した通知が処理されます。

11. `ViewController.m` で、`AppDelegate.h` の `import` ステートメントを追加し、XCode で生成された `subscribe` メソッドに次のコードをコピーします。 このコードは、ユーザーがユーザー インターフェイスで選択した新しいカテゴリ タグを使用するように通知の登録を更新します。

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    このメソッドでは、カテゴリの `NSMutableArray` を作成し、`Notifications` クラスを使用してそのリストをローカル ストレージに格納し、対応するタグを通知ハブに登録します。 カテゴリが変更されると、新しいカテゴリで登録が再作成されます。

12. `ViewController.m` に、保存済みのカテゴリに基づいてユーザー インターフェイスを設定する次のコードを `viewDidLoad` メソッドに追加します。

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

これで、アプリは、毎回の起動時に、通知ハブに登録するために使用されるカテゴリ セットをデバイスのローカル ストレージに格納できるようになりました。 ユーザーは、実行時にカテゴリの選択を変更し、`subscribe` メソッドをクリックして、デバイスの登録を更新できます。 次に、アプリ自体で直接ニュース速報通知を送信するようにアプリを更新します。

## <a name="optional-send-tagged-notifications"></a>(省略可能) タグ付けされた通知を送信する

Visual Studio にアクセスできない場合は、次のセクションをスキップし、アプリ自体から通知を送信できます。 [Azure Portal] で通知ハブの [デバッグ] タブを使用して、適切なテンプレート通知を送信することもできます。

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(省略可能) デバイスから通知を送信する

通常、通知はバックエンド サービスによって送信されますが、アプリからニュース速報通知を直接送信できます。 それを行うには、「[Notification Hubs の使用][get-started]」のチュートリアルで定義した `SendNotificationRESTAPI` メソッドを更新します。

1. `ViewController.m` で、次のように `SendNotificationRESTAPI` メソッドを更新して、そのメソッドがカテゴリ タグのパラメーターを受け付け、正しい[テンプレート](notification-hubs-templates-cross-platform-push-messages.md)通知を送信するようにします。

    ```objc
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
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

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
    ```

2. `ViewController.m` で、次のコードに示すように `Send Notification` アクションを更新します。 それにより、各タグを個別に使用して通知を送信したり、複数のプラットフォームに送信したりします。

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. プロジェクトをリビルドし、ビルド エラーがないことを確認します。

## <a name="run-the-app-and-generate-notifications"></a>アプリケーションを実行して通知を生成する

1. [実行] ボタンを押してプロジェクトをビルドし、アプリケーションを開始します。 購読するニュース速報のオプションを選択して、 **[購読]** ボタンをクリックします。 通知が購読されたことを示すダイアログが表示されます。

    ![iOS での通知例][1]

    **[購読]** を選択すると、アプリケーションにより、選択されたカテゴリがタグに変換され、選択されたタグの新しいデバイス登録が通知ハブから要求されます。

2. ニュース速報として送信するメッセージを入力し、 **[通知の送信]** ボタンを押します。 または、通知を生成する .NET コンソール アプリケーションを実行します。

    ![iOS の通知設定を変更する][2]

3. ニュース速報にサブスクライブしている各デバイスが、今送信したばかりのニュース速報通知を受信します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、カテゴリに登録している特定の iOS デバイスにブロードキャスト通知を送信しました。 ローカライズしたプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
>[ローカライズしたプッシュ通知を送信する](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: /previous-versions/azure/azure-services/dn530749(v=azure.100)
[Notification Hubs How-To for iOS]: /previous-versions/azure/azure-services/jj927170(v=azure.100)
[get-started]: ios-sdk-get-started.md
[Azure Portal]: https://portal.azure.com
