---
title: Azure Notification Hubs と iOS SDK バージョン 3.0.0 プレビュー 1 を使用して iOS アプリにプッシュ通知を送信する
description: このチュートリアルでは、Azure Notification Hubs と Apple Push Notification Service を使用して iOS デバイスにプッシュ通知を送信する方法について学習します。
author: sethmanheim
ms.author: sethm
ms.date: 06/19/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: bec4b771387854c40045f2b57afe7ead6c52f2c7
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87836039"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs-version-300-preview1"></a>チュートリアル:Azure Notification Hubs (バージョン 3.0.0-preview1) を使用して iOS アプリにプッシュ通知を送信する

このチュートリアルでは、Azure Notification Hubs SDK バージョン 2.0.4 を使用して iOS アプリケーションにプッシュ通知を送信する方法について説明します。

このチュートリアルに含まれる手順は次のとおりです。

- サンプル iOS アプリを作成します。
- iOS アプリを Azure Notification Hubs に接続します。
- テスト プッシュ通知を送信します。
- アプリが通知を受信することを確認します。

このチュートリアルの完全なコードは、[GitHub から](https://github.com/Azure/azure-notificationhubs-ios/tree/v3-preview2/Samples)ダウンロードできます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

- [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。
- iOS バージョン 10 以降を実行している iPhone または iPad。
- [Apple ポータル](https://developer.apple.com/)に登録され、自分の証明書に関連付けられている物理デバイス。

作業を進める前に、[iOS アプリ向けに Azure Notification Hubs](ios-sdk-get-started.md) の使用を開始することに関する前のチュートリアルを終え、通知ハブでプッシュ資格情報の設定と構成を行ってください。 今までに iOS 開発の経験がなくても、以下の手順に従うことができます。

> [!NOTE]
> プッシュ通知の構成要件により、プッシュ通知のデプロイとテストは、iOS エミュレーターではなく物理 iOS デバイス (iPhone または iPad) で行う必要があります。

## <a name="connect-your-ios-app-to-notification-hubs"></a>Notification Hubs に iOS アプリケーションを接続する

1. Xcode で、新しい iOS プロジェクトを作成し、 **[Single View Application]**   テンプレートを選択します。

   :::image type="content" source="media/ios-sdk/image1.png" alt-text="テンプレートを選択する":::

2. 新しいプロジェクトのオプションを設定する際には、Apple Developer ポータルでバンドル識別子を設定したときに使用したのと同じ **製品名** と **組織識別子** を使用してください。

3. プロジェクト ナビゲーターの  **[Targets] (ターゲット)** でプロジェクト名を選択してから、 **[Signing & Capabilities] (署名と機能)**   タブを選択します。 お使いの Apple Developer アカウントに適した **チーム** を選択します。 XCode を選択すると、バンドル識別子に基づいて以前に作成したプロビジョニング プロファイルが自動的に表示されます。

   Xcode で作成した新しいプロビジョニング プロファイルが表示されない場合は、署名 ID のプロファイルを更新してみてください。 メニュー バーの **[Xcode]**   をクリックし、 **[Preference] (ユーザー設定)** 、 **[アカウント]**   タブ、 **[View Details] (詳細の表示)**   ボタンの順にクリックします。署名 ID をクリックし、右下隅にある更新ボタンをクリックします。

   :::image type="content" source="media/ios-sdk/image2.png" alt-text="詳細の表示":::

4.  **[Signing & Capabilities] (署名と機能)**   タブで、 **[+ Capability] (+ 機能)** を選択します。  **[プッシュ通知]**   をダブルクリックして有効にします。

   :::image type="content" source="media/ios-sdk/image3.png" alt-text="機能":::

5. Azure Notification Hubs SDK モジュールを追加します。

    [Cocoapods](https://cocoapods.org/) を使用するか、手動でプロジェクトにバイナリを追加することで、アプリに Azure Notification Hubs SDK を統合できます。

   - Cocoapods による統合:次の依存関係をポッドファイルに追加して、アプリに Azure Notification Hubs SDK を含めます。

      ```ruby
      pod 'AzureNotificationHubs-iOS'
      ```

      - pod install を実行して新しく定義したポッドをインストールし、.xcworkspace を開きます。

         pod install の実行中に "**Unable to find a specification for AzureNotificationHubs-iOS (AzureNotificationHubs-iOS の仕様を見つけられません)** " などのエラーが表示された場合は、`pod repo update` を実行して Cocoapods リポジトリから最新のポッドを取得してから、pod install を実行します。

   - バイナリをプロジェクトにコピーすることによる統合:

      バイナリを次のようにプロジェクトにコピーすることによって、統合が可能です。

        - zip ファイルとして提供されている [Azure Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs-iOS/releases/) フレームワークをダウンロードし、解凍します。

        - Xcode でプロジェクトを右クリックし、 **[Add Files to] (ファイルの追加先)**   オプションをクリックして Xcode プロジェクトに  **WindowsAzureMessaging.framework**  フォルダーを追加します。  **[オプション]**   を選択し、 **[Copy items if needed] (必要に応じてアイテムをコピーする)**   がオンになっていることを確認してから、 **[Add] (追加)** をクリックします。

          :::image type="content" source="media/ios-sdk/image4.png" alt-text="フレームワークを追加する":::

6.  **Constants.h**という名前の新しいヘッダー ファイルをプロジェクトに追加します。 そのためには、プロジェクト名を右クリックし、 **[New File...] (新しいファイル)** を選択します。次に、 **[ヘッダー ファイル]** を選択します。 このファイルは、通知ハブの定数を保持します。  **[次へ]** を選択します。 ファイルに **Constants.h**という名前を付けます。

7. 次のコードを Constants.h ファイルに追加します。

   ```objc
   #ifndef Constants_h
   #define Constants_h
   extern NSString* const NHInfoConnectionString;
   extern NSString* const NHInfoHubName;
   extern NSString* const NHUserDefaultTags;
   #endif /* Constants_h */
   ```

8. Constants.h の実装ファイルを追加します。 そのためには、プロジェクト名を右クリックし、 **[New File...] (新しいファイル)** を選択します。 **[Objective-C ファイル]** を選択し、 **[Next] (次へ)** を選択します。 ファイルに **Constants.m**という名前を付けます。

   :::image type="content" source="media/ios-sdk/image5.png" alt-text="実装ファイルを追加する":::

9.  **Constants.m** ファイルを開き、その内容を次のコードに置き換えます。 文字列リテラルのプレースホルダー `NotificationHubConnectionString` および `NotificationHubConnectionString` を、それぞれ、以前にポータルから取得したハブ名と **DefaultListenSharedAccessSignature**に置き換えます。

   ```objc
   #import <Foundation/Foundation.h>
   #import "Constants.h"

   NSString* const NHInfoConnectionString = @"NotificationHubConnectionString";
   NSString* const NHInfoHubName = @"NotificationHubName";NSString* const NHUserDefaultTags = @"notification_tags";
   ```

10. プロジェクトの **AppDelegate.h** ファイルで、次の `import` ステートメントを追加します。

    ```objc
    #import "Constants.h"
    ```

11. 同じ **AppDelegate.m** ファイルで、 `didFinishLaunchingWithOptions` の後にあるすべてのコードを次のコードに置き換えます。

    ```objc
    // Tells the delegate that the app successfully registered with Apple Push Notification service (APNs).


    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    NSString *hubName = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoHubName];
    NSString *connectionString = [[NSBundle mainBundle] objectForInfoDictionaryKey:NHInfoConnectionString];
    [MSNotificationHub initWithConnectionString:connectionString withHubName:hubName];


    NSMutableSet *tags = [[NSMutableSet alloc] init];

    // Load and parse stored tags
    NSString *unparsedTags = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
    if (unparsedTags.length > 0) {
        NSArray *tagsArray = [unparsedTags componentsSeparatedByString: @","];

        [MSNotificationHub addTags:tagsArray];
    }

    }
    - (void)showAlert:(NSString *)message withTitle:(NSString *)title {
    if (title == nil) {
        title = @"Alert";
    }
    UIAlertController *alert = [UIAlertController alertControllerWithTitle:title message:message preferredStyle:UIAlertControllerStyleAlert];
    [alert addAction:[UIAlertAction actionWithTitle:@"OK" style:UIAlertActionStyleDefault handler:nil]];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:alert animated:YES completion:nil];
    }

    - (void)showNotification:(NSDictionary *)userInfo {
    [self logNotificationDetails:userInfo];

    NotificationDetailViewController *notificationDetail = [[NotificationDetailViewController alloc] initWithUserInfo:userInfo];
    [[[[UIApplication sharedApplication] keyWindow] rootViewController] presentViewController:notificationDetail animated:YES completion:nil];
    }

    @end
    ```

    このコードでは、 **Constants.h**で指定した接続情報を使用して通知ハブに接続します。 その後、通知ハブにデバイス トークンを付与して、ハブで通知を送信できるようにします。

### <a name="create-notificationdetailviewcontroller-header-file"></a>NotificationDetailViewController ヘッダー ファイルを作成する

1. 前の手順と同様に、 **NotificationDetailViewController.h**という名前の別のヘッダー ファイルを追加します。 新しいヘッダー ファイルの内容を次のコードに置き換えます。

   ```objc
   #import <UIKit/UIKit.h>

   NS_ASSUME_NONNULL_BEGIN

   @interface NotificationDetailViewController : UIViewController

   @property (strong, nonatomic) IBOutlet UILabel *titleLabel;
   @property (strong, nonatomic) IBOutlet UILabel *bodyLabel;
   @property (strong, nonatomic) IBOutlet UIButton *dismissButton;

   @property (strong, nonatomic) NSDictionary *userInfo;

   - (id)initWithUserInfo:(NSDictionary *)userInfo;

   @end

   NS_ASSUME_NONNULL_END
   ```

2. 実装ファイル **NotificationDetailViewController.m**を追加します。 ファイルの内容を、UIViewController メソッドを実装する次のコードに置き換えます。

   ```objc
   #import "NotificationDetailViewController.h"

   @interface NotificationDetailViewController ()

   @end

   @implementation NotificationDetailViewController

   - (id)initWithUserInfo:(NSDictionary *)userInfo {
    self = [super initWithNibName:@"NotificationDetail" bundle:nil];
    if (self) {
        _userInfo = userInfo;
    }
    return self;
   }

   - (void)viewDidLayoutSubviews {
    [self.titleLabel sizeToFit];
    [self.bodyLabel sizeToFit];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    NSString *title = nil;
    NSString *body = nil;

    NSDictionary *aps = [_userInfo valueForKey:@"aps"];
    NSObject *alertObject = [aps valueForKey:@"alert"];
    if (alertObject != nil) {
        if ([alertObject isKindOfClass:[NSDictionary class]]) {
            NSDictionary *alertDict = (NSDictionary *)alertObject;
            title = [alertDict valueForKey:@"title"];
            body = [alertObject valueForKey:@"body"];
        } else if ([alertObject isKindOfClass:[NSString class]]) {
            body = (NSString *)alertObject;
        } else {
            NSLog(@"Unable to parse notification content. Unexpected format: %@", alertObject);
        }
    }

    if (title == nil) {
        title = @"<unset>";
    }

    if (body == nil) {
        body = @"<unset>";
    }

    self.titleLabel.text = title;
    self.bodyLabel.text = body;
   }

   - (IBAction)handleDismiss:(id)sender {
    [self dismissViewControllerAnimated:YES completion:nil];
   }

   @end
   ```

### <a name="viewcontroller"></a>ViewController

1. プロジェクトの **ViewController.h** ファイルで、次の `import` ステートメントを追加します。

   ```objc
   #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
   #import <UserNotifications/UserNotifications.h>
   ```

2.  **ViewController.h**でも、 `@interface` 宣言の後に次のプロパティ宣言を追加します。

   ```objc
   @property (strong, nonatomic) IBOutlet UITextField *tagsTextField;
   ```

3. プロジェクトの **ViewController.m** 実装ファイルで、ファイルの内容を次のコードに置き換えます。

   ```objc
   #import "ViewController.h"
   #import "Constants.h"
   #import "AppDelegate.h"

   @interface ViewController ()

   @end

   @implementation ViewController

   // UIViewController methods

   - (void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event {
    // Simple method to dismiss keyboard when user taps outside of the UITextField.
    [self.view endEditing:YES];
   }

   - (void)viewDidLoad {
    [super viewDidLoad];

    // Load raw tags text from storage and initialize the text field
    self.tagsTextField.text = [[NSUserDefaults standardUserDefaults] valueForKey:NHUserDefaultTags];
   }

   - (IBAction)handleRegister:(id)sender {
    // Save raw tags text in storage
    [[NSUserDefaults standardUserDefaults] setValue:self.tagsTextField.text forKey:NHUserDefaultTags];

   // Delegate processing the register action to the app delegate.
   [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleRegister)];
   }

   - (IBAction)handleUnregister:(id)sender {
    [[[UIApplication sharedApplication] delegate] performSelector:@selector(handleUnregister)];
   }

   @end
   ```

4. エラーがないことを確認するために、デバイスでアプリをビルドして実行します。

## <a name="send-test-push-notifications"></a>テスト プッシュ通知を送信する

 [Azure portal](https://portal.azure.com/) で  **[テスト送信]**   オプションを使用すると、アプリでの通知の受信をテストできます。 これは、デバイスにテスト プッシュ通知を送信します。

:::image type="content" source="media/ios-sdk/image6.png" alt-text="テスト送信":::

プッシュ通知は通常、互換性のあるライブラリを使用して Mobile Apps などのバックエンド サービスや ASP.NET に送信されます。 ライブラリがバックエンドに使用できない場合は、REST API を直接使用して通知メッセージを送信することもできます。

通知の送信方法を確認できるチュートリアルの一覧を次に示します。

- Azure Mobile Apps:Notification Hubs に統合されている Mobile Apps バックエンドから通知を送信する方法の例については、「 [iOS アプリへのプッシュ通知の追加](/previous-versions/azure/app-service-mobile/app-service-mobile-ios-get-started-push)」を参照してください。
- ASP.NET:  [Notification Hubs を使用してユーザーにプッシュ通知を送信する方法](notification-hubs-aspnet-backend-ios-apple-apns-notification.md)。
- Azure Notification Hubs Java SDK: Java からの通知を送信するには「 [Java から Notification Hubs を使用する方法](notification-hubs-java-push-notification-tutorial.md)」を参照してください。 これは Android の開発用に Eclipse でテストされています。
- PHP:  [PHP から Notification Hubs を使用する方法](notification-hubs-php-push-notification-tutorial.md)。

## <a name="verify-that-your-app-receives-push-notifications"></a>アプリがプッシュ通知を受信することを確認する

iOS でプッシュ通知をテストするには、物理 iOS デバイスにアプリをデプロイする必要があります。 iOS シミュレーターを使用して Apple のプッシュ通知を送信することはできません。

1. アプリケーションを実行して登録が成功したことを確認したら、 **[OK]** を押します。

   :::image type="content" source="media/ios-sdk/image7.png" alt-text="[登録]":::

2. 次に、前のセクションで説明されているように、 [Azure portal](https://portal.azure.com/)からテスト プッシュ通知を送信します。

3. プッシュ通知は、特定の通知ハブから通知を受信するように登録されているすべてのデバイスに送信されます。

   :::image type="content" source="media/ios-sdk/image8.png" alt-text="送信のテスト":::

## <a name="next-steps"></a>次のステップ

この簡単な例では、登録されているすべての iOS デバイスにプッシュ通知をブロードキャストします。 特定の iOS デバイスにプッシュ通知を送信する方法を学習するには、次のチュートリアルに進んでください。

[チュートリアル:特定のデバイスにプッシュ通知を送信する](notification-hubs-ios-xplat-segmented-apns-push-notification.md)

詳細については、次の記事を参照してください。

- [Azure Notification Hubs の概要](notification-hubs-push-notification-overview.md)
- [Notification Hubs REST API](/rest/api/notificationhubs/)
- [バックエンド操作用の Notification Hubs SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)
- [GitHub 上の Notification Hubs SDK](https://github.com/Azure/azure-notificationhubs)
- [アプリケーション バックエンドへの登録](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)
- [登録管理](notification-hubs-push-notification-registration-management.md)
- [タグの使用](notification-hubs-tags-segment-push-message.md)
- [カスタム テンプレートの使用](notification-hubs-templates-cross-platform-push-messages.md)
- [Service Bus access control with shared access signatures](../service-bus-messaging/service-bus-sas.md) (共有アクセス署名による Service Bus のアクセスの制御)
- [プログラムによる SAS トークンの生成](/rest/api/eventhub/generate-sas-token)
- [Apple のセキュリティ: 一般的な暗号化](https://developer.apple.com/security/)
- [UNIX エポック時間](https://en.wikipedia.org/wiki/Unix_time)
- [HMAC](https://en.wikipedia.org/wiki/HMAC)