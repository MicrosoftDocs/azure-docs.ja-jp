---
title: Azure Mobile Apps を使用した iOS での認証の追加
description: Azure Mobile Apps を使用して、AAD、Google、Facebook、Twitter、Microsoft などのさまざまな ID プロバイダーを通じて iOS アプリのユーザーを認証する方法について説明します。
services: app-service\mobile
documentationcenter: ios
author: elamalani
manager: crdun
editor: ''
ms.assetid: ef3d3cbe-e7ca-45f9-987f-80c44209dc06
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 88e278ced5cbddb132cdc2f760864df119762088
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449132"
---
# <a name="add-authentication-to-your-ios-app"></a>iOS アプリに認証を追加する
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーと関わることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-ios-get-started-users) を今すぐチェックしてください。
>

このチュートリアルでは、サポートされている ID プロバイダーを使用して、 [iOS クイック スタート] プロジェクトに認証を追加します。 最初に、このチュートリアルの基になっている [iOS クイック スタート] チュートリアルを完了しておく必要があります。

## <a name="register"></a>アプリケーションを認証に登録し、App Service を構成する
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>許可されている外部リダイレクト URL にアプリを追加する

認証をセキュリティで保護するには、アプリ用の新しい URL スキームの定義が必要になります。  これによって、認証プロセスが完了すると認証システムからアプリにリダイレクトできます。  このチュートリアル全体を通して、URL スキーム _appname_ を使用します。  ただし、選択したあらゆる URL スキームを使用できます。  URL スキームは、モバイル アプリに対して一意である必要があります。  サーバー側でリダイレクトを有効にするには、以下の手順に従います。

1. [Azure Portal] で、App Service を選択します。

2. **[認証/承認]** メニュー オプションをクリックします。

3. **[認証プロバイダー]** セクションの下の **[Azure Active Directory]** をクリックします。

4. **[Management mode (管理モード)]** を **[Advanced (詳細)]** に設定します。

5. **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** に `appname://easyauth.callback` を入力します。  この文字列の _appname_ は、モバイル アプリケーションの URL スキームです。  プロトコルの通常の URL 仕様 (文字と数字のみを使用し、文字で始まる) に従う必要があります。  数か所で URL スキームに合わせてモバイル アプリケーション コードを調整する必要があるため、選択した文字列をメモしておく必要があります。

6. Click **OK**.

7. **[Save]** をクリックします。

## <a name="permissions"></a>アクセス許可を、認証されたユーザーだけに制限する
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode で、 **[Run]** をクリックしてアプリケーションを開始します。 認証されないユーザーとしてアプリがバックエンドにアクセスしようとしても、*TodoItem* テーブルで認証が要求されるために例外が発生します。

## <a name="add-authentication"></a>アプリケーションに認証を追加する
**Objective-C**:

1. Mac の Xcode で *QSTodoListViewController.m* を開き、次のメソッドを追加します。

    ```Objective-C
    - (void)loginAndGetData
    {
        QSAppDelegate *appDelegate = (QSAppDelegate *)[UIApplication sharedApplication].delegate;
        appDelegate.qsTodoService = self.todoService;

        [self.todoService.client loginWithProvider:@"google" urlScheme:@"appname" controller:self animated:YES completion:^(MSUser * _Nullable user, NSError * _Nullable error) {
            if (error) {
                NSLog(@"Login failed with error: %@, %@", error, [error userInfo]);
            }
            else {
                self.todoService.client.currentUser = user;
                NSLog(@"User logged in: %@", user.userId);

                [self refresh];
            }
        }];
    }
    ```

    Google を ID プロバイダーとして使用しない場合は、*google* を *microsoftaccount*、*twitter*、*facebook*、*windowsazureactivedirectory* のいずれかに変更します。 Facebook を使用する場合、アプリで [Facebook ドメインをホワイトリストに追加する][1]必要があります。

    **urlScheme** をアプリケーションの一意の名前に置き換えます。  urlScheme は、Azure Portal **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** フィールドに指定した URL スキーム プロトコルと同じにする必要があります。 urlScheme は、認証要求が完了した後にアプリケーションに戻るために、認証コールバックで使用されます。

2. *QSTodoListViewController.m* 内の `viewDidLoad` の `[self refresh]` を次のコードに置き換えます。

    ```Objective-C
    [self loginAndGetData];
    ```

3. `QSAppDelegate.h` ファイルを開き、次のコードを追加します。

    ```Objective-C
    #import "QSTodoService.h"

    @property (strong, nonatomic) QSTodoService *qsTodoService;
    ```

