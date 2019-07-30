---
title: Azure Notification Hubs を使用する Swift iOS アプリにプッシュ通知を送信する | Microsoft Docs
description: Azure Notification Hubs を使用する Swift iOS アプリにプッシュ通知を送信する方法を学習します。
services: notification-hubs
documentationcenter: ios
author: mikeparker104
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 05/21/2019
ms.author: miparker
ms.openlocfilehash: c35044918876b2c7710e26f6b868bc1096c2f538
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340403"
---
# <a name="tutorial-push-notifications-to-swift-ios-apps-that-use-the-notification-hubs-rest-api"></a>チュートリアル:Notification Hubs REST API を使用する Swift iOS アプリにプッシュ通知を送信する

> [!div class="op_single_selector"]
> * [Objective-C](notification-hubs-ios-apple-push-notification-apns-get-started.md)
> * [Swift](notification-hubs-ios-push-notifications-swift-apps-get-started.md)

このチュートリアルでは、[REST API](/rest/api/notificationhubs/) を使用して、Azure Notification Hubs を通じて Swift ベースの iOS アプリケーションにプッシュ通知を送信します。 また、[Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) を使用してプッシュ通知を受信する空の iOS アプリを作成します。

このチュートリアルでは、次の手順について説明します。

> [!div class="checklist"]
> * 証明書の署名要求ファイルを生成する。
> * アプリをプッシュ通知用に要求する。
> * アプリケーションのプロビジョニング プロファイルを作成する。
> * 通知ハブを作成します。
> * APNs 情報を利用して通知ハブを構成する。
> * iOS アプリを通知ハブに接続する。
> * ソリューションをテストする。

## <a name="prerequisites"></a>前提条件

理解するには、次の内容が必要です。

