---
title: OAuth 2.0 を使用してサインインするために Azure AD と統合された iOS アプリを作成する | Microsoft Docs
description: iOS アプリからユーザーにサインインし、Microsoft Graph API を呼び出す方法について説明します。
services: active-directory
documentationcenter: ios
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: brandwe
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a82a7cad9b9176589824b6febb5cfdde89fce8a
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380878"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-app"></a>クイック スタート:iOS アプリからユーザーにサインインし、Microsoft Graph API を呼び出す

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

Azure Active Directory (Azure AD) には、保護されたリソースにアクセスする必要がある iOS クライアント向けに、Active Directory 認証ライブラリ (ADAL) が用意されています。 ADAL は、アプリがアクセス トークンを取得する際のプロセスを簡略化します。 

このクイック スタートでは、以下の操作を行う Objective C To-Do List アプリケーションを構築する方法について説明します。

* OAuth 2.0 認証プロトコルを使用して、Azure AD Graph API を呼び出すためのアクセス トークンを取得する
* 指定されたエイリアスを持つユーザーをディレクトリで検索する

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

1. Azure AD にアプリケーションを登録する
1. ADAL をインストールして構成する
1. ADAL を使用して、Azure AD からトークンを取得する

## <a name="prerequisites"></a>前提条件

最初に、以下の前提条件を完了します。

* [アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)する。
* ユーザーを作成してアプリケーションを登録するための Azure AD テナントがある。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](quickstart-create-new-tenant.md)」を参照して取得してください。

