---
title: Azure Mobile Apps 向け iOS SDK の使用方法
description: Azure Mobile Apps 向け iOS SDK の使用方法
services: app-service\mobile
documentationcenter: ios
author: elamalani
editor: ''
ms.assetid: 4e8e45df-c36a-4a60-9ad4-393ec10b7eb9
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 38d992e55a8e1f0a057a96f3e13c93c9dbd0c4a9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440383"
---
# <a name="how-to-use-ios-client-library-for-azure-mobile-apps"></a>Azure Mobile Apps 向け iOS クライアント ライブラリの使用方法

[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center では、モバイル アプリ開発の中心となる新しい統合サービスに投資しています。 開発者は、**ビルド**、**テスト**、**配布**のサービスを使用して、継続的インテグレーションおよびデリバリー パイプラインを設定できます。 アプリがデプロイされたら、開発者は**分析**および**診断**のサービスを利用してアプリの状態と使用状況を監視し、**プッシュ** サービスを利用してユーザーとかかわることができます。 また、開発者は **Auth** を利用してユーザーを認証し、**データ** サービスを利用してクラウド内のアプリ データを保持および同期することもできます。 [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=/app-service-mobile-ios-how-to-use-client-library) を今すぐチェックしてください。
>

## <a name="overview"></a>概要
このガイドでは、最新の [Azure Mobile Apps iOS SDK][1] を使用して一般的なシナリオを実行する方法について説明します。 Azure Mobile Apps を初めて使用する場合は、まず、「 [Apache Cordova アプリの作成] 」を参照して、バックエンドの作成、テーブルの作成、構築済みの iOS Xcode プロジェクトのダウンロードを行ってください。 このガイドでは、クライアント側の iOS SDK に重点を置いています。 バックエンドのサーバー側 SDK の詳細については、サーバー SDK の使用方法に関する記事をご覧ください。

## <a name="reference-documentation"></a>リファレンス ドキュメント

iOS クライアント SDK のリファレンス ドキュメントは、[Azure Mobile Apps iOS クライアントのリファレンス][2]に関するページにあります。

## <a name="supported-platforms"></a>サポートされているプラットフォーム

iOS SDK は、iOS バージョン 8.0 以降の Objective-C プロジェクト、Swift 2.2 プロジェクト、Swift 2.3 プロジェクトをサポートしています。

"サーバーフロー" の認証では、UI の表示に WebView を使用します。  デバイスで WebView UI を表示できない場合は、この製品の対象外である別の認証方法が必要になります。  
そのため、この SDK は腕時計型デバイスのような制限付きデバイスには適していません。

## <a name="Setup"></a>セットアップと前提条件

このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。 このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。 また、コードで `MicrosoftAzureMobile.framework` を参照し、`MicrosoftAzureMobile/MicrosoftAzureMobile.h` をインポートしていることも前提となります。

## <a name="create-client"></a>方法:クライアントを作成する

プロジェクトで Azure Mobile Apps バックエンドにアクセスするには、 `MSClient`を作成します。 `AppUrl` をアプリの URL に置き換えます。 `gatewayURLString` と `applicationKey` は空のままにしておいてかまいません。 認証用のゲートウェイを設定する場合は、ゲートウェイの URL で `gatewayURLString` を設定します。

**Objective-C**:

```objc
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```swift
let client = MSClient(applicationURLString: "AppUrl")
```

## <a name="table-reference"></a>方法:テーブル参照を作成する

データへのアクセスやデータの更新を行うには、バックエンド テーブルへの参照を作成します。 `TodoItem` を実際のテーブルの名前に置き換えます。

**Objective-C**:

```objc
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```swift
let table = client.tableWithName("TodoItem")
```

## <a name="querying"></a>方法:データのクエリを実行する

データベース クエリを作成するには、 `MSTable` オブジェクトのクエリを実行します。 次のクエリは、 `TodoItem` 内のすべての項目を取得し、各項目のテキストをログに記録します。

**Objective-C**:

```objc
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) { // error is nil if no error occurred
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) { // items is NSArray of records that match query
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="filtering"></a>方法:返されるデータをフィルター処理する

結果をフィルター処理するには、多くのオプションを使用できます。

述語を使用してフィルター処理するには、`NSPredicate` と `readWithPredicate` を使用します。 次のコードは、返されたデータをフィルター処理して、不完全な Todo 項目のみを見つけます。

**Objective-C**:

```objc
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="query-object"></a>方法:MSQuery を使用する

複雑なクエリ (並べ替えとページングを含む) を実行するには、 `MSQuery` オブジェクトを直接作成するか、述語を使用して作成します。

**Objective-C**:

```objc
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```swift
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` を使用すると、複数のクエリ動作を制御できます。

* 結果の順序を指定する
* 返すフィールドを制限する
* 返すレコード数を制限する
* 応答の合計数を指定する
* 要求内にカスタム クエリ文字列パラメーターを指定する
* 追加の関数を適用する

オブジェクトで `readWithCompletion` を呼び出して、`MSQuery` クエリを実行します。

## <a name="sorting"></a>方法:MSQuery でデータを並べ替える

結果の並べ替えの例を見てみましょう。 "text" フィールドを昇順で並べ替えた後、"complete" を降順で並べ替えるには、次のように `MSQuery` を呼び出します。

**Objective-C**:

```objc
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
        if(error) {
                NSLog(@"ERROR %@", error);
        } else {
                for(NSDictionary *item in result.items) {
                        NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
                }
        }
}];
```

**Swift**:

```swift
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