4. `QSAppDelegate.m` ファイルを開き、次のコードを追加します。

    ```Objective-C
    - (BOOL)application:(UIApplication *)application openURL:(NSURL *)url options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
    {
        if ([[url.scheme lowercaseString] isEqualToString:@"appname"]) {
            // Resume login flow
            return [self.qsTodoService.client resumeWithURL:url];
        }
        else {
            return NO;
        }
    }
    ```

   `#pragma mark - Core Data stack` という行の前にこのコードを直接追加します。  _appname_ を、手順 1. で使用した urlScheme 値で置き換えます。

5. `AppName-Info.plist` ファイル (AppName はアプリの名前で置き換える) を開き、次のコードを追加します。

    ```XML
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    このコードは、`<dict>` 要素内に配置する必要があります。  _appname_ 文字列 (**CFBundleURLSchemes** の配列内) を、手順 1 で選択したアプリ名で置き換えます。  plist エディターでこれらに変更を加えることもできます。XCode で `AppName-Info.plist` ファイルをクリックして plist エディターを開きます。

    **CFBundleURLName** の `com.microsoft.azure.zumo` 文字列を Apple のバンドル識別子で置き換えます。

6. *[実行]* をクリックしてアプリを起動したら、ログインします。 ログインが成功すると、Todo リストを表示して更新できます。

**Swift**:

1. Mac の Xcode で *ToDoTableViewController.swift* を開き、次のメソッドを追加します。

    ```swift
    func loginAndGetData() {

        guard let client = self.table?.client, client.currentUser == nil else {
            return
        }

        let appDelegate = UIApplication.shared.delegate as! AppDelegate
        appDelegate.todoTableViewController = self

        let loginBlock: MSClientLoginBlock = {(user, error) -> Void in
            if (error != nil) {
                print("Error: \(error?.localizedDescription)")
            }
            else {
                client.currentUser = user
                print("User logged in: \(user?.userId)")
            }
        }

        client.login(withProvider:"google", urlScheme: "appname", controller: self, animated: true, completion: loginBlock)

    }
    ```

    Google を ID プロバイダーとして使用しない場合は、*google* を *microsoftaccount*、*twitter*、*facebook*、*windowsazureactivedirectory* のいずれかに変更します。 Facebook を使用する場合、アプリで [Facebook ドメインをホワイトリストに追加する][1]必要があります。

    **urlScheme** をアプリケーションの一意の名前に置き換えます。  urlScheme は、Azure Portal **[Allowed External Redirect URLs (許可されている外部リダイレクト URL)]** フィールドに指定した URL スキーム プロトコルと同じにする必要があります。 urlScheme は、認証要求が完了した後にアプリケーションに戻るために、認証コールバックで使用されます。

2. *ToDoTableViewController.swift*の `viewDidLoad()` の最後にある `self.refreshControl?.beginRefreshing()` と `self.onRefresh(self.refreshControl)` の行を削除します。 その場所に `loginAndGetData()` の呼び出しを追加します。

    ```swift
    loginAndGetData()
    ```

3. `AppDelegate.swift` ファイルを開き、次のコード行を `AppDelegate` クラスに追加します。

    ```swift
    var todoTableViewController: ToDoTableViewController?

    func application(_ application: UIApplication, openURL url: NSURL, options: [UIApplicationOpenURLOptionsKey : Any] = [:]) -> Bool {
        if url.scheme?.lowercased() == "appname" {
            return (todoTableViewController!.table?.client.resume(with: url as URL))!
        }
        else {
            return false
        }
    }
    ```

    _appname_ を、手順 1. で使用した urlScheme 値で置き換えます。

4. `AppName-Info.plist` ファイル (AppName はアプリの名前で置き換える) を開き、次のコードを追加します。

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
        <dict>
            <key>CFBundleURLName</key>
            <string>com.microsoft.azure.zumo</string>
            <key>CFBundleURLSchemes</key>
            <array>
                <string>appname</string>
            </array>
        </dict>
    </array>
    ```

    このコードは、`<dict>` 要素内に配置する必要があります。  _appname_ 文字列 (**CFBundleURLSchemes** の配列内) を、手順 1 で選択したアプリ名で置き換えます。  plist エディターでこれらに変更を加えることもできます。XCode で `AppName-Info.plist` ファイルをクリックして plist エディターを開きます。

    **CFBundleURLName** の `com.microsoft.azure.zumo` 文字列を Apple のバンドル識別子で置き換えます。

5. *[実行]* をクリックしてアプリを起動したら、ログインします。 ログインが成功すると、Todo リストを表示して更新できます。

App Service の認証では、Apple の Inter-App Communication が使用されます。  このトピックの詳細については、[Apple のドキュメント][2]を参照してください。
<!-- URLs. -->

[1]: https://developers.facebook.com/docs/ios/ios9#whitelist
[2]: https://developer.apple.com/library/content/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/Inter-AppCommunication/Inter-AppCommunication.html
[Azure Portal]: https://portal.azure.com

[iOS クイック スタート]: app-service-mobile-ios-get-started.md

