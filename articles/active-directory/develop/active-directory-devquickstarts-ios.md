---
title: "Azure AD iOS の概要 | Microsoft Docs"
description: "サインインと、OAuth を用いた Azure AD の保護 API の呼び出しのために Azure AD と統合する、iOS アプリケーションの構築方法を説明します。"
services: active-directory
documentationcenter: ios
author: xerners
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
translationtype: Human Translation
ms.sourcegitcommit: c579135f798ea0c2a5461fdd7c88244d2d6d78c6
ms.openlocfilehash: c74e63621d422f8fa13bc1dd2730ec2c3325a46a


---
# <a name="integrate-azure-ad-into-an-ios-app"></a>Azure AD の iOS アプリへの統合
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Azure AD には、保護されたリソースにアクセスする必要がある iOS クライアント向けに、Active Directory 認証ライブラリ (ADAL) が用意されています。  ADAL の唯一の目的は、アプリがアクセス トークンを容易に取得できるようにすることです。  それがどれほど簡単であるかを示すために、ここで、次のような、Objective-C の To-Do List アプリを構築します。

* [OAuth 2.0 認証プロトコル](https://msdn.microsoft.com/library/azure/dn645545.aspx)を使用して Azure AD Graph API を呼び出すためのアクセス トークンを取得します。
* 指定されたエイリアスを持つユーザーをディレクトリで検索します。

完全に動作するアプリケーションを構築するには、次の手順を行う必要があります。

1. Azure AD にアプリケーションを登録する
2. ADAL をインストールおよび構成する
3. ADAL を使用して、Azure AD からトークンを取得する

最初に、[アプリのスケルトンをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/skeleton.zip)するか、[完全なサンプルをダウンロード](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)します。  また、ユーザーを作成し、アプリケーションを登録することを可能にするための Azure AD テナントも必要です。  テナントを所有していない場合は、「 [How to get an Azure Active Directory tenant (Azure Active Directory テナントの取得方法)](active-directory-howto-tenant.md)」を参照して取得してください。

> [!TIP]
> Azure Active Directory をほんの数分で稼働するために役立つ、新しい [開発者ポータル](https://identity.microsoft.com/Docs/iOS) のプレビュー版をお試しください。  開発者ポータルでは、アプリを登録して、コードに Azure AD を統合するプロセスを説明してあります。  完了すると、テナント内のユーザーを認証できる単純なアプリケーションと、トークンを受け取って検証を実行できるバックエンドを手に入れることになります。 
> 
> 

## <a name="1-determine-what-your-redirect-uri-will-be-for-ios"></a>1.iOS 用のリダイレクト URI を決定する
特定の SSO シナリオでアプリケーションを安全に起動するには、 **リダイレクト URI** を特定の形式で作成する必要があります。 リダイレクト URI は、トークンがそのトークンを要求した適切なアプリケーションに返されるようにするために使用します。

iOS のリダイレクト URI の形式は次のとおりです。

```
<app-scheme>://<bundle-id>
```

* **aap-scheme** : これは XCode プロジェクトに登録されています。 他のアプリケーションから呼び出す方法を示します。 これは、Info.plist、URL types、URL ID の順に探すと見つかります。 まだ&1; つも構成していない場合は作成する必要があります。
* **bundle-id** : XCode プロジェクトの設定の "identity" の下にある Bundle Identifier です。

この QuickStart コードの例は次のようになります: ***msquickstart://com.microsoft.azureactivedirectory.samples.graph.QuickStart***

## <a name="2-register-the-directorysearcher-application"></a>2.DirectorySearcher アプリケーションを登録する
アプリでトークンを取得できるようにするには、まず、アプリを Azure AD テナントに登録し、Azure AD Graph API にアクセスするためのアクセス許可を付与する必要があります。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 上部のバーにある自分のアカウントをクリックし、**[ディレクトリ]** の一覧から、アプリケーションを登録する Active Directory テナントを選択します。
3. 左側のナビゲーションで **[More Services (その他のサービス)]**をクリックし、**[Azure Active Directory]** を選択します。
4. **[アプリの登録]** をクリックして、**[追加]** を選択します。
5. 画面の指示に従い、新しい **ネイティブ クライアント アプリケーション**を作成します。
  * アプリケーションの **[名前]** には、エンド ユーザーがアプリケーションの機能を把握できるような名前を設定します。
  * **[リダイレクト URI]** には、Azure AD がトークン応答を返すために使用するスキームと文字列の組み合わせを設定します。  上記の情報に基づいて、アプリケーション固有の値を入力します。
6. 登録が完了すると、AAD により、アプリに一意のアプリケーション ID が割り当てられます。  この値は次のセクションで必要になるので、[アプリケーション] タブからコピーします。
7. **[設定]** ページで、**[必要なアクセス許可]** を選択し、**[追加]** を選択します。 API として **[Microsoft Graph]** を選択し、**[デリゲートされたアクセス許可]** の下に **[ディレクトリ データの読み取り]** アクセス許可を追加します。  これにより、アプリケーションが Graph API を使用してユーザーをクエリできるようになります。

## <a name="3-install--configure-adal"></a>3.ADAL のインストールと構成
アプリケーションを Azure AD に登録したので、ADAL をインストールし、ID 関連のコードを記述できます。  ADAL が Azure AD と通信できるようにするには、アプリの登録に関するいくつかの情報を ADAL に提供する必要があります。

* Cocapods を使用して DirectorySearcher プロジェクトに ADAL を追加することから始めます。

```
$ vi Podfile
```
このポッドファイルに次のコードを追加します。

```
source 'https://github.com/CocoaPods/Specs.git'
link_with ['QuickStart']
xcodeproj 'QuickStart'

pod 'ADALiOS'
```

次に Cocoapods を使用してポッドファイルを読み込みます。 これにより、読み込む新しい XCode ワークスペースが作成されます。

```
$ pod install
...
$ open QuickStart.xcworkspace
```

* QuickStart プロジェクトで、.plist ファイル `settings.plist`を開きます。  Azure ポータルで入力した値が反映されるように、セクションの要素の値を置き換えます。  これらの値は、コードで ADAL を使用する際に常に参照されます。
  * `tenant` は、Azure AD テナントのドメイン (たとえば、contoso.onmicrosoft.com) です。
  * `clientId` は、ポータルからコピーしたアプリケーションのクライアント ID である必要があります。
  * `redirectUri` は、ポータルに登録したリダイレクト URL です。

## <a name="4----use-adal-to-get-tokens-from-aad"></a>4.  DAL を使用して AAD からトークンを取得する
ADAL の背後にある基本的な原理として、アプリはアクセス トークンが必要になるたびに、completionBlock `+(void) getToken : ` を呼び出すだけで、残りの処理は ADAL で実行されます。  

* `QuickStart` プロジェクトで、`GraphAPICaller.m` を開き、上部の近くにある `// TODO: getToken for generic Web API flows. Returns a token with no additional parameters provided.` コメントを探します。  ここでは、CompletionBlock で ADAL に座標を渡し、Azure AD と通信して、トークンをキャッシュする方法を指示します。

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

* 次に、このトークンを使用して、グラフでユーザーを検索する必要があります。 `// TODO: implement SearchUsersList` コメントを検索します。このメソッドは、指定された検索用語で UPN が始まるユーザーを照会するための GET 要求を Azure AD Graph API に対して実行します。  ただし、Graph API をクエリするためには、要求の `Authorization` ヘッダーに access_token を含める必要があります。この処理を ADAL が実行します。

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

                     // We can grab the top most JSON node to get our graph data.
                     NSArray *graphDataArray = [dataReturned objectForKey:@"value"];

                     // Don't be thrown off by the key name being "value". It really is the name of the
                     // first node. :-)

                     //each object is a key value pair
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
* アプリが `getToken(...)`を呼び出すことによってトークンを要求すると、ADAL はユーザーに資格情報を要求することなく、トークンを返そうとします。  ADAL は、トークンを取得するにはユーザーのサインインが必要であると判断した場合、ログイン ダイアログを表示し、ユーザーの資格情報を収集し、認証が成功するとトークンを返します。  また、何らかの理由によりトークンを返せない場合、 `AdalException`をスローします。
* `AuthenticationResult` オブジェクトには、アプリが必要とする可能性のある情報を収集するために使用される `tokenCacheStoreItem` オブジェクトが含まれていることに注意してください。  QuickStart では、認証が既に行われたかどうかを確認するために `tokenCacheStoreItem` が使用されます。

## <a name="5-build-and-run-the-application"></a>5. アプリケーションをビルドして実行する
お疲れさまでした。 これで、作業中の iOS アプリケーションでは、ユーザーの認証、OAuth 2.0 を使用した Web API の安全な呼び出し、ユーザーに関する基本情報の取得が可能になりました。  テナントに一連のユーザーを設定します (設定していない場合)。  QuickStart アプリを実行し、そのユーザーの&1; 人としてサインインします。  UPN に基づいて、他のユーザーを検索します。  アプリを閉じて、再び実行します。  ユーザーのセッションがそのままに維持されていることに注意します。

ADAL を使用することにより、これらの共通 ID 機能のすべてを容易にアプリケーションに組み込むことができます。  キャッシュ管理、OAuth プロトコル サポート、ログイン UI を使用してのユーザーの提示、有効期限切れとなったトークンの更新など、面倒な操作を容易に実装できます。  習得する必要があるのは、単一の API 呼び出し、 `getToken`のみです。

完全なサンプル (構成値を除く) を取得するには、 [ここ](https://github.com/AzureADQuickStarts/NativeClient-iOS/archive/complete.zip)をクリックしてください。  

## <a name="additional-scenarios"></a>その他のシナリオ
ここからは、さらなるシナリオに進むことができます。  次のチュートリアルを試してみてください。

* [Azure AD による Node.JS Web API のセキュリティ保護](active-directory-devquickstarts-webapi-nodejs.md)
* [iOS で ADAL を使用してクロス アプリ SSO を有効にする方法](active-directory-sso-ios.md)  

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]




<!--HONumber=Jan17_HO3-->