## <a name="selecting"></a><a name="parameters"></a>方法:MSQuery でフィールドを制限し、クエリ文字列パラメーターを展開する

クエリで返されるフィールドを制限するには、 **selectFields** プロパティにフィールド名を指定します。 次の例では、text フィールドと completed フィールドだけが返されます。

**Objective-C**:

```objc
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```swift
query.selectFields = ["text", "complete"]
```

サーバー要求に追加のクエリ文字列パラメーターを含めるには (たとえば、カスタム サーバー側のスクリプトで使用されているため)、 `query.parameters` 次のように設定します。

**Objective-C**:

```objc
query.parameters = @{
    @"myKey1" : @"value1",
    @"myKey2" : @"value2",
};
```

**Swift**:

```swift
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

## <a name="paging"></a>方法:ページ サイズを構成する

Azure Mobile Apps では、バックエンド テーブルから同時に取得されるレコードの数がページ サイズで制御されます。 `pull` データの呼び出しは、このページ サイズに基づいて、プルするレコードがなくなるまで、データをバッチで追加します。

次のように、**MSPullSettings** を使用してページ サイズを構成することができます。 既定のページ サイズは 50 ですが、次の例では 3 に変更します。

パフォーマンス上の理由から、別のページ サイズを構成できます。 小さいデータ レコードが多数ある場合は、ページ サイズが大きければ、サーバーのラウンド トリップの数が少なくなります。

この設定では、クライアント側のページ サイズのみが制御されます。 クライアントが要求するページ サイズが、Mobile Apps バックエンドでサポートされるページ サイズを超える場合は、バックエンドがサポートするように構成されている最大ページ サイズが上限になります。

これも、"*バイト サイズ*" でなく、データ レコードの "*数*" で設定します。