- [Azure Notification Hubs の概要](notification-hubs-push-notification-overview.md)を確認する (このサービスに慣れていない場合)。
- [登録とインストール](notification-hubs-push-notification-registration-management.md)について学習する。
- アクティブな [Apple Developer アカウント](https://developer.apple.com)。
- Xcode を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。
- 実行およびデバッグに使用できる物理的な iPhone デバイス (シミュレーターを使用してプッシュ通知をテストすることはできないため)。
- [Apple ポータル](https://developer.apple.com)に登録され、自分の証明書に関連付けられている物理的な iPhone デバイス。
- リソースを作成および管理できる [Azure サブスクリプション](https://portal.azure.com)。

この基本原則の 1 つ目の例は、今までに iOS 開発の経験がなくても、手順に従って作成することができます。 ただし、次の概念に関する知識があると有益です。

- Xcode と Swift を使用した iOS アプリの構築。
- iOS 用の [Azure 通知ハブ](notification-hubs-ios-apple-push-notification-apns-get-started.md)の構成。
- [Apple Developer ポータル](https://developer.apple.com)と [Azure portal](https://portal.azure.com)。

> [!NOTE]
> 通知ハブは、"**サンドボックス**" 認証モードのみを使用するように構成されます。 運用環境のワークロードにはこの認証モードを使用しないでください。

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="connect-your-ios-app-to-a-notification-hub"></a>iOS アプリを通知ハブに接続する

このセクションでは、通知ハブに接続する iOS アプリを構築します。  

### <a name="create-an-ios-project"></a>iOS プロジェクトを作成する

1. Xcode で、新しい iOS プロジェクトを作成し、 **[Single View Application]** テンプレートを選択します。

1. 新しいプロジェクトのオプションを設定するときは:

   1. Apple Developer ポータルで **[Bundle Identifier]\(バンドル識別子\)** を設定するときに使用した **[製品名]** (PushDemo) と **[Organization Identifier]\(組織 ID\)** (`com.<organization>`) を指定します。

   1. **アプリ ID** が設定された**チーム**を選択します。

   1. **言語**を **Swift** に設定します。

   1. **[次へ]** を選択します。

1. **SupportingFiles** という名前の新しいフォルダーを作成します。

1. **SupportingFiles** フォルダーの下に **devsettings.plist** という名前の新しい p-list ファイルを作成します。 git リポジトリを操作するときにコミットされないように、このフォルダーを必ず **gitignore** ファイルに追加してください。 運用アプリでは、自動ビルド プロセスの一部としてこれらのシークレットを条件付きで設定するケースが考えられます。 このチュートリアルでは、そのような設定については説明しません。

1. プロビジョニングした通知ハブからの独自の値を使用して、次の構成エントリが含まれるように **devsettings.plist** を更新します。

   | キー                            | Type                     | 値                     |
   |--------------------------------| -------------------------| --------------------------|
   | notificationHubKey             | string                   | \<hubKey>                  |
   | notificationHubKeyName         | string                   | \<hubKeyName>              |
   | notificationHubName            | string                   | \<hubName>                 |
   | notificationHubNamespace       | string                   | \<hubNamespace>            |

   Azure portal の通知ハブ リソースに移動して、必要な値を見つけることができます。 特に、**notificationHubName** および **notificationHubNamespace** の値は、 **[概要]** ページ内の **[要点]** サマリーの右上隅にあります。

   ![Notification Hubs の [要点] サマリー](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials.png)

   **notificationHubKeyName** と **notificationHubKey** の値は、 **[アクセス ポリシー]** に移動し、それぞれの**アクセス ポリシー** (`DefaultFullSharedAccessSignature` など) を選択して見つけることもできます。 その後、 **[プライマリ接続文字列]** から、プレフィックスが `SharedAccessKeyName=` の値 (`notificationHubKeyName` 用) とプレフィックスが `SharedAccessKey=` の値 (`notificationHubKey` 用) をコピーします。

   接続文字列は、次の形式にする必要があります。

   ```xml
   Endpoint=sb://<namespace>.servicebus.windows.net/;SharedAccessKeyName=<notificationHubKeyName>;SharedAccessKey=<notificationHubKey>
   ```

   わかりやすいように、`DefaultFullSharedAccessSignature` を指定して、トークンを使って通知を送信できるようにします。 実際に通知を受け取るだけの場合は、`DefaultListenSharedAccessSignature` を選んだ方が適切です。

1. **プロジェクト ナビゲーター**で、**プロジェクト名**を選択し、次に **[General]\(全般\)** タブを選択します。

1. **[Identity]\(ID\)** を見つけ、 **[Bundle Identifier]\(バンドル識別子\)** の値を、前の手順の **[アプリ ID]** に対して使用される値である `com.<organization>.PushDemo` と一致するように設定します。

1. **[Signing]\(署名\)** を見つけ、お使いの **[Apple Developer Account]\(Apple Developer アカウント\)** に適した **[チーム]** を選択します。 **[チーム]** の値は、自分の証明書とプロファイルを作成したときのものに一致するようにします。

1. Xcode により、**バンドル識別子**に基づいて適切な**プロビジョニング プロファイル**の値が自動的にプルダウンします。 新しい**プロビジョニング プロファイル**の値が表示されない場合は、 **[Xcode]**  >  **[Preferences]\(環境設定\)**  >  **[アカウント]**  >  **[View Details]\(詳細を表示\)** を選択して、 **[Signing Identity]\(署名 ID\)** のプロファイルを更新してみてください。 **[Signing Identity]\(署名 ID\)** を選択し、右下にある **[Refresh]\(更新\)** ボタンをクリックしてプロファイルをダウンロードします。

1. **[Capabilities]\(機能\)** タブを選択し、 **[push notifications]\(プッシュ通知\)** が有効になっていることを確認します。

1. **AppDelegate.swift** ファイルを開いて **UNUserNotificationCenterDelegate** プロトコルを実装し、次のコードをクラスの先頭に追加します。

    ```swift
    @UIApplicationMain
    class AppDelegate: UIResponder, UIApplicationDelegate, UNUserNotificationCenterDelegate {

        ...

        var configValues: NSDictionary?
        var notificationHubNamespace : String?
        var notificationHubName : String?
        var notificationHubKeyName : String?
        var notificationHubKey : String?
        let tags = ["12345"]
        let genericTemplate = PushTemplate(withBody: "{\"aps\":{\"alert\":\"$(message)\"}}")

        ...
    }
    ```

    後でこれらのメンバーを使用します。 具体的には、後で登録の一部として **tags** および **genericTemplate** メンバーを使用します。 タグの詳細については、[登録用のタグ](notification-hubs-tags-segment-push-message.md)に関するページおよび[テンプレートの登録](notification-hubs-templates-cross-platform-push-messages.md)に関するページを参照してください。

1. 同じファイルで、**didFinishLaunchingWithOptions** 関数に次のコードを追加します。

    ```swift
    if let path = Bundle.main.path(forResource: "devsettings", ofType: "plist") {
        if let configValues = NSDictionary(contentsOfFile: path) {
            self.notificationHubNamespace = configValues["notificationHubNamespace"] as? String
            self.notificationHubName = configValues["notificationHubName"] as? String
            self.notificationHubKeyName = configValues["notificationHubKeyName"] as? String
            self.notificationHubKey = configValues["notificationHubKey"] as? String
        }
    }

    if #available(iOS 10.0, *){
        UNUserNotificationCenter.current().delegate = self
        UNUserNotificationCenter.current().requestAuthorization(options: [.alert, .sound, .badge]) {
            (granted, error) in

            if (granted)
            {
                DispatchQueue.main.async {
                    application.registerForRemoteNotifications()
                }
            }
        }
    }

    return true
    ```

    このコードでは、**devsettings.plist** から設定値を取得し、**AppDelegate** クラスを **UNUserNotificationCenter** デリゲートとして設定します。次に、プッシュ通知の承認を要求した後、**registerForRemoteNotifications** を呼び出します。

    わかりやすいように、このコードでは *iOS 10 以降のみ*をサポートします。 通常行うように条件付きでそれぞれの API とアプローチを使用することで、以前の OS バージョンのサポートを追加できます。

1. 同じファイルで、次の関数を追加します。

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})
    }

    func showAlert(withText text : String) {
        let alertController = UIAlertController(title: "PushDemo", message: text, preferredStyle: UIAlertControllerStyle.alert)
        alertController.addAction(UIAlertAction(title: "OK", style: UIAlertActionStyle.default,handler: nil))
        self.window?.rootViewController?.present(alertController, animated: true, completion: nil)
    }
    ```

    このコードでは、**installationId** と **pushChannel** の値を使用して通知ハブに登録します。 この場合は、**UIDevice.current.identifierForVendor** を使用して、デバイスを識別するための一意の値を入力した後、**deviceToken** を書式設定して必要な **pushChannel** 値を入力します。 **showAlert** 関数は、単にデモンストレーションのためのいくつかのメッセージ テキストを表示するために存在します。

1. 引き続き **AppDelegate.swift** ファイル内で、**willPresent** 関数と **didReceive** 関数を **UNUserNotificationCenterDelegate** に追加します。 これらの関数は、それぞれフォアグラウンドまたはバックグラウンドのどちらかでアプリが実行されていることが通知されるとアラートを表示します。

    ```swift
    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        willPresent notification: UNNotification, 
        withCompletionHandler completionHandler: @escaping (UNNotificationPresentationOptions) -> Void) {
        showAlert(withText: notification.request.content.body)
    }

    @available(iOS 10.0, *)
    func userNotificationCenter(_ center: UNUserNotificationCenter, 
        didReceive response: UNNotificationResponse, 
        withCompletionHandler completionHandler: @escaping () -> Void) {
        showAlert(withText: response.notification.request.content.body)
    }
    ```

1. **didRegisterForRemoteNotificationsWithDeviceToken** 関数の末尾に print ステートメントを追加して、**installationId** および **pushChannel** に値が割り当てられることを確認します。

1. 後でプロジェクトに追加する基本コンポーネント用の **Models**、**Services**、および **Utilities** フォルダーを作成します。

1. プロジェクトが物理デバイス上でビルドおよび実行されることを確認します。 プッシュ通知はシミュレーターを使用してテストすることはできません。

### <a name="create-models"></a>モデルを作成する

この手順では、[Notification Hubs REST API](/rest/api/notificationhubs/) ペイロードを表し、必要な共有アクセス署名 (SAS) トークン データを格納するためのモデルのセットを作成します。

1. **PushTemplate.swift** という名前の新しい Swift ファイルを **Models** フォルダーに追加します。 このモデルは、**DeviceInstallation** ペイロードの一部として、個々のテンプレートの**本文**を表す構造体を提供します。

    ```swift
    import Foundation

    struct PushTemplate : Codable {
        let body : String

        init(withBody body : String) {
            self.body = body
        }
    }
    ```

1. **DeviceInstallation.swift** という名前の新しい Swift ファイルを **Models** フォルダーに追加します。 このファイルでは、**デバイス インストール**を作成または更新するためのペイロードを表す構造体を定義します。 次のコードをファイルに追加します。

    ```swift
    import Foundation

    struct DeviceInstallation : Codable {
        let installationId : String
        let pushChannel : String
        let platform : String = "apns"
        var tags : [String]
        var templates : Dictionary<String, PushTemplate>

        init(withInstallationId installationId : String, andPushChannel pushChannel : String) {
            self.installationId = installationId
            self.pushChannel = pushChannel
            self.tags = [String]()
            self.templates = Dictionary<String, PushTemplate>()
        }
    }
    ```

1. **TokenData.swift** という名前の新しい Swift ファイルを **Models** フォルダーに追加します。 このモデルは、SAS トークンをその有効期限と共に格納するために使用されます。

    ```swift
    import Foundation

    struct TokenData {

        let token : String
        let expiration : Int

        init(withToken token : String, andTokenExpiration expiration : Int) {
            self.token = token
            self.expiration = expiration
        }
    }
    ```

### <a name="generate-a-sas-token"></a>SAS トークンを生成する

Notification Hubs では、Azure Service Bus と同じセキュリティ インフラストラクチャが使用されます。 REST API を呼び出すには、要求の **Authorization** ヘッダーで使用できる [SAS トークンをプログラムで生成する](/rest/api/eventhub/generate-sas-token)必要があります。  

結果のトークンは次の形式になります。

```xml
SharedAccessSignature sig=<UrlEncodedSignature>&se=<ExpiryEpoch>&skn=<KeyName>&sr=<UrlEncodedResourceUri>
```

このプロセス自体には、同じ 6 つの主要な手順が含まれます。  

1. 有効期限を [UNIX エポック時間](https://en.wikipedia.org/wiki/Unix_time)形式、つまり、1970 年 1 月 1 日の午前 0 時 (協定世界時) から経過した秒数で計算します。
1. アクセスしようとしているリソースを表す **ResourceUrl** を書式設定して、パーセントでエンコードされた小文字表記にします。 **ResourceUrl** の形式は `'https://<namespace>.servicebus.windows.net/<hubName>'` です。
1. **StringToSign** を準備します。これは `'<UrlEncodedResourceUrl>\n<ExpiryEpoch>'` のように書式設定されます。
1. **StringToSign** 値の HMAC-SHA256 ハッシュを使用して**署名**を計算し、Base64 でエンコードします。 ハッシュ値は、それぞれの**承認規則**の**接続文字列**の**キー**部分と共に使用されます。
1. Base64 でエンコードされた**署名**を書式設定して、パーセントでエンコードされた表記にします。
1. **UrlEncodedSignature**、**ExpiryEpoch**、**KeyName**、および **UrlEncodedResourceUrl** の値を使用して、必要な形式でトークンを構築します。

共有アクセス署名の詳しい概要のほか、Azure Service Bus および Notification Hubs でのその使用方法については、[Azure Service Bus のドキュメント](../service-bus-messaging/service-bus-sas.md)を参照してください。

この Swift の例では、その目的上、署名のハッシュをサポートするために Apple のオープンースの **CommonCrypto** ライブラリを使用します。 これは C ライブラリであるため、そのままでは Swift で使用できません。 ブリッジ ヘッダーを使用すると、ライブラリを使用できるようになります。

ブリッジ ヘッダーを追加して構成するには:

1. Xcode で、 **[ファイル]**  >  **[New]\(新規\)**  >  **[ファイル]**  >  **[Header File]\(ヘッダー ファイル\)** を選択します。 ヘッダー ファイルの名前を **BridgingHeader.h** にします。

1. **CommonHMAC.h** をインポートするようにファイルを編集します。

    ```swift
    #import <CommonCrypto/CommonHMAC.h>

    #ifndef BridgingHeader_h
    #define BridgingHeader_h


    #endif /* BridgingHeader_h */
    ```

1. ブリッジ ヘッダーを参照するようにターゲットの**ビルド設定**を更新します。

   1.  **[Building Settings]\(ビルド設定\)**  タブを開き、 **[Swift Compiler]\(Swift コンパイラ\)**  セクションまで下へスクロールします。

   1.  **[Install Objective-C Compatibility Header]\(Objective-C 互換性ヘッダーをインストールする\)**   オプションが  **[Yes]\(はい\)** に設定されていることを確認します。

   1. ファイル パス `'<ProjectName>/BridgingHeader.h'` を **[Objective-C bridging Header]\(Objective-C ブリッジ ヘッダー\)**   オプションに入力します。 これは、今回使用するブリッジ ヘッダーのファイル パスです。

   これらのオプションが見つからない場合は、 **[Basic]\(基本\)** または **[Customized]\(カスタマイズ\)** ではなく **[すべて]** ビューが選択されていることを確認してください。

   **CommonCrypto** を簡単に使用できるようになるサードパーティ製のオープンソース ラッパー ライブラリがたくさんあります。 ただし、そのようなライブラリについてはこの記事では取り扱いません。

1. **TokenUtility.swift** という名前の新しい Swift ファイルを **Utilities** フォルダー内に追加し、次のコードを追加します。

   ```swift
   import Foundation

   struct TokenUtility {    
        typealias Context = UnsafeMutablePointer<CCHmacContext>

        static func getSasToken(forResourceUrl resourceUrl : String, withKeyName keyName : String, andKey key : String, andExpiryInSeconds expiryInSeconds : Int = 3600) -> TokenData {
            let expiry = (Int(NSDate().timeIntervalSince1970) + expiryInSeconds).description
            let encodedUrl = urlEncodedString(withString: resourceUrl)
            let stringToSign = "\(encodedUrl)\n\(expiry)"
            let hashValue = sha256HMac(withData: stringToSign.data(using: .utf8)!, andKey: key.data(using: .utf8)!)
            let signature = hashValue.base64EncodedString(options: .init(rawValue: 0))
            let encodedSignature = urlEncodedString(withString: signature)
            let sasToken = "SharedAccessSignature sr=\(encodedUrl)&sig=\(encodedSignature)&se=\(expiry)&skn=\(keyName)"
            let tokenData = TokenData(withToken: sasToken, andTokenExpiration: expiryInSeconds)

            return tokenData
        }

        private static func sha256HMac(withData data : Data, andKey key : Data) -> Data {
            let context = Context.allocate(capacity: 1)
            CCHmacInit(context, CCHmacAlgorithm(kCCHmacAlgSHA256), (key as NSData).bytes, size_t((key as NSData).length))
            CCHmacUpdate(context, (data as NSData).bytes, (data as NSData).length)
            var hmac = Array<UInt8>(repeating: 0, count: Int(CC_SHA256_DIGEST_LENGTH))
            CCHmacFinal(context, &hmac)

            let result = NSData(bytes: hmac, length: hmac.count)
            context.deallocate()

            return result as Data
        }

        private static func urlEncodedString(withString stringToConvert : String) -> String {
            var encodedString = ""
            let sourceUtf8 = (stringToConvert as NSString).utf8String
            let length = strlen(sourceUtf8)

            let charArray: [Character] = [ ".", "-", "_", "~", "a", "z", "A", "Z", "0", "9"]
            let asUInt8Array = String(charArray).utf8.map{ Int8($0) }

            for i in 0..<length {
                let currentChar = sourceUtf8![i]

                if (currentChar == asUInt8Array[0] || currentChar == asUInt8Array[1] || currentChar == asUInt8Array[2] || currentChar == asUInt8Array[3] ||
                    (currentChar >= asUInt8Array[4] && currentChar <= asUInt8Array[5]) ||
                    (currentChar >= asUInt8Array[6] && currentChar <= asUInt8Array[7]) ||
                    (currentChar >= asUInt8Array[8] && currentChar <= asUInt8Array[9])) {
                    encodedString += String(format:"%c", currentChar)
                }
                else {
                    encodedString += String(format:"%%%02x", currentChar)
                }
            }

            return encodedString
        }
    }
   ```

   このユーティリティでは、SAS トークンの生成を担うロジックをカプセル化しています。

   以前に説明したように、**getSasToken** 関数は、トークンを準備するために必要な大まかな手順を調整します。 この関数は、このチュートリアルの後半でインストール サービスによって呼び出されます。

   **getSasToken** 関数によって、他の 2 つの関数が呼び出されます。署名を計算するための **sha256HMac** と、関連付けられた URL 文字列をエンコードするための **urlEncodedString** です。 **urlEncodedString** 関数が必要なのは、組み込みの **addingPercentEncoding** 関数を使用しても必要な出力が得られないからです。

   [Azure Storage iOS SDK](https://github.com/Azure/azure-storage-ios/blob/master/Lib/Azure%20Storage%20Client%20Library/Azure%20Storage%20Client%20Library/AZSUtil.m) は、Objective-C でこれらの操作にアプローチする方法の良い例です。 Azure Service Bus SAS トークンの詳細については、[Azure Service Bus に関するドキュメント](../service-bus-messaging/service-bus-sas.md)を参照してください。

### <a name="verify-the-sas-token"></a>SAS トークンを検証する

クライアントにインストール サービスを実装する前に、任意の HTTP ユーティリティを使用して、アプリから正常に SAS トークンが生成されていることを確認してください。 このチュートリアルのために私たちが選んだツールは **Postman** です。

適切に配置された print ステートメントまたはブレークポイントを使用して、アプリによって生成される **installationId** および **token** の値をメモします。

次の手順に従って、**Installations** API を呼び出します。

1. **Postman** で、新しいタブを開きます。

1. **GET** の要求を設定し、次のアドレスを指定します。

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/installations/<installationId>?api-version=2015-01
    ```

