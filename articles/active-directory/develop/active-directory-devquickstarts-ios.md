---
title: "Azure AD の iOS アプリへの統合 | Microsoft Docs"
description: "サインインし、OAuth を使用して Azure AD で保護されている API を呼び出すために Azure AD と統合する iOS アプリケーションを構築する方法を説明します。"
services: active-directory
documentationcenter: ios
author: brandwe
manager: mbaldwin
editor: 
ms.assetid: 42303177-9566-48ed-8abb-279fcf1e6ddb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
ms.custom: aaddev
ms.openlocfilehash: 57f465df99ac234466459b8031f61805d8334b59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Azure AD の iOS アプリへの統合
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

> [!TIP]
> Azure Active Directory をほんの数分で稼働するために役立つ、新しい[開発者ポータル](https://identity.microsoft.com/Docs/iOS)のプレビュー版をお試しください。  開発者ポータルでは、アプリを登録して、コードに Azure AD を統合するプロセスが説明されています。  完了すると、テナント内のユーザーを認証できる簡単なアプリケーションと、トークンを受け取って検証を実行できるバックエンドを手に入れることになります。 
> 
> 

Azure Active Directory (Azure AD) には、保護されたリソースにアクセスする必要がある iOS クライアント向けに、Active Directory 認証ライブラリ (ADAL) が用意されています。 ADAL は、アプリがアクセス トークンを取得する際のプロセスを簡略化します。 それがどれほど簡単であるかを示すために、この記事で、次のような Objective C To-Do List アプリケーションを構築します。

* [OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して、Azure AD Graph API を呼び出すためのアクセス トークンを取得します。
* 指定されたエイリアスを持つユーザーをディレクトリで検索します。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

1. Azure AD にアプリケーションを登録する
2. ADAL をインストールして構成する
3. ADAL を使用して、Azure AD からトークンを取得する

最初に、[アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)します。 また、ユーザーを作成してアプリケーションを登録するための Azure AD テナントも必要です。 テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。


> [!TIP]
> Azure AD をほんの数分で稼働するために役立つ、新しい[開発者ポータル](https://identity.microsoft.com/Docs/iOS)のプレビュー版をお試しください。 開発者ポータルでは、アプリを登録して、コードに Azure AD を統合するプロセスが説明されています。 完了すると、テナント内のユーザーを認証できる簡単なアプリケーションと、トークンを受け取って検証を実行できるバックエンドを手に入れることになります。 
> 
> 

## <a name="1-determine-what-your-redirect-uri-is-for-ios"></a>1.iOS 用のリダイレクト URI を決定する
特定の SSO シナリオでアプリケーションを安全に起動するには、*リダイレクト URI* を特定の形式で作成する必要があります。 リダイレクト URI は、トークンが要求された適切なアプリケーションに返されるようにするために使用します。


iOS のリダイレクト URI の形式は次のとおりです。

```
<app-scheme>://<bundle-id>
```

* **aap-scheme** - これは XCode プロジェクトに登録されています。 他のアプリケーションから呼び出す方法を示します。 これは、Info.plist、URL types、URL ID の順に探すと見つかります。 まだ 1 つも構成していない場合は作成する必要があります。
* **bundle-id** : XCode プロジェクトの設定の "identity" の下にある Bundle Identifier です。

この QuickStart コードの例は次のようになります。 ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2.DirectorySearcher アプリケーションを登録する
アプリでトークンを取得するようにするには、まず、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部のバーで、自分のアカウントをクリックします。 **[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。
3. 一番左のナビゲーション ウィンドウで **[その他のサービス]** をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックし、**[追加]** を選択します。
5. 画面の指示に従って、新しい**ネイティブ クライアント アプリケーション**を作成します。
  * アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
  * **[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用するスキームと文字列の組み合わせを設定します。  上記のリダイレクト URI 情報に基づいて、アプリケーション固有の値を入力します。
6. 登録が完了すると、Azure AD によって、一意のアプリケーション ID がアプリに割り当てられます。  この値は次のセクションで必要になるので、[アプリケーション] タブからコピーします。
7. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。 API として **[Microsoft Graph]** を選択し、**[委任されたされたアクセス許可]** の下に **[ディレクトリ データの読み取り]** アクセス許可を追加します。  これにより、アプリケーションが Azure AD Graph API を使用してユーザーをクエリするようになります。

## <a name="3-install-and-configure-adal"></a>3.ADAL をインストールして構成する
アプリケーションを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。  ADAL が Azure AD と通信するには、アプリの登録に関するいくつかの情報を ADAL に提供する必要があります。

1. CocoaPods を使用して DirectorySearcher プロジェクトに ADAL を追加することから始めます。

    ```
    $ vi Podfile
    ```
2. このポッドファイルに次のコードを追加します。

    ```
    source 'https://github.com/CocoaPods/Specs.git'
    link_with ['QuickStart']
    xcodeproj 'QuickStart'

    pod 'ADALiOS'
    ```

3. ここで、CocoaPods を使用してポッドファイルを読み込みます。 それにより、読み込む新しい XCode ワークスペースが作成されます。

    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

4. QuickStart プロジェクトで、.plist ファイル `settings.plist`を開きます。  Azure Portal で入力した値が反映されるように、セクションの要素の値を置き換えます。 これらの値は、コードで ADAL を使用する際に常に参照されます。
  * `tenant` は、Azure AD テナントのドメイン (contoso.onmicrosoft.com など) です。
  * `clientId` は、ポータルからコピーしたアプリケーションのクライアント ID である必要があります。
  * `redirectUri` は、ポータルに登録したリダイレクト URL です。

## <a name="4----use-adal-to-get-tokens-from-azure-ad"></a>4.  ADAL を使用して、Azure AD からトークンを取得する
ADAL の背後にある基本的な原理として、アプリはアクセス トークンが必要になるたびに、completionBlock `+(void) getToken : ` を呼び出すだけで、残りの処理は ADAL で実行されます。  

1. `QuickStart` プロジェクトで、`GraphAPICaller.m` を開き、上部の近くにある `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` コメントを探します。  ここでは、CompletionBlock で ADAL に座標を渡し、Azure AD と通信して、トークンをキャッシュする方法を指示します。

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

2. 次に、このトークンを使用して、グラフでユーザーを検索する必要があります。 `// TODO: implement SearchUsersList` コメントを検索します。 このメソッドは、指定された検索用語で UPN が始まるユーザーをクエリするための、Azure AD Graph API に対する GET 要求を実行します。  Azure AD Graph API をクエリするには、要求の `Authorization` ヘッダーに access_token を含める必要があります。 ここで、ADAL の出番です。

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


3. アプリが `getToken(...)` を呼び出すことによってトークンを要求すると、ADAL はユーザーに資格情報を要求することなく、トークンを返そうとします。  ADAL は、トークンの取得にユーザーのサインインが必要であると判断した場合、サインイン用のダイアログ ボックスを表示し、ユーザーの資格情報を収集してから、認証の成功後にトークンを返します。  何らかの理由によりトークンを返せない場合、`AdalException` をスローします。

> [!Note] 
> `AuthenticationResult` オブジェクトには、アプリが必要とする可能性のある情報の収集に使用される `tokenCacheStoreItem` オブジェクトが含まれています。 QuickStart では、認証が既に行われたかどうかを確認するために `tokenCacheStoreItem` が使用されます。
>
>

## <a name="5-build-and-run-the-application"></a>5.アプリケーションの構築と実行
ご利用ありがとうございます。 これで、作業中の iOS アプリケーションでは、ユーザーの認証、OAuth 2.0 を使用した Web API の安全な呼び出し、ユーザーに関する基本情報の取得が可能になりました。  テナントに一連のユーザーを設定します (設定していない場合)。  QuickStart アプリを起動し、そのユーザーの 1 人としてサインインします。  UPN に基づいて、他のユーザーを検索します。  アプリを閉じて再起動します。  ユーザーのセッションがそのままに維持されています。

ADAL を使用することにより、これらの共通 ID 機能のすべてを容易にアプリケーションに組み込むことができます。  キャッシュ管理、OAuth プロトコル サポート、ユーザーへのサインイン UI の表示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。  習得する必要があるのは、単一の API 呼び出し、 `getToken`のみです。

参考のため、完成済みサンプル (構成値を除く) が [GitHub](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip) で提供されています。  

## <a name="next-steps"></a>次のステップ
ここからは、さらなるシナリオに進むことができます。  次のチュートリアルを試してみてください。

* [Azure AD による Node.JS Web API のセキュリティ保護](active-directory-devquickstarts-webapi-nodejs.md)
* [iOS で ADAL を使用してクロス アプリ SSO を有効にする方法](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]