クライアントのページ サイズを増やす場合は、サーバーのページ サイズを大きくする必要があります。 そのための手順については、「[方法:テーブルのページング サイズを調整する](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

**Objective-C**:

```objc
  MSPullSettings *pullSettings = [[MSPullSettings alloc] initWithPageSize:3];
  [table  pullWithQuery:query queryId:@nil settings:pullSettings
                        completion:^(NSError * _Nullable error) {
                               if(error) {
                    NSLog(@"ERROR %@", error);
                }
                           }];
```

**Swift**:

```swift
let pullSettings = MSPullSettings(pageSize: 3)
table.pullWithQuery(query, queryId:nil, settings: pullSettings) { (error) in
    if let err = error {
        print("ERROR ", err)
    }
}
```

## <a name="inserting"></a>方法:データを挿入する

新しいテーブル行を挿入するには、`NSDictionary` を作成し、`table insert` を呼び出します。 [動的スキーマ]が有効になっている場合、Azure App Service モバイル バックエンドは、`NSDictionary` に基づいて新しい列を自動的に生成します。

`id` が指定されていない場合、バックエンドは新しい一意の ID を自動的に生成します。 電子メール アドレス、ユーザー名、または独自のカスタム値を ID として使用するには、独自の `id` を指定します。 独自の ID を指定すると、結合およびビジネス指向のデータベース ロジックを容易にすることができます。

`result` には、挿入された新しい項目が含まれます。 サーバー ロジックによっては、サーバーに渡された内容と比較して、追加または変更されたデータが含まれる場合があります。

**Objective-C**:

```objc
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

## <a name="modifying"></a>方法:データを変更する

既存の行を更新するには、次のように項目を変更して `update`を呼び出します。

**Objective-C**:

```objc
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

あるいは、次のように行 ID と更新フィールドを指定します。

**Objective-C**:

```objc
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
    }
}];
```

**Swift**:

```swift
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

更新操作を行う場合は、少なくとも `id` 属性を設定する必要があります。

## <a name="deleting"></a>方法:データを削除する

項目を削除するには、次のように、該当する項目を指定して `delete` を呼び出します。

**Objective-C**:

```objc
[table delete:item completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

あるいは、次のように行 ID を指定して削除します。

**Objective-C**:

```objc
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    } else {
        NSLog(@"Todo Item ID: %@", itemId);
    }
}];
```

**Swift**:

```swift
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

削除操作を行う場合は、少なくとも `id` 属性を設定する必要があります。

## <a name="customapi"></a>方法:カスタム API の呼び出し

カスタム API を使用して、任意のバックエンド機能を公開できます。 テーブル操作にマップする必要はありません。 メッセージングを詳細に制御できるだけでなく、ヘッダーの読み取り/設定や応答本文の形式の変更も可能です。 バックエンドでカスタム API を作成する方法については、「 [カスタム API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)

カスタム API を呼び出すには、 `MSClient.invokeAPI`を呼び出します。 要求と応答のコンテンツは、JSON として扱われます。 他のメディアの種類を使用するには、[`invokeAPI` の他のオーバーロードを使用します][5]。  `POST` 要求の代わりに `GET` 要求を行うには、パラメーター `HTTPMethod` を `"GET"` に設定し、パラメーター `body` を `nil` に設定します (GET 要求にはメッセージ本文がないため)。カスタム API で、他の HTTP 動詞をサポートする場合は、それに応じて `HTTPMethod` を変更します。

**Objective-C**:

```objc
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```swift
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

## <a name="templates"></a>方法:プッシュ テンプレートを登録してクロス プラットフォーム通知を送信する

テンプレートを登録するには、 **client.push registerDeviceToken** メソッドを使用してクライアント アプリにテンプレートを渡します。

**Objective-C**:

```objc
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
    if(error) {
        NSLog(@"ERROR %@", error);
    }
}];
```

**Swift**:

```swift
client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
    if let err = error {
        print("ERROR ", err)
    }
})
```

テンプレートは NSDictionary 型であり、次の形式で複数のテンプレートを含めることができます。

**Objective-C**:

```objc
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```swift
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

セキュリティを確保するために、要求からすべてのタグが削除されます。  インストールまたはインストール内のテンプレートにタグを追加する方法については、「 [Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作][4]」を参照してください。  これらの登録済みテンプレートを使用して通知を送信するには、[Notification Hubs API シリーズ][3]を使用します。

## <a name="errors"></a>方法:エラーを処理する

Azure App Service モバイル バックエンドを呼び出すと、完了ブロックに `NSError` パラメーターが含まれます。 エラーが発生すると、このパラメーターは null 以外の値になります。 前のコード スニペットに示すように、コードでは、このパラメーターを確認し、必要に応じてエラーを処理する必要があります。

[`<WindowsAzureMobileServices/MSError.h>`][6] ファイルで、`MSErrorResponseKey`、`MSErrorRequestKey`、`MSErrorServerItemKey` の各定数を定義します。 エラーに関するさらに多くのデータを取得するには、次のコードを使用します。

**Objective-C**:

```objc
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```swift
let serverItem = error.userInfo[MSErrorServerItemKey]
```

さらに、このファイルでエラー コードごとに定数を定義します。

**Objective-C**:

```objc
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```swift
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>方法:Active Directory 認証ライブラリを使用してユーザーを認証する

Active Directory 認証ライブラリ (ADAL) を使用して、Azure Active Directory を使用しているアプリケーションにユーザーをサインインさせることができます。 ID プロバイダー SDK を使用したクライアント フローの認証は、 `loginWithProvider:completion:` メソッドを使用する方法よりも推奨されます。  クライアント フローの認証により、よりネイティブな UX が実現し、さらにカスタマイズすることが可能になります。