1. 次のように要求ヘッダーを構成します。

   | キー           | 値            |
   | ------------- | ---------------- |
   | Content-Type  | application/json |
   | Authorization | \<sasToken>       |
   | x-ms-version  | 2015-01          |

1. **[保存]** ボタンの下の、右上に表示される **[コード]** ボタンを選択します。 要求は次の例のようになります。

    ```html
    GET /<hubName>/installations/<installationId>?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json
    Authorization: <sasToken>
    x-ms-version: 2015-01
    Cache-Control: no-cache
    Postman-Token: <postmanToken>
    ```

1. **[送信]** ボタンを選択します。

この時点では、指定した **installationId** の登録は存在しません。 検証の結果は、"401 Unauthorized" 応答ではなく "404 Not Found" 応答になります。 この結果により、SAS トークンが受け入れられたことを確認できます。

### <a name="implement-the-installation-service-class"></a>インストール サービス クラスを実装する

次に、[Installations REST API](/rest/api/notificationhubs/create-overwrite-installation) の基本的なラッパーを実装します。  

**NotificationRegistrationService.swift** という名前の新しい Swift ファイルを **Services** フォルダーの下に追加した後、このファイルに次のコードを追加します。

```swift
import Foundation

class NotificationRegistrationService {
    private let tokenizedBaseAddress: String = "https://%@.servicebus.windows.net/%@"
    private let tokenizedCreateOrUpdateInstallationRequest = "/installations/%@?api-version=%@"
    private let session = URLSession(configuration: URLSessionConfiguration.default)
    private let apiVersion = "2015-01"
    private let jsonEncoder = JSONEncoder()
    private let defaultHeaders: [String : String]
    private let installationId : String
    private let pushChannel : String
    private let hubNamespace : String
    private let hubName : String
    private let keyName : String
    private let key : String
    private var tokenData : TokenData? = nil

    init(withInstallationId installationId : String,
            andPushChannel pushChannel : String,
            andHubNamespace hubNamespace : String,
            andHubName hubName : String,
            andKeyName keyName : String,
            andKey key: String) {
        self.installationId = installationId
        self.pushChannel = pushChannel
        self.hubNamespace = hubNamespace
        self.hubName = hubName
        self.keyName = keyName
        self.key = key
        self.defaultHeaders = ["Content-Type": "application/json", "x-ms-version": apiVersion]
    }

    func register(
        withTags tags : [String]? = nil,
        andTemplates templates : Dictionary<String, PushTemplate>? = nil,
        completeWith completion: ((_ result: Bool) -> ())? = nil) {

        var deviceInstallation = DeviceInstallation(withInstallationId: installationId, andPushChannel: pushChannel)

        if let tags = tags {
            deviceInstallation.tags = tags
        }

        if let templates = templates {
            deviceInstallation.templates = templates
        }

        if let deviceInstallationJson = encodeToJson(deviceInstallation) {
            let sasToken = getSasToken()
            let requestUrl = String.init(format: tokenizedCreateOrUpdateInstallationRequest, installationId, apiVersion)
            let apiEndpoint = "\(getBaseAddress())\(requestUrl)"

            var request = URLRequest(url: URL(string: apiEndpoint)!)
            request.httpMethod = "PUT"

            for (key,value) in self.defaultHeaders {
                request.addValue(value, forHTTPHeaderField: key)
            }

            request.addValue(sasToken, forHTTPHeaderField: "Authorization")
            request.httpBody = Data(deviceInstallationJson.utf8)

            (self.session.dataTask(with: request) { dat, res, err in
                if let completion = completion {
                        completion(err == nil && (res as! HTTPURLResponse).statusCode == 200)
                }
            }).resume()
        }
    }

    private func getBaseAddress() -> String {
        return String.init(format: tokenizedBaseAddress, hubNamespace, hubName)
    }

    private func getSasToken() -> String {
        if (tokenData == nil ||
            Date(timeIntervalSince1970: Double((tokenData?.expiration)!)) < Date(timeIntervalSinceNow: -(5 * 60))) {
            self.tokenData = TokenUtility.getSasToken(forResourceUrl: getBaseAddress(), withKeyName: self.keyName, andKey: self.key)
        }

        return (tokenData?.token)!
    }

    private func encodeToJson<T : Encodable>(_ object: T) -> String? {
        do {
            let jsonData = try jsonEncoder.encode(object)
            if let jsonString = String(data: jsonData, encoding: .utf8) {
                return jsonString
            } else {
                return nil
            }
        }
        catch {
            return nil
        }
    }
}
```

