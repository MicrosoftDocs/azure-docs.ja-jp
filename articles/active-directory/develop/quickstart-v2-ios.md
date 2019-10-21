---
title: Microsoft ID プラットフォームでの iOS と macOS のクイック スタート | Azure
description: iOS または macOS アプリケーションでユーザーのサインインを行い、Microsoft Graph に対してクエリを実行する方法を説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2019
ms.author: twhitney
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdb1ae92b17bef281e3b82a8c1a21344fa37f0da
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345538"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-or-macos-app"></a>クイック スタート:iOS または macOS アプリからユーザーのサインインを行い、Microsoft Graph API を呼び出す

このクイック スタートには、ネイティブの iOS または macOS アプリケーションから個人や仕事、学校のアカウントへのサインイン、アクセス トークンの取得、および Microsoft Graph API の呼び出しを行う方法を示すコード サンプルが含まれています。

このクイック スタートは、iOS アプリと macOS アプリの両方に適用されます。 一部の手順は、iOS アプリにのみ必要です。 それらの手順では、iOS のみが対象であることが示されています。

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-ios/ios-intro.svg)

> [!NOTE]
> **前提条件**
> * XCode 10 以降
> * iOS 10 以降 
> * macOS 10.12 以降

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>クイック スタート アプリを登録してダウンロードする
> クイック スタート アプリケーションを開始する方法としては、次の 2 つの選択肢があります。
> * [簡易] [選択肢 1: アプリを登録して自動構成を行った後、コード サンプルをダウンロードする](#option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample)
> * [手動] [選択肢 2: アプリケーションを登録し、コード サンプルを手動で構成する](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-the-code-sample"></a>オプション 1:アプリを登録して自動構成を行った後、コード サンプルをダウンロードする
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
> アプリを登録するには、
> 1. 新しい [Azure portal の [アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/IosQuickstartPage/sourceType/docs) ウィンドウに移動します。
> 1. アプリケーションの名前を入力し、 **[登録]** を選択します。
> 1. 画面の指示に従ってダウンロードし、1 回クリックするだけで、新しいアプリケーションが自動的に構成されます。
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>オプション 2:アプリケーションを登録し、アプリケーションとコード サンプルを手動で構成する
>
> #### <a name="step-1-register-your-application"></a>手順 1: アプリケーションの登録
> アプリケーションを登録し、その登録情報をソリューションに手動で追加するには、次の手順を実行します。
>
> 1. 開発者用の Microsoft ID プラットフォームの [[アプリの登録]](https://aka.ms/MobileAppReg) ページに移動します。
> 1. **[新規登録]** を選択します。
> 1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
>      - **[名前]** セクションに、アプリのユーザーがアプリにサインインまたは同意したときにユーザーに表示されるわかりやすいアプリケーション名を入力します。
>      - このページの他の設定はスキップします。
>      - [`Register`] を選択します。
> 1. **[管理]** セクションで、[`Authentication`] > [`Add Platform`] > [`iOS`] を選択します。
>      - アプリケーションの***バンドル ID*** を入力します。 バンドル ID は、アプリケーションを一意に識別する一意の文字列 (例: `com.<yourname>.identitysample.MSALMacOS`) にすぎません。 使用する値を書き留めておきます。
>      - iOS の構成は macOS アプリケーションにも適用できることに注意してください。
> 1. `Configure` を選択し、このクイック スタートでの後の手順のために "***MSAL 構成***" の詳細を保存しておきます。
> [!div renderon="portal" class="sxs-lookup"]
>
> #### <a name="step-1-configure-your-application"></a>手順 1:アプリケーションの作成
> このクイック スタートのコード サンプルを動作させるには、Auth ブローカーと互換性があるリダイレクト URI を追加する必要があります。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-ios/green-check.png) アプリケーションはこれらの属性で構成されています

#### <a name="step-2-download-the-sample-project"></a>手順 2:サンプル プロジェクトのダウンロード

- [iOS 用のコード サンプルをダウンロードする](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)
- [macOS 用のコード サンプルをダウンロードする](https://github.com/Azure-Samples/active-directory-macOS-swift-native-v2/archive/master.zip)

#### <a name="step-3-install-dependencies"></a>手順 3:依存関係をインストールする

ターミナル ウィンドウで、ダウンロードしたコード サンプルが含まれるフォルダーに移動し、`pod install` を実行して、最新の MSAL ライブラリをインストールします。

#### <a name="step-4-configure-your-project"></a>手順 4:プロジェクトを構成する

> [!div renderon="docs"]
> 上のオプション 1 を選択した場合は、以下の手順を省略できます。 

> [!div renderon="portal" class="sxs-lookup"]
> 1. zip ファイルを解凍し、XCode でプロジェクトを開きます。
> 1. **ViewController.swift** を編集し、'let kClientID' で始まる行を次のコード スニペットに置き換えます。 `kClientID` の値を、このクイック スタートの前の手順でポータルにアプリを登録したときに保存したクライアント ID に必ず更新してください。
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. プロジェクトの設定を開きます。 **[ID]** セクションに、ポータルに入力した**バンドル ID** を入力します。
> 1. iOS のみ: **Info.plist** を右クリックし、 **[形式を指定して開く]**  >  **[ソース コード]** を選択します。
> 1. iOS のみ: dict ルート ノードの下の `CFBundleURLSchemes` を、ポータルに入力した "***バンドル ID***" に置き換えます。
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
>    ```
> 1. アプリをビルドして実行します。
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > このクイックスタートは、Enter_the_Supported_Account_Info_Here をサポートしています。
> [!div renderon="docs"]
>
> 1. zip ファイルを解凍し、XCode でプロジェクトを開きます。
> 1. **ViewController.swift** を編集し、'let kClientID' で始まる行を次のコード スニペットに置き換えます。 `kClientID` の値を、このクイック スタートの前の手順でポータルにアプリを登録したときに保存したクライアント ID に必ず更新してください。
>    ```swift
>    let kClientID = "Enter_the_Application_Id_Here"
>    ```
> 1. プロジェクトの設定を開きます。 **[ID]** セクションに、ポータルに入力した**バンドル ID** を入力します。
> 1. iOS のみ: **Info.plist** を右クリックし、 **[形式を指定して開く]**  >  **[ソース コード]** を選択します。
> 1. iOS のみ: dict ルート ノードの下の `Enter_the_bundle_Id_Here` を、ポータルで使用した "***バンドル ID***" に置き換えます。
>
>    ```xml
>    <key>CFBundleURLTypes</key>
>    <array>
>       <dict>
>          <key>CFBundleURLSchemes</key>
>          <array>
>             <string>msauth.Enter_the_Bundle_Id_Here</string>
>          </array>
>       </dict>
>    </array>
> 
>    ```
> 1. アプリをビルドして実行します。 

## <a name="more-information"></a>詳細情報

このクイック スタートの詳細については、以下のセクションをお読みください。

### <a name="get-msal"></a>MSAL の取得

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) はユーザーをサインインし、Microsoft ID プラットフォームによって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 MSAL は、次のプロセスを使用してアプリケーションに追加できます。

```
$ vi Podfile

```
次の内容を (プロジェクトのターゲットと共に) この podfile に追加します。

```
use_frameworks!

target 'MSALiOS' do
   pod 'MSAL'
end

```

CocoaPods インストール コマンドを実行します。

```pod install```

### <a name="initialize-msal"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```swift
import MSAL
```

続いて、次のコードを使用して MSAL を初期化します。

```swift
let authority = try MSALAADAuthority(url: URL(string: kAuthority)!)
            
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
```

> |各値の説明: ||
> |---------|---------|
> | `clientId` | *portal.azure.com* に登録されているアプリケーションの Application ID |
> | `authority` | Microsoft ID プラットフォーム エンドポイント。 ほとんどの場合は *https<span/>://login.microsoftonline.com/common* |
> | `redirectUri` | アプリケーションのリダイレクト URI。 'nil' を渡すと、既定値またはカスタムのリダイレクト URI を使用できます。 |

### <a name="for-ios-only-additional-app-requirements"></a>iOS のみ: アプリの追加要件

アプリでは、`AppDelegate` 内に次の内容も必要です。 これにより、認証を実行するときに MSAL SDK による Auth ブローカー アプリからのトークン応答の処理が可能になります。

 ```swift
 func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String)
    }

 ```

> [!NOTE]
> iOS 13 以降では、`UIApplicationDelegate` ではなく `UISceneDelegate` を採用する場合は、代わりに `scene:openURLContexts:` のコールバックにこのコードを配置します ([Apple のドキュメント](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)を参照してください)。
> 以前の iOS との互換性を保持するために UISceneDelegate と UIApplicationDelegate の両方をサポートしている場合は、MSAL コールバックを両方の場所に配置する必要があります。

 ```swift
 func scene(_ scene: UIScene, openURLContexts URLContexts: Set<UIOpenURLContext>) {
        
        guard let urlContext = URLContexts.first else {
            return
        }
        
        let url = urlContext.url
        let sourceApp = urlContext.options.sourceApplication
        
        MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApp)
    }
 ```

最後に、アプリでは、`LSApplicationQueriesSchemes` エントリが ***Info.plist*** に `CFBundleURLTypes` と並んで存在している必要があります。 サンプルにはこれが含まれています。 

   ```xml 
   <key>LSApplicationQueriesSchemes</key>
   <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
   </array>
   ```

### <a name="sign-in-users--request-tokens"></a>ユーザーのサインインを行ってトークンを要求する

MSAL には、トークンの取得に使用する 2 つのメソッド `acquireToken`、`acquireTokenSilent` があります。

#### <a name="acquiretoken-get-a-token-interactively"></a>acquireToken: 対話形式でのユーザー トークンの取得

状況によっては、ユーザーが Microsoft ID プラットフォームと対話する必要があります。 このような場合、エンド ユーザーは自分のアカウントを選択する、自分の資格情報を入力する、またはアプリのアクセス許可に同意することを要求される可能性があります。 たとえば、次のように入力します。 

* ユーザーが初めてアプリケーションにサインインした場合
* ユーザーが自分のパスワードをリセットした場合、ユーザーは自分の資格情報を入力する必要がある 
* アプリケーションがリソースへのアクセスを初めて要求している場合
* MFA またはその他の条件付きアクセス ポリシーが必要な場合

```swift
let parameters = MSALInteractiveTokenParameters(scopes: kScopes, webviewParameters: self.webViewParamaters!)
self.applicationContext!.acquireToken(with: parameters) { (result, error) in /* Add your handling logic */}
```

> |各値の説明:||
> |---------|---------|
> | `scopes` | 要求するスコープを含む (つまり、Microsoft Graph 用の `[ "user.read" ]` またはカスタム Web API 用の `[ "<Application ID URL>/scope" ]`) (`api://<Application ID>/access_as_user`) |

#### <a name="acquiretokensilent-get-an-access-token-silently"></a>acquireTokenSilent: アクセス トークンを自動的に取得する

アプリは、トークンを要求するたびに、ユーザーにサインインを要求するべきではありません。 ユーザーが既にサインインしている場合は、この方法により、アプリはトークンを暗黙的に要求できます。 

```swift
guard let account = try self.applicationContext!.allAccounts().first else { return }
        
let silentParams = MSALSilentTokenParameters(scopes: kScopes, account: account)
self.applicationContext!.acquireTokenSilent(with: silentParams) { (result, error) in /* Add your handling logic */}
```

> |各値の説明: ||
> |---------|---------|
> | `scopes` | 要求するスコープを含む (つまり、Microsoft Graph 用の `[ "user.read" ]` またはカスタム Web API 用の `[ "<Application ID URL>/scope" ]`) (`api://<Application ID>/access_as_user`) |
> | `account` | トークンが要求されているアカウント。 このクイックスタートは単一アカウントのアプリケーションです。複数アカウントのアプリを構築する場合は、どのアカウントをトークン要求に使用するかを識別するためのロジックを定義する必要があります `applicationContext.account(forHomeAccountId: self.homeAccountId)` |

## <a name="next-steps"></a>次の手順

アプリケーションの構築についての完全なステップ バイ ステップ ガイドは、iOS チュートリアルをお試しください。このクイック スタートの完全な説明も含まれています。

### <a name="learn-how-to-create-the-application-used-in-this-quickstart"></a>このクイック スタートで使用されているアプリケーションの作成方法を確認する

> [!div class="nextstepaction"]
> [Graph API 呼び出し iOS チュートリアル](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Microsoft ID プラットフォームの改善にご協力ください。 簡単な 2 つの質問からなるアンケートに記入し、ご意見をお聞かせください。

> [!div class="nextstepaction"]
> [Microsoft ID プラットフォームのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