1. 「 [Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法][7] 」のチュートリアルに従って、AAD のサインイン用にモバイル アプリ バックエンドを構成します。 ネイティブ クライアント アプリケーションを登録する省略可能な手順を確実に実行します。 iOS の場合、リダイレクト URI を `<app-scheme>://<bundle-id>` 形式にすることをお勧めします。 詳細については、[ADAL iOS のクイックスタート][8]に関する記事をご覧ください。
2. Cocoapods を使用して ADAL をインストールします。 POD ファイルを編集して次の定義を含め、 **YOUR-PROJECT** を Xcode プロジェクトの名前に置き換えます。

        source 'https://github.com/CocoaPods/Specs.git'
        link_with ['YOUR-PROJECT']
        xcodeproj 'YOUR-PROJECT'

   POD:

        pod 'ADALiOS'

3. ターミナルを使用して、プロジェクトを含むディレクトリから `pod install` を実行し、(プロジェクトではなく) 生成された Xcode ワークスペースを開きます。
4. ご使用の言語に応じて、アプリケーションに次のコードを追加します。 各コードでは、次のように置き換えます。

   * **INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。 形式は https://login.microsoftonline.com/contoso.onmicrosoft.com である必要があります。 この値は、[Azure Portal] の Azure Active Directory の [ドメイン] タブからコピーできます。
   * **INSERT-RESOURCE-ID-HERE** を、モバイル アプリ バックエンドのクライアント ID に置き換えます。 クライアント ID は、ポータルの **[Azure Active Directory の設定]** の **[詳細]** タブで入手できます。
   * **INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。
   * **INSERT-REDIRECT-URI-HERE** を、HTTPS スキームを使用して、サイトの */.auth/login/done* エンドポイントに置き換えます。 これは、 *https://contoso.azurewebsites.net/.auth/login/done* のような値である必要があります。

**Objective-C**:

```objc
#import <ADALiOS/ADAuthenticationContext.h>
#import <ADALiOS/ADAuthenticationSettings.h>
// ...
- (void) authenticate:(UIViewController*) parent
            completion:(void (^) (MSUser*, NSError*))completionBlock;
{
    NSString *authority = @"INSERT-AUTHORITY-HERE";
    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
    ADAuthenticationError *error;
    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
    authContext.parentController = parent;
    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
    [authContext acquireTokenWithResource:resourceId
                                    clientId:clientId
                                redirectUri:redirectUri
                            completionBlock:^(ADAuthenticationResult *result) {
                                if (result.status != AD_SUCCEEDED)
                                {
                                    completionBlock(nil, result.error);;
                                }
                                else
                                {
                                    NSDictionary *payload = @{
                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
                                                            };
                                    [client loginWithProvider:@"aad" token:payload completion:completionBlock];
                                }
                            }];
}
```

**Swift**:

```swift
// add the following imports to your bridging header:
//        #import <ADALiOS/ADAuthenticationContext.h>
//        #import <ADALiOS/ADAuthenticationSettings.h>

func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
    let authority = "INSERT-AUTHORITY-HERE"
    let resourceId = "INSERT-RESOURCE-ID-HERE"
    let clientId = "INSERT-CLIENT-ID-HERE"
    let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
    var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
    let authContext = ADAuthenticationContext(authority: authority, error: error)
    authContext.parentController = parent
    ADAuthenticationSettings.sharedInstance().enableFullScreen = true
    authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
            if result.status != AD_SUCCEEDED {
                completion(nil, result.error)
            }
            else {
                let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
                client.loginWithProvider("aad", token: payload, completion: completion)
            }
        }
}
```

## <a name="facebook-sdk"></a>方法:Facebook SDK for iOS でユーザーを認証する

Facebook SDK for iOS を使用すると、Facebook でアプリケーションにユーザーをサインインさせることができます。  クライアント フローの認証の使用は、 `loginWithProvider:completion:` メソッドを使用する方法よりも推奨されます。  クライアント フローの認証により、よりネイティブな UX が実現し、さらにカスタマイズすることが可能になります。