必要条件の詳細については、初期化の一部として提供されています。 タグとテンプレートはオプションで **register** 関数に渡され、**デバイスのインストール** JSON ペイロードの一部を形成します。  

**register** 関数は、他のプライベート関数を呼び出して要求を準備します。 応答が受信された後、completion が呼び出され、登録が成功したかどうかを示します。  

要求エンドポイントは **getBaseAddress** 関数によって構築されます。 構築には、初期化時に提供された通知ハブ パラメーターの *namespace* と *name* が使用されます。  

**getSasToken** 関数は、現在格納されているトークンが有効かどうかを確認します。 トークンが有効でない場合、関数は値を返す前に **TokenUtility** を呼び出して、新しいトークンを生成して格納します。

最後に、**encodeToJson** により、それぞれのモデル オブジェクトが、要求本文の一部として使用するために JSON に変換されます。

### <a name="invoke-the-notification-hubs-rest-api"></a>Notification Hubs REST API を呼び出す

最後の手順では、**NotificationRegistrationService** を使用して **NotificationHub** に登録するように、**AppDelegate** を更新します。

1. **AppDelegate.swift** を開き、**NotificationRegistrationService** への参照を格納するクラスレベルの変数を追加します。

    ```swift
    var registrationService : NotificationRegistrationService?
    ```

