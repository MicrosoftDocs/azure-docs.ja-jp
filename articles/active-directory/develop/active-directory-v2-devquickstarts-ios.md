---
title: "Azure AD v2.0 エンドポイントを使用して iOS アプリケーションにサインインを追加する | Microsoft Docs"
description: "サインインに個人の Microsoft アカウントと会社/学校アカウントの両方を使用する iOS アプリを、サード パーティのライブラリを使用して構築する方法を説明します。"
services: active-directory
documentationcenter: 
author: xerners
manager: mbaldwin
editor: 
ms.assetid: fd3603c0-42f7-438c-87b5-a52d20d6344b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/07/2017
ms.author: brandwe
translationtype: Human Translation
ms.sourcegitcommit: 47dce83cb4e3e5df92e91f1ca9195326634d6c8b
ms.openlocfilehash: 36c83ad9424c7c1e0bc096696148dda801bc4257


---
# <a name="add-sign-in-to-an-ios-app-using-a-third-party-library-with-graph-api-using-the-v20-endpoint"></a>サード パーティのライブラリと Graph API と v2.0 エンドポイントを使用して iOS アプリにサインインを追加する
Microsoft の ID プラットフォームには、OAuth2 や OpenID Connect といったオープンな標準が使用されています。 開発者は、任意のライブラリを使用して Microsoft のサービスと連携させることができます。 Microsoft では、そのプラットフォームを他のライブラリから使用する開発者のために、サード パーティのライブラリから Microsoft の ID プラットフォームに接続するための構成方法を紹介するチュートリアルを作成しています。この記事もそうしたチュートリアルの一つです。 Microsoft の ID プラットフォームには、[RFC6749 OAuth2 仕様](https://tools.ietf.org/html/rfc6749)を実装するほとんどのライブラリから接続できます。

このチュートリアルで作成するアプリケーションで、ユーザーは所属組織にサインインした後、Graph API を使用して組織内のユーザーを検索することができます。

ここで紹介する構成サンプルは OAuth2 や OpenID Connect に精通している読者を想定しており、それ以外の方にとっては、あまり参考にならない可能性があります。 背景については、「[v2.0 プロトコル: OAuth 2.0 承認コード フロー](active-directory-v2-protocols-oauth-code.md)」に目を通すことをお勧めします。

> [!NOTE]
> Microsoft のプラットフォーム機能のうち、OAuth2 や OpenID Connect の標準に含まれている式が使われているいくつかの機能 (条件付きアクセスや Intune ポリシー管理) については、オープン ソースの Microsoft Azure Identity Libraries を使用する必要があります。
> 
> 

Azure Active Directory のシナリオおよび機能のすべてが v2.0 エンドポイントでサポートされているわけではありません。

> [!NOTE]
> v2.0 エンドポイントを使用する必要があるかどうかを判断するには、 [v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。
> 
> 

## <a name="download-code-from-github"></a>GitHub からコードをダウンロードする
このチュートリアルのコードは、 [GitHub](https://github.com/Azure-Samples/active-directory-ios-native-nxoauth2-v2)で管理されています。  追加の参考資料として、 [アプリのスケルトン (.zip) をダウンロード](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) したり、スケルトンを複製したりすることができます:

```
git clone --branch skeleton git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

単にサンプルをダウンロードして作業してもかまいません。

```
git clone git@github.com:Azure-Samples/active-directory-ios-native-nxoauth2-v2.git
```

## <a name="register-an-app"></a>アプリを登録します
[アプリケーション登録ポータル](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)で新しいアプリを作成するか、「[v2.0 エンドポイントを使用してアプリケーションを登録する方法](active-directory-v2-app-registration.md)」の詳細な手順に従ってください。  次のことを確認します。

* アプリに割り当てられた**アプリケーション ID** をコピーしておきます。これは後で必要になります。
* アプリ用の **モバイル** プラットフォームを追加します。
* ポータルから **リダイレクト URI** をコピーしておきます。 既定値の `urn:ietf:wg:oauth:2.0:oob`を使用する必要があります。

## <a name="download-the-third-party-nxoauth2-library-and-create-a-workspace"></a>サード パーティ製 NXOAuth2 ライブラリのダウンロードとワークスペースの作成
このチュートリアルでは、Mac OS X および iOS (Cocoa および Cocoa Touch) 用の OAuth2 ライブラリ OAuth2Client を GitHub からクローンして使用します。 このライブラリのベースとなっているのは、OAuth2 仕様のドラフト 10 です。 このライブラリは、ネイティブ アプリケーション プロファイルを実装し、ユーザーの承認エンドポイントをサポートしています。 これらの条件さえ整えば、Microsoft の ID プラットフォームと連携させることができます。

### <a name="add-the-library-to-your-project-by-using-cocoapods"></a>CocoaPods を使用してプロジェクトにライブラリを追加する
CocoaPods は、Xcode プロジェクト用の依存関係マネージャーです。 上記のインストール手順は、CocoaPods によって自動的に管理されます。

```
$ vi Podfile
```
1. このポッドファイルに次のコードを追加します。
   
    ```
     platform :ios, '8.0'
   
     target 'QuickStart' do
   
     pod 'NXOAuth2Client'
   
     end
    ```
2. CocoaPods を使用してポッドファイルを読み込みます。 これにより、読み込む新しい Xcode ワークスペースが作成されます。
   
    ```
    $ pod install
    ...
    $ open QuickStart.xcworkspace
    ```

## <a name="explore-the-structure-of-the-project"></a>プロジェクトの構造を確認する
プロジェクトのスケルトンには、次の構造をセットアップしてあります。

* マスター ビュー (UPN 検索)
* 詳細ビュー (選択ユーザーについてのデータ用)
* ログイン ビュー (Graph に対して問い合わせを行うユーザーがアプリにサインインするときに使用)

ここでは、スケルトン内の各種ファイルにアクセスして認証機能を追加していきます。 その他のコード要素 (ビジュアル コードなど) は ID と密接な関係があるとは言えませんが、参考までに記載しています。

## <a name="set-up-the-settingsplst-file-in-the-library"></a>ライブラリの settings.plst ファイルのセットアップ
* QuickStart プロジェクトで、 `settings.plist` ファイルを開きます。 Azure Portal で使用した値が反映されるように、セクションの要素の値を置き換えます。 Active Directory Authentication Library を使用するたびに、コードはこれらの値を参照します。
  * `clientId` は、ポータルからコピーしたアプリケーションのクライアント ID である必要があります。
  * `redirectUri` は、ポータルから取得したリダイレクト URL です。

## <a name="set-up-the-nxoauth2client-library-in-your-loginviewcontroller"></a>LoginViewController の NXOAuth2Client ライブラリのセットアップ
NXOAuth2Client ライブラリでは、いくつかの値を設定する必要があります。 そのタスクが完了したら、取得したトークンを使って Graph API を呼び出すことができます。 認証が必要なときには常に `LoginView` が呼び出されるため、設定する値はそのファイルに記述するのが妥当です。

* 認証と承認のコンテキストを設定するために、いくつかの値を `LoginViewController.m` ファイルに追加しましょう。 値の詳細については、コードに従ってください。
  
    ```objc
    NSString *scopes = @"openid offline_access User.Read";
    NSString *authURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/authorize";
    NSString *loginURL = @"https://login.microsoftonline.com/common/login";
    NSString *bhh = @"urn:ietf:wg:oauth:2.0:oob?code=";
    NSString *tokenURL = @"https://login.microsoftonline.com/common/oauth2/v2.0/token";
    NSString *keychain = @"com.microsoft.azureactivedirectory.samples.graph.QuickStart";
    static NSString * const kIDMOAuth2SuccessPagePrefix = @"session_state=";
    NSURL *myRequestedUrl;
    NSURL *myLoadedUrl;
    bool loginFlow = FALSE;
    bool isRequestBusy;
    NSURL *authcode;
    ```

コードの詳細を見てみましょう。

最初は `scopes`の文字列です。  `User.Read` 値によって、サインインしているユーザーの基本プロファイルを読み取ることができます。

使用可能なすべてのスコープの詳細については、「 [Microsoft Graph のアクセス許可スコープ](https://graph.microsoft.io/docs/authorization/permission_scopes)」を参照してください。

`authURL`、`loginURL`、`bhh`、および `tokenURL` については、上記の値を使用する必要があります。 オープン ソースである Microsoft Azure Identity Libraries を使用する場合、このデータは、Microsoft のメタデータ エンドポイントを使用して自動的に取得されます。 これらの値を自動的に抽出するためのさまざまな工夫が Microsoft によって施されています。

`keychain` の値は、NXOAuth2Client ライブラリがトークンの格納先となるキーチェーンを作成する際に使用するコンテナーです。 多数のアプリでシングル サインオン (SSO) を利用したい場合は、それぞれのアプリケーションで同じキーチェーンを指定し、Xcode の Entitlement からそのキーチェーンの使用を要求することができます。 この点については、Apple のドキュメントを参照してください。

残りはライブラリを使用するために必要となる値で、目的のコンテキストに伝える値を保持するために使用されます。

### <a name="create-a-url-cache"></a>URL キャッシュを作成する
ビューが読み込まれた後に必ず呼び出される `(void)viewDidLoad()`の中で、必要なキャッシュを次のコードで用意します。

次のコードを追加します。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    self.loginView.delegate = self;
    [self setupOAuth2AccountStore];
    [self requestOAuth2Access];
    NSURLCache *URLCache = [[NSURLCache alloc] initWithMemoryCapacity:4 * 1024 * 1024
                                                         diskCapacity:20 * 1024 * 1024
                                                             diskPath:nil];
    [NSURLCache setSharedURLCache:URLCache];

}
```

### <a name="create-a-webview-for-sign-in"></a>サインインの WebView を作成する
WebView を介して SMS テキスト メッセージなどの付加的な要素の入力をユーザーに促したり (構成されている場合)、エラー メッセージをユーザーに返したりすることができます。 ここで WebView を設定した後、WebView 内で発生する ID サービスからのコールバックに対処するコードを記述します。

```objc
-(void)requestOAuth2Access {
    //to sign in to Microsoft APIs using OAuth2, we must show an embedded browser (UIWebView)
    [[NXOAuth2AccountStore sharedStore] requestAccessToAccountWithType:@"myGraphService"
                                   withPreparedAuthorizationURLHandler:^(NSURL *preparedURL) {
                                       //navigate to the URL returned by NXOAuth2Client

                                       NSURLRequest *r = [NSURLRequest requestWithURL:preparedURL];
                                       [self.loginView loadRequest:r];
                                   }];
}
```

### <a name="override-the-webview-methods-to-handle-authentication"></a>WebView のメソッドをオーバーライドして認証を処理する
上記のサインインをユーザーから要求されたときの動作を WebView に対して指定するには、次のコードを貼り付けます。

```objc
- (void)resolveUsingUIWebView:(NSURL *)URL {

    // We get the auth token from a redirect so we need to handle that in the webview.

    if (![NSThread isMainThread]) {
        [self performSelectorOnMainThread:@selector(resolveUsingUIWebView:) withObject:URL waitUntilDone:YES];
        return;
    }

    NSURLRequest *hostnameURLRequest = [NSURLRequest requestWithURL:URL cachePolicy:NSURLRequestUseProtocolCachePolicy timeoutInterval:10.0f];
    isRequestBusy = YES;
    [self.loginView loadRequest:hostnameURLRequest];

    NSLog(@"resolveUsingUIWebView ready (status: UNKNOWN, URL: %@)", self.loginView.request.URL);
}

- (BOOL)webView:(UIWebView *)webView shouldStartLoadWithRequest:(NSURLRequest *)request navigationType:(UIWebViewNavigationType)navigationType {

    NSLog(@"webView:shouldStartLoadWithRequest: %@ (%li)", request.URL, (long)navigationType);

    // The webview is where all the communication happens. Slightly complicated.

    myLoadedUrl = [webView.request mainDocumentURL];
    NSLog(@"***Loaded url: %@", myLoadedUrl);

    //if the UIWebView is showing our authorization URL or consent URL, show the UIWebView control
    if ([request.URL.absoluteString rangeOfString:authURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:loginURL options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = NO;
    } else if ([request.URL.absoluteString rangeOfString:bhh options:NSCaseInsensitiveSearch].location != NSNotFound) {
        //otherwise hide the UIWebView, we've left the authorization flow
        self.loginView.hidden = YES;
        [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }
    else {
        self.loginView.hidden = NO;
        //read the Location from the UIWebView, this is how Microsoft APIs is returning the
        //authentication code and relation information. This is controlled by the redirect URL we chose to use from Microsoft APIs
        //continue the OAuth2 flow
       // [[NXOAuth2AccountStore sharedStore] handleRedirectURL:request.URL];
    }

    return YES;

}
```

### <a name="write-code-to-handle-the-result-of-the-oauth2-request"></a>OAuth2 要求の結果を処理するコードを記述する
次のコードは、WebView から返された redirectURL を処理します。 認証に成功しなかった場合、コードは再試行します。 このときライブラリから生成されたエラーをコンソールに表示したり、非同期で処理したりすることができます。

```objc
- (void)handleOAuth2AccessResult:(NSString *)accessResult {

    AppData* data = [AppData getInstance];

    //parse the response for success or failure
     if (accessResult)
    //if success, complete the OAuth2 flow by handling the redirect URL and obtaining a token
     {
         [[NXOAuth2AccountStore sharedStore] handleRedirectURL:accessResult];
    } else {
        //start over
        [self requestOAuth2Access];
    }
}
```

### <a name="set-up-the-oauth-context-called-account-store"></a>OAuth コンテキスト (アカウント ストア) を設定する
ここで、アプリケーションから利用したいサービスごとに、共有アカウント ストアの `-[NXOAuth2AccountStore setClientID:secret:authorizationURL:tokenURL:redirectURL:forAccountType:]` を呼び出すことができます。 アカウント タイプは、特定のサービスの識別子として使用される文字列です。 アクセスの対象が Graph API なので、 `"myGraphService"`と呼ぶことにしましょう。 次に、トークンに変更が生じたときの通知をつかさどるオブザーバーを設定します。 トークンを取得したら、ユーザーを `masterView`に戻します。

```objc
- (void)setupOAuth2AccountStore {


        AppData* data = [AppData getInstance];

    [[NXOAuth2AccountStore sharedStore] setClientID:data.clientId
                                             secret:data.secret
                                              scope:[NSSet setWithObject:scopes]
                                   authorizationURL:[NSURL URLWithString:authURL]
                                           tokenURL:[NSURL URLWithString:tokenURL]
                                        redirectURL:[NSURL URLWithString:data.redirectUriString]
                                      keyChainGroup: keychain
                                     forAccountType:@"myGraphService"];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreAccountsDidChangeNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      if (aNotification.userInfo) {
                                                          //account added, we have access
                                                          //we can now request protected data
                                                          NSLog(@"Success!! We have an access token.");
                                                          dispatch_async(dispatch_get_main_queue(),^ {

                                                              MasterViewController* masterViewController = [self.storyboard instantiateViewControllerWithIdentifier:@"masterView"];
                                                              [self.navigationController pushViewController:masterViewController animated:YES];
                                                          });
                                                      } else {
                                                          //account removed, we lost access
                                                      }
                                                  }];

    [[NSNotificationCenter defaultCenter] addObserverForName:NXOAuth2AccountStoreDidFailToRequestAccessNotification
                                                      object:[NXOAuth2AccountStore sharedStore]
                                                       queue:nil
                                                  usingBlock:^(NSNotification *aNotification) {
                                                      NSError *error = [aNotification.userInfo objectForKey:NXOAuth2AccountStoreErrorKey];
                                                      NSLog(@"Error!! %@", error.localizedDescription);
                                                  }];
}
```

## <a name="set-up-the-master-view-to-search-and-display-the-users-from-the-graph-api"></a>Graph API からユーザーを検索して表示するための設定を Master View に対して行う
返されたデータをグリッドに表示する Master-View-Controller (MVC) アプリについては、このチュートリアルでは取り上げません。MVC アプリの作成方法を説明するチュートリアルはインターネットに数多く公開されています。 このコードはすべてスケルトン ファイルにあります。 ただし、以下に示した処理については、この MVC アプリケーション内で行う必要があります。

* 検索フィールドへのユーザー入力をインターセプトする。
* グリッドに結果を表示できるようデータのオブジェクトを MasterView に戻す。

以降、これらの処理について説明します。

### <a name="add-a-check-to-see-if-youre-logged-in"></a>ログインしているかどうかのチェックを追加する
このアプリケーションは、ユーザーがサインインしていなければほとんど機能を果たしません。そのため、キャッシュにトークンが既に存在するかどうかを確認する必要があります。 トークンが存在しない場合は、ユーザーにサインインを求めるため、LoginView にリダイレクトします。 先ほど触れたように、ビューが読み込まれたときにアクションを実行する最善の方法は、Apple から提供されている `viewDidLoad()` メソッドを使用することです。

```objc
- (void)viewDidLoad {
    [super viewDidLoad];


    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];

        if (accounts.count == 0) {

        dispatch_async(dispatch_get_main_queue(),^ {

            LoginViewController* userSelectController = [self.storyboard instantiateViewControllerWithIdentifier:@"LoginUserView"];
            [self.navigationController pushViewController:userSelectController animated:YES];
        });
        }
```

### <a name="update-the-table-view-when-data-is-received"></a>データが返されたときにテーブル ビューを更新する
Graph API がデータを返したら、データを表示させる必要があります。 手間を省くため、テーブルを更新するためのコード全体を以下に記載しました。 MVC のスケルトン コードにそのまま値を貼り付けるだけでかまいません。

```objc
#pragma mark - Table View

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return 1;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {

        return [upnArray count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {

    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:@"TaskPrototypeCell" forIndexPath:indexPath];

    if ( cell == nil ) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:@"TaskPrototypeCell"];
    }

    User *user = nil;
     user = [upnArray objectAtIndex:indexPath.row];


    // Configure the cell
    cell.textLabel.text = user.name;
    [cell setAccessoryType:UITableViewCellAccessoryDisclosureIndicator];

    return cell;
}

```

### <a name="provide-a-way-to-call-the-graph-api-when-someone-types-in-the-search-field"></a>検索フィールドへのユーザー入力があったときに Graph API を呼び出す手段を用意する。
ユーザーが検索ボックスに何かを入力したら、それを Graph API に渡す必要があります。 ルックアップ機能をプレゼンテーションのロジックから切り離すために、`GraphAPICaller` クラスを次のコードで作成します。 まずは、検索文字を Graph API に渡すためのコードを記述しましょう。 そのために、`lookupInGraph` というメソッドを作成します。検索対象の文字列を引数として受け取ります。

```objc

-(void)lookupInGraph:(NSString *)searchText {
if (searchText.length > 0) {

    };



        [GraphAPICaller searchUserList:searchText completionBlock:^(NSMutableArray* returnedUpns, NSError* error) {
            if (returnedUpns) {


                upnArray = returnedUpns;


            }
            else
            {
                UIAlertView *alertView = [[UIAlertView alloc]initWithTitle:nil message:[[NSString alloc]initWithFormat:@"Error : %@", error.localizedDescription] delegate:nil cancelButtonTitle:@"Retry" otherButtonTitles:@"Cancel", nil];

                [alertView setDelegate:self];

                dispatch_async(dispatch_get_main_queue(),^ {
                    [alertView show];
                });
            }


        }];


}
```

## <a name="write-a-helper-class-to-access-the-graph-api"></a>Graph API にアクセスするためのヘルパー クラスを作成する
これはアプリケーションの核となる部分です。 それ以外は Apple の既定の MVC パターンにコードを挿入することになります。一方、このヘルパー クラスには、ユーザーからの入力内容を Graph に問い合わせて、そのデータを取得するコードを記述します。 コードは次のとおりです。詳細な説明については、その後に記載しています。

### <a name="create-a-new-objective-c-header-file"></a>新しい Objective C ヘッダー ファイルを作成する
ファイルに「 `GraphAPICaller.h`」という名前を付け、次のコードを追加します。

```objc
@interface GraphAPICaller : NSObject<NSURLConnectionDataDelegate>

+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray*, NSError* error))completionBlock;

@end
```

ここに記述されているのは、文字列を引数として受け取って completionBlock を返すメソッドです。 もうおわかりかもしれませんが、この completionBlock は、ユーザーが検索を実行する過程で入力したデータをリアルタイムでオブジェクトに追加することによってテーブルを更新します。

### <a name="create-a-new-objective-c-file"></a>新しい Objective C ファイルを作成する
ファイルに「 `GraphAPICaller.m`」という名前を付け、次のメソッドを追加します。

```objc
+(void) searchUserList:(NSString*)searchString
       completionBlock:(void (^) (NSMutableArray* Users, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    AppData* data = [AppData getInstance];

    NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];

    NXOAuth2AccountStore *store = [NXOAuth2AccountStore sharedStore];
    NSDictionary* params = [self convertParamsToDictionary:searchString];

    NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

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
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


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

```

このメソッドについて詳しく見ていきましょう。

このコードの核は、先ほど settings.plist ファイルの中で既に定義したパラメーターを受け取る `NXOAuth2Request`メソッドです。

まず、Graph API の呼び出しを正しく記述しましょう。 呼び出しの対象となる `/users`を Graph API リソースとバージョンの後に付加する形で指定します。 これらは API の進化に伴って変わる可能性があるので、外部の設定ファイルに記述するのが妥当です。

```objc
NSString *graphURL = [NSString stringWithFormat:@"%@%@/users", data.graphApiUrlString, data.apiversion];
```

次に、Graph API 呼び出しに渡すパラメーターをもう一つ指定する必要があります。 これらのパラメーターはリソース エンドポイントに挿入しないでください。URI に使用できない文字は実行時に除去されるため、このことは*きわめて重要*です。 クエリ コードはすべてパラメーターに指定する必要があります。

```objc

NSDictionary* params = [self convertParamsToDictionary:searchString];
```

お気付きのように、 `convertParamsToDictionary` というまだ作成していないメソッドが呼び出されています。 このメソッドをファイルの最後に作成しましょう。

```objc
+(NSDictionary*) convertParamsToDictionary:(NSString*)searchString
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

        NSString *query = [NSString stringWithFormat:@"startswith(givenName, '%@')", searchString];

           [dictionary setValue:query forKey:@"$filter"];



    return dictionary;
}

```
次に、 `NXOAuth2Request` メソッドを使用して API から JSON 形式でデータを取得します。

```objc
NSArray *accounts = [store accountsWithAccountType:@"myGraphService"];
    [NXOAuth2Request performMethod:@"GET"
                        onResource:[NSURL URLWithString:graphURL]
                   usingParameters:params
                       withAccount:accounts[0]
               sendProgressHandler:^(unsigned long long bytesSend, unsigned long long bytesTotal) {
                   // e.g., update a progress indicator
               }
                   responseHandler:^(NSURLResponse *response, NSData *responseData, NSError *error) {
                       // Process the response
                       if (responseData) {
                           NSError *error;
                           NSDictionary *dataReturned = [NSJSONSerialization JSONObjectWithData:responseData options:0 error:nil];
                           NSLog(@"Graph Response was: %@", dataReturned);

                           // We can grab the top most JSON node to get our graph data.
                           NSArray *graphDataArray = [dataReturned objectForKey:@"value"];
```

最後に、MasterViewController にデータが返されるときの動作について見ていきましょう。 データはシリアル化された状態で返されるので、逆シリアル化したうえでオブジェクトに読み込んで MainViewController に渡す必要があります。 そこで、User オブジェクトを作成する `User.m/h` ファイルがスケルトンに用意されています。 Graph から返された情報は、この User オブジェクトに追加することになります。

```objc
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
                               s.name =[keyValuePairs valueForKey:@"displayName"];
                               s.mail =[keyValuePairs valueForKey:@"mail"];
                               s.businessPhones =[keyValuePairs valueForKey:@"businessPhones"];
                               s.mobilePhones =[keyValuePairs valueForKey:@"mobilePhone"];


                               [Users addObject:s];
```


## <a name="run-the-sample"></a>サンプルの実行
スケルトンを使用した場合、またはチュートリアルに従って作成されていれば、アプリケーションは正しく実行されると思います。 シミュレーターを起動し、 **サインイン** ボタンをクリックしてアプリケーションを使用してみましょう。

## <a name="get-security-updates-for-our-product"></a>Microsoft 製品のセキュリティ更新プログラムの取得
セキュリティの問題が発生したときに通知を受け取ることをお勧めします。[Security TechCenter](https://technet.microsoft.com/security/dd252948) にアクセスし、セキュリティ アドバイザリ通知の受信登録を行ってください。




<!--HONumber=Jan17_HO4-->