1. [Facebook ログイン用に App Service を構成する方法][9]に関するチュートリアルに従って、Facebook のサインイン用にモバイル アプリ バックエンドを構成します。
2. [Facebook SDK for iOS の概要][10]に関するページに従って Facebook SDK for iOS をインストールします。 アプリを作成する代わりに、既存の登録に iOS プラットフォームを追加できます。
3. Facebook のドキュメントには、App Delegate での Objective-C コードが含まれます。 **Swift** を使用している場合は、AppDelegate.swift に次の変換を使用できます。

    ```swift
    // Add the following import to your bridging header:
    //        #import <FBSDKCoreKit/FBSDKCoreKit.h>

    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
        // Add any custom logic here.
        return true
    }

    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
        let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
        // Add any custom logic here.
        return handled
    }
    ```
4. `FBSDKCoreKit.framework` をプロジェクトに追加するだけでなく、同じ方法で `FBSDKLoginKit.framework` への参照を追加することもできます。
5. ご使用の言語に応じて、アプリケーションに次のコードを追加します。

    **Objective-C**:

    ```objc
    #import <FBSDKLoginKit/FBSDKLoginKit.h>
    #import <FBSDKCoreKit/FBSDKAccessToken.h>
    // ...
    - (void) authenticate:(UIViewController*) parent
                completion:(void (^) (MSUser*, NSError*)) completionBlock;
    {
        FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
        [loginManager
            logInWithReadPermissions: @[@"public_profile"]
            fromViewController:parent
            handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
                if (error) {
                    completionBlock(nil, error);
                } else if (result.isCancelled) {
                    completionBlock(nil, error);
                } else {
                    NSDictionary *payload = @{
                                            @"access_token":result.token.tokenString
                                            };
                    [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
                }
            }];
    }
    ```

    **Swift**:

    ```swift
    // Add the following imports to your bridging header:
    //        #import <FBSDKLoginKit/FBSDKLoginKit.h>
    //        #import <FBSDKCoreKit/FBSDKAccessToken.h>

    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let loginManager = FBSDKLoginManager()
        loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
            if (error != nil) {
                completion(nil, error)
            }
            else if result.isCancelled {
                completion(nil, error)
            }
            else {
                let payload: [String: String] = ["access_token": result.token.tokenString]
                client.loginWithProvider("facebook", token: payload, completion: completion)
            }
        }
    }
    ```

## <a name="twitter-fabric"></a>方法:Twitter Fabric for iOS でユーザーを認証する

Fabric for iOS を使用すると、Twitter でアプリケーションにユーザーをサインインさせることができます。 クライアント フローの認証を使用すると、よりネイティブな UX が実現し、さらにカスタマイズすることが可能になるため、 `loginWithProvider:completion:` メソッドを使用する方法よりも推奨されます。

1. 「 [App Service アプリケーションを Twitter ログインを使用するように構成する方法](../app-service/configure-authentication-provider-twitter.md) 」のチュートリアルに従って、Twitter のサインイン用にモバイル アプリ バックエンドを構成します。
2. 「[Fabric for iOS - Getting Started (Fabric for iOS - 概要) (Fabric for iOS - 概要)]」ドキュメントに従ってプロジェクトに Fabric を追加し、TwitterKit をセットアップします。

   > [!NOTE]
   > 既定では、Fabric は Twitter アプリケーションを自動的に作成します。 次のコード スニペットを使用して、以前に作成したコンシューマー キーとコンシューマー シークレットを登録することにより、アプリケーションを作成しなくて済みます。    または、App Service に提供したコンシューマー キーとコンシューマー シークレットの値を、 [Fabric Dashboard]に表示される値に置き換えることもできます。 このオプションを選択する場合は、コールバック URL を `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`などのプレースホルダーの値に設定してください。

    以前に作成したシークレットを使用する場合は、App Delegate に次のコードを追加します。

    **Objective-C**:

    ```objc
    #import <Fabric/Fabric.h>
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
    {
        [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
        [Fabric with:@[[Twitter class]]];
        // Add any custom logic here.
        return YES;
    }
    ```

    **Swift**:

    ```swift
    import Fabric
    import TwitterKit
    // ...
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
        Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
        Fabric.with([Twitter.self])
        // Add any custom logic here.
        return true
    }
    ```

3. ご使用の言語に応じて、アプリケーションに次のコードを追加します。

    **Objective-C**:

    ```objc
    #import <TwitterKit/TwitterKit.h>
    // ...
    - (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
    {
        [[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
            if (session) {
                NSDictionary *payload = @{
                                            @"access_token":session.authToken,
                                            @"access_token_secret":session.authTokenSecret
                                        };
                [client loginWithProvider:@"twitter" token:payload completion:completionBlock];
            } else {
                completionBlock(nil, error);
            }
        }];
    }
    ```

    **Swift**:

    ```swift
    import TwitterKit
    // ...
    func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
        let client = self.table!.client
        Twitter.sharedInstance().logInWithCompletion { session, error in
            if (session != nil) {
                let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
                client.loginWithProvider("twitter", token: payload, completion: completion)
            } else {
                completion(nil, error)
            }
        }
    }
    ```