1. 同じファイルで、必要なパラメーターを使用して **NotificationRegistrationService** を初期化した後、**register** 関数を呼び出すように、**didRegisterForRemoteNotificationsWithDeviceToken** 関数を更新します。

    ```swift
    func application(_ application: UIApplication, didRegisterForRemoteNotificationsWithDeviceToken deviceToken: Data) {
        let installationId = (UIDevice.current.identifierForVendor?.description)!
        let pushChannel = deviceToken.reduce("", {$0 + String(format: "%02X", $1)})

        // Initialize the Notification Registration Service
        self.registrationService = NotificationRegistrationService(
            withInstallationId: installationId,
            andPushChannel: pushChannel,
            andHubNamespace: notificationHubNamespace!,
            andHubName: notificationHubName!,
            andKeyName: notificationHubKeyName!,
            andKey: notificationHubKey!)

        // Call register, passing in the tags and template parameters
        self.registrationService!.register(withTags: tags, andTemplates: ["genericTemplate" : self.genericTemplate]) { (result) in
            if !result {
                print("Registration issue")
            } else {
                print("Registered")
            }
        }
    }
    ```

    わかりやすいように、いくつかの print ステートメントを使用して、**register** 操作の結果で出力ウィンドウを更新します。

1. 物理デバイス上でアプリをビルドして実行します。 出力ウィンドウに "Registered" と表示されます。