> [!TIP]
> Azure AD をほんの数分で稼働するには、[開発者ポータル](https://identity.microsoft.com/Docs/iOS)を試してください。 開発者ポータルでは、アプリを登録して、コードに Azure AD を統合するプロセスが説明されています。 完了すると、テナント内のユーザーを認証できる簡単なアプリケーションと、トークンを受け取って検証を実行できるバックエンドを手に入れることになります。

## <a name="step-1-determine-what-your-redirect-uri-is-for-ios"></a>手順 1:iOS 用のリダイレクト URI を決定する

特定の SSO シナリオでアプリケーションを安全に起動するには、*リダイレクト URI* を特定の形式で作成する必要があります。 リダイレクト URI は、トークンが要求された適切なアプリケーションに返されるようにするために使用します。

iOS のリダイレクト URI の形式は次のとおりです。

```
<app-scheme>://<bundle-id>
```

* **app-scheme** - XCode プロジェクトに登録される、他のアプリケーションが呼び出す方法です。 app-scheme は、**Info.plist、URL types、URL ID** の順に探すと見つかります。 app-scheme をまだ 1 つも構成していない場合は、作成します。
* **bundle-id** - XCode プロジェクトの設定の **identity** の下にある Bundle Identifier です。

このクイック スタートのコード例:

***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="step-2-register-the-directorysearcher-application"></a>手順 2:DirectorySearcher アプリケーションを登録する

アプリでトークンを取得するようにするには、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 上部のバーで、自分のアカウントを選択します。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。
3. 左側のナビゲーション ウィンドウで **[すべてのサービス]** を選択し、 **[Azure Active Directory]** を選択します。
4. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
5. 画面の指示に従って、新しいクライアント アプリケーションを作成します。
    * **[名前]** はアプリケーションの名前で、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
    * **[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用するスキームと文字列の組み合わせを設定します。 上記のリダイレクト URI 情報に基づいて、アプリケーション固有の値を入力します。 また、 **[パブリック クライアント (モバイルとデスクトップ)]** をドロップダウンから選択します。
6. 登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。 この値は次のセクションで必要になるので、[アプリケーション] タブからコピーします。
7. **[API のアクセス許可]** ページで、 **[アクセス許可の追加]** を選択します。 **[API を選択します]** 内の ***[Microsoft Graph]*** を選択します。
8. **[委任されたアクセス許可]** で、アクセス許可 **User.Read** を選択し、 **[追加]** を選択して保存します。 このアクセス許可により、アプリケーションが Azure AD Graph API を使用してユーザーをクエリするように設定されます。

## <a name="step-3-install-and-configure-adal"></a>手順 3:ADAL をインストールして構成する

アプリケーションを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。 ADAL が Azure AD と通信するには、アプリの登録に関するいくつかの情報を ADAL に提供する必要があります。

1. CocoaPods を使用して DirectorySearcher プロジェクトに ADAL を追加することから始めます。

    ```
    $ vi Podfile
    ```
1. このポッドファイルに次のコードを追加します。

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADAL'
    ```

1. CocoaPods を使用してポッドファイルを読み込みます。 それにより、読み込む新しい XCode ワークスペースが作成されます。

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

1. QuickStart プロジェクトで、.plist ファイル `settings.plist`を開きます。
1. Azure portal で入力した値が使用されるように、セクションの要素の値を置き換えます。 これらの値は、コードで ADAL を使用する際に常に参照されます。
    * `tenant` は、Azure AD テナントのドメイン (contoso.onmicrosoft.com など) です。
    * `clientId` は、ポータルからコピーしたアプリケーションのクライアント ID です。
    * `redirectUri` は、ポータルに登録したリダイレクト URL です。

## <a name="step-4-use-adal-to-get-tokens-from-azure-ad"></a>手順 4:ADAL を使用して、Azure AD からトークンを取得する

ADAL の背後にある基本的な原理として、アプリはアクセス トークンが必要になるたびに、completionBlock `+(void) getToken :` を呼び出すだけで、残りの処理は ADAL で実行されます。

1. `QuickStart` プロジェクトで、`GraphAPICaller.m` を開き、上部の近くにある `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` コメントを探します。

    ここでは、CompletionBlock で ADAL に座標を渡し、Azure AD と通信して、トークンをキャッシュする方法を指示します。

    ```ObjC
    +(void) getToken : (BOOL) clearCache
               parent:(UIViewController*) parent
    completionHandler:(void (^) (NSString*, NSError*))completionBlock;
    {
        AppData* data = [AppData getInstance];
        if(data.userItem){
            completionBlock(data.userItem.accessToken, nil);
            return;
        }

        ADAuthenticationError *error;
        authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
        authContext.parentController = parent;
        NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

        [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
        [authContext acquireTokenWithResource:data.resourceId
                                     clientId:data.clientId
                                  redirectUri:redirectUri
                               promptBehavior:AD_PROMPT_AUTO
                                       userId:data.userItem.userInformation.userId
                        extraQueryParameters: @"nux=1" // if this strikes you as strange it was legacy to display the correct mobile UX. You most likely won't need it in your code.
                             completionBlock:^(ADAuthenticationResult *result) {

                                  if (result.status != AD_SUCCEEDED)
                                  {
                                     completionBlock(nil, result.error);
                                  }
                                  else
                                  {
                                      data.userItem = result.tokenCacheStoreItem;
                                      completionBlock(result.tokenCacheStoreItem.accessToken, nil);
                                  }
                             }];
    }

    ```

2. このトークンを使用して、グラフでユーザーを検索する必要があります。 `// TODO: implement SearchUsersList` コメントを検索します。 このメソッドは、指定された検索用語で UPN が始まるユーザーをクエリするための、Azure AD Graph API に対する GET 要求を実行します。 

    Azure AD Graph API をクエリするには、要求の `Authorization` ヘッダーに access_token を含める必要があります。 ここで、ADAL の出番です。

    ```ObjC
    +(void) searchUserList:(NSString*)searchString
                    parent:(UIViewController*) parent
          completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
    {
        if (!loadedApplicationSettings)
       {
            [self readApplicationSettings];
        }
        
        AppData* data = [AppData getInstance];

        NSString *graphURL = [NSString stringWithFormat:@"%@%@/users?api-version=%@&$filter=startswith(userPrincipalName, '%@')", data.taskWebApiUrlString, data.tenant, data.apiversion, searchString];

        [self craftRequest:[self.class trimString:graphURL]
                    parent:parent
         completionHandler:^(NSMutableURLRequest *request, NSError *error) {

             if (error != nil)
             {
                 completionBlock(nil, error);
             }
             else
             {

                 NSOperationQueue *queue = [[NSOperationQueue alloc]init];

                 [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                     if (error == nil && data != nil){

                         NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                         // We can grab the JSON node at the top to get our graph data.
                         NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                         // Don't be thrown off by the key name being "value". It really is the name of the
                         // first node. :-)

                         // Each object is a key value pair
                         NSDictionary *keyValuePairs;
                         NSMutableArray* Users = [[NSMutableArray alloc]init];

                         for(int i =0; i < graphDataArray.count; i++)
                         {
                             keyValuePairs = [graphDataArray objectAtIndex:i];

                             User *s = [[User alloc]init];
                             s.upn = [keyValuePairs valueForKey:@"userPrincipalName"];
                             s.name =[keyValuePairs valueForKey:@"givenName"];

                             [Users addObject:s];
                         }

                         completionBlock(Users, nil);
                     }
                     else
                     {
                         completionBlock(nil, error);
                     }

                }];
             }
         }];

    }

    ```

3. アプリが `getToken(...)` を呼び出すことによってトークンを要求すると、ADAL はユーザーに資格情報を要求することなく、トークンを返そうとします。 ADAL は、トークンの取得にユーザーのサインインが必要であると判断した場合、サインイン用のダイアログ ボックスを表示し、ユーザーの資格情報を収集してから、認証の成功後にトークンを返します。 何らかの理由によりトークンを返せない場合、`AdalException` をスローします。

> [!NOTE]
> `AuthenticationResult` オブジェクトには、アプリが必要とする可能性のある情報の収集に使用できる `tokenCacheStoreItem` オブジェクトが含まれています。 QuickStart では、認証が既に行われたかどうかを確認するために `tokenCacheStoreItem` が使用されます。

## <a name="step-5-build-and-run-the-application"></a>手順 5:アプリケーションの構築と実行

お疲れさまでした。 これで、作業中の iOS アプリケーションでは、ユーザーの認証、OAuth 2.0 を使用した Web API の安全な呼び出し、ユーザーに関する基本情報の取得が可能になりました。

テナントに一連のユーザーを設定します (設定していない場合)。

1. QuickStart アプリを起動し、そのユーザーの 1 人としてサインインします。
1. UPN に基づいて、他のユーザーを検索します。
1. アプリを閉じて再起動します。 ユーザーのセッションがそのままに維持されています。

ADAL を使用することにより、これらの共通 ID 機能のすべてを容易にアプリケーションに組み込むことができます。 キャッシュ管理、OAuth プロトコル サポート、ユーザーへのサインイン UI の表示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。 習得する必要があるのは、単一の API 呼び出し、 `getToken`のみです。

参考のため、完成済みサンプル (構成値を除く) が [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) で提供されています。

## <a name="next-steps"></a>次の手順

ここからは、さらなるシナリオに進むことができます。 次のことを試すことをお勧めします。

* [Azure AD による Node.JS Web API のセキュリティ保護](quickstart-v1-nodejs-webapi.md)
* [iOS で ADAL を使用してクロス アプリ SSO を有効にする方法](howto-v1-enable-sso-ios.md)  