## <a name="google-sdk"></a>方法:Google Sign-In SDK for iOS でユーザーを認証する

Google Sign-In SDK for iOS を使用すると、Google アカウントでユーザーをアプリケーションにサインインさせることができます。  Google は、OAuth セキュリティ ポリシーの変更を最近発表しました。  ポリシーのこれらの変更により、将来的に Google SDK を使用することが必要になります。

1. [Google ログイン用に App Service を構成する方法](../app-service/configure-authentication-provider-google.md) のチュートリアルに従って、Google のサインイン用にモバイル アプリ バックエンドを構成します。
2. [Google Sign-In for iOS の統合の開始](https://developers.google.com/identity/sign-in/ios/start-integrating) に関するドキュメントに従って、Google SDK for iOS をインストールします。 バックエンド サーバーによる認証に関するセクションはスキップしてかまいません。
3. ご使用の言語に応じて、デリゲートの `signIn:didSignInForUser:withError:` メソッドに次のコードを追加します。

    **Objective-C**:
    ```objc
    NSDictionary *payload = @{
                                @"id_token":user.authentication.idToken,
                                @"authorization_code":user.serverAuthCode
                                };

    [client loginWithProvider:@"google" token:payload completion:^(MSUser *user, NSError *error) {
        // ...
    }];
    ```

    **Swift**:

    ```swift
    let payload: [String: String] = ["id_token": user.authentication.idToken, "authorization_code": user.serverAuthCode]
    client.loginWithProvider("google", token: payload) { (user, error) in
        // ...
    }
    ```

4. アプリ デリゲートの `application:didFinishLaunchingWithOptions:` に、次のコードも必ず追加してください。"SERVER_CLIENT_ID" は、手順 1. で App Service の構成に使用したものと同じ ID に置き換えます。

    **Objective-C**:

    ```objc
    [GIDSignIn sharedInstance].serverClientID = @"SERVER_CLIENT_ID";
    ```

     **Swift**:

    ```swift
    GIDSignIn.sharedInstance().serverClientID = "SERVER_CLIENT_ID"
    ```

5. ご使用の言語に応じて、`GIDSignInUIDelegate` プロトコルを実装する、アプリケーションの UIViewController に次のコードを追加します。  サインアウトしてサインインし直します。資格情報を再度入力する必要はありませんが、同意ダイアログが表示されます。  このメソッドを呼び出すのは、セッション トークンの有効期限が切れたときだけです。

   **Objective-C**:

    ```objc
    #import <Google/SignIn.h>
    // ...
    - (void)authenticate
    {
            [GIDSignIn sharedInstance].uiDelegate = self;
            [[GIDSignIn sharedInstance] signOut];
            [[GIDSignIn sharedInstance] signIn];
    }
    ```

   **Swift**:

    ```swift
    // ...
    func authenticate() {
        GIDSignIn.sharedInstance().uiDelegate = self
        GIDSignIn.sharedInstance().signOut()
        GIDSignIn.sharedInstance().signIn()
    }
    ```

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Apache Cordova アプリの作成]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode
[Azure Portal]: https://portal.azure.com/
[Handling Expired Tokens]: https://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: https://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: https://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[動的スキーマ]: https://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: https://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: https://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: https://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: /cli/azure/get-started-with-az-cli2
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

[Fabric Dashboard]: https://www.fabric.io/home
[Fabric for iOS - Getting Started (Fabric for iOS - 概要) (Fabric for iOS - 概要)]: https://docs.fabric.io/ios/fabric/getting-started.html
[1]: https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk
[2]: https://azure.github.io/azure-mobile-apps-ios-client/
[3]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[4]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags
[5]: https://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:
[6]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h
[7]: ../app-service/configure-authentication-provider-aad.md
[8]:../active-directory/develop/quickstart-v1-ios.md
[9]: ../app-service/configure-authentication-provider-facebook.md
[10]: https://developers.facebook.com/docs/ios/getting-started