## <a name="test-the-solution"></a>ソリューションをテストする

アプリはこの段階で **NotificationHub** に登録されており、プッシュ通知を受け取ることができます。 Xcode で、デバッガーを停止し、アプリが現在実行中の場合はアプリを終了します。 次に、**デバイスのインストール**の詳細が想定どおりであり、アプリがプッシュ通知を受信できるようになったことを確認します。  

### <a name="verify-the-device-installation"></a>デバイスのインストールを確認する

ここで、以前に [SAS トークンを検証する](#verify-the-sas-token)ために **Postman** を使用して行ったのと同じ要求を行うことができます。 SAS トークンの有効期限が切れていないと仮定すると、応答には、指定したインストールの詳細 (テンプレート、タグなど) が含まれるようになっているはずです。

```json
{
    "installationId": "<installationId>",
    "pushChannel": "<pushChannel>",
    "pushChannelExpired": false,
    "platform": "apns",
    "expirationTime": "9999-12-31T23:59:59.9999999Z",
    "tags": [
        "12345"
    ],
    "templates": {
        "genericTemplate": {
            "body": "{\"aps\":{\"alert\":\"$(message)\"}}",
            "tags": [
                "genericTemplate"
            ]
        }
    }
}
```

### <a name="send-a-test-notification-azure-portal"></a>テスト通知を送信する (Azure portal)

通知を受信できるようになったことをテストする最も簡単な方法は、Azure portal の通知ハブを参照することです。

1. Azure portal で、ご自分の通知ハブの **[概要]** タブを参照します。

1. **[テスト送信]** を選択します。これは、ポータル ウィンドウの左上で **[要点]** サマリーの上にあります。

    ![Notification Hubs の [要点] サマリーの [テスト送信] ボタン](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-essentials-test-send.png)

1. **[プラットフォーム]** 一覧から **[カスタム テンプレート]** を選択します。

1. **[Send to Tag Expression]\(タグ式に送信\)** に「**12345**」と入力します。 このタグは以前にインストールで指定したものです。

1. オプションで、JSON ペイロードの**メッセージ**を編集します。

    ![Notification Hubs でのテスト送信](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send.png)

1. **[送信]** を選択します。 ポータルに、通知がデバイスに正常に送信されたかどうかが示されます。

    ![Notification Hubs でのテスト送信の結果](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/hub-test-send-result.png)

    アプリがフォアグラウンドで実行されていないと想定して、ご自分のデバイスの**通知センター**にも通知が表示されます。 通知をタップすると、アプリが開き、アラートが表示されます。

    ![通知の受信の例](./media/notification-hubs-ios-push-notifications-swift-apps-get-started/test-send-notification-received.png)

### <a name="send-a-test-notification-mail-carrier"></a>テスト通知を送信する (メール通信事業者)

**Postman** を使用して、[REST API](/rest/api/notificationhubs/) を介して通知を送信できます。テストするにはこの方法の方が便利かもしれません。

1. **Postman** で新しいタブを開きます。

1. **POST** の要求を設定し、次のアドレスを入力します。

    ```xml
    https://<namespace>.servicebus.windows.net/<hubName>/messages/?api-version=2015-01
    ```

1. 次のように要求ヘッダーを構成します。

   | キー                            | 値                          |
   | ------------------------------ | ------------------------------ |
   | Content-Type                   | application/json;charset=utf-8 |
   | Authorization                  | \<sasToken>                     |
   | ServiceBusNotification-Format  | template                       |
   | Tags                           | "12345"                        |

1. 次の JSON ペイロードで **RAW - JSON (application.json)** を使用するように要求**本文**を構成します。

    ```json
    {
       "message" : "Hello from Postman!"
    }
    ```

1. **[コード]** ボタンを選択します。これはウィンドウの右上で **[保存]** ボタンの下にあります。 要求は次の例のようになります。

    ```html
    POST /<hubName>/messages/?api-version=2015-01 HTTP/1.1
    Host: <namespace>.servicebus.windows.net
    Content-Type: application/json;charset=utf-8.
    ServiceBusNotification-Format: template
    Tags: "12345"
    Authorization: <sasToken>
    Cache-Control: no-cache
    Postman-Token: <postmanToken>

    {
        "message" : "Hello from Postman!"
    }
    ```

1. **[送信]** ボタンを選択します。

成功状態コードが届くと共に、クライアント デバイスで通知が届きます。

## <a name="next-steps"></a>次の手順
これで、[REST API](/rest/api/notificationhubs/) を介して通知ハブに接続された基本的な iOS Swift アプリが完成し、通知を送受信できるようになりました。 詳細については、次の記事を参照してください。

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
