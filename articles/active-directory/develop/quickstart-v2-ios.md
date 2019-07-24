---
title: Microsoft ID プラットフォーム iOS のクイック スタート | Azure
description: iOS ネイティブ アプリケーションでユーザーにサインインし、Microsoft Graph に対してクエリを実行する方法を説明します。
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: dadobali
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6340e0f349d66ecf6baaca481722396a6d786c5
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496131"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-an-ios-native-app"></a>クイック スタート:iOS ネイティブ アプリからユーザーにサインインし、Microsoft Graph API を呼び出す

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

このクイック スタートには、ネイティブ iOS アプリケーションから個人や仕事、学校のアカウントへのサインイン、アクセス トークンの取得、Microsoft Graph API の呼び出しを行う方法を示すコード サンプルが含まれています。

![このクイック スタートで生成されたサンプル アプリの動作の紹介](media/quickstart-v2-ios/ios-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download"></a>登録とダウンロード
> ### <a name="register-and-configure-your-application-and-code-sample"></a>アプリケーションとサンプル コードの登録と構成
> #### <a name="step-1-register-your-application"></a>手順 1:アプリケーションの登録
> アプリケーションを登録し、ソリューションにアプリケーション登録情報を追加するには、次の手順を実行します。
> 1. [Microsoft アプリケーション登録ポータル](https://apps.dev.microsoft.com/portal/register-app)に移動して、アプリケーションを登録します。
> 1. **[アプリケーション名]** ボックスに、アプリケーションの名前を入力します。
> 1. **[Guided Setup]\(ガイド付きセットアップ\)** チェック ボックスがオフになっていることを確認し、**[作成]** を選択します。
> 1. **[プラットフォームの追加]**、**[ネイティブ アプリケーション]**、**[保存]** の順に選択します。

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>手順 1:アプリケーションの作成
> このクイック スタートのコード サンプルを動作させるには、応答 URL として `msal<AppId>://auth` (msal\<AppId> はこのアプリケーション ID) を追加する必要があります。
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [この変更を行う]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![構成済み](media/quickstart-v2-ios/green-check.png) ご自分のアプリケーションはこの属性で構成されています

#### <a name="step-2-download-your-web-server-or-project"></a>手順 2:Web サーバーまたはプロジェクトのダウンロード

- [XCode プロジェクトのダウンロード](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip)

#### <a name="step-3-configure-your-project"></a>手順 3:プロジェクトを構成する

1. zip ファイルを解凍し、XCode でプロジェクトを開きます。
1. **ViewController.swift** を編集し、'let kClientID' で始まる行を次のコード スニペットで置換します。

    > [!div renderon="portal" class="sxs-lookup"]
    > ```swift
    > let kClientID = "Enter_the_Application_Id_here"
    > ```

    > [!div renderon="docs"]
    > ```swift
    > let kClientID = "<ENTER_THE_APPLICATION_ID_HERE>"
    > ```   
1. Control を押しながら **Info.plist** をクリックしてコンテキスト メニューを表示し、**[Open As** > **Source Code]** を選択します。
1. ルート ノード dict の下に次のコードを追加します。

    > [!div renderon="portal" class="sxs-lookup"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msalEnter_the_Application_Id_here</string>
    >         </array>
    >     </dict>
    > </array>
    > ```

    > [!div renderon="docs"]
    > ```xml
    > <key>CFBundleURLTypes</key>
    > <array>
    >     <dict>
    >         <key>CFBundleTypeRole</key>
    >         <string>Editor</string>
    >         <key>CFBundleURLName</key>
    >         <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
    >         <key>CFBundleURLSchemes</key>
    >         <array>
    >             <string>msal<ENTER_THE_APPLICATION_ID_HERE></string>
    >         </array>
    >     </dict>
    > </array>
    > ```
    
> [!div renderon="docs"]
> <span>5.</span>`<ENTER_THE_APPLICATION_ID_HERE>` をアプリケーションの *Application ID* で置換します。 *Application ID* を検索する必要がある場合は、*[Overview]\(概要\)* ページに移動します。

## <a name="more-information"></a>詳細情報

このクイック スタートの詳細については、以下のセクションをお読みください。

### <a name="msal"></a>MSAL

MSAL ([MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)) はユーザーにサインインし、Microsoft Azure Active Directory によって保護されている API にアクセスするトークンを要求するために使用するライブラリです。 MSAL は、次のプロセスを使用してアプリケーションに追加できます。

```
$ vi Podfile
```
このポッドファイルに次のコードを追加します。

```
 target 'QuickStart' do
   use_frameworks!
 pod 'MSAL'
 end
```

### <a name="msal-initialization"></a>MSAL の初期化

MSAL への参照を追加するには、次のコードを追加します。

```swift
import MSAL
```

続いて、次のコードを使用して MSAL を初期化します。

```swift
let authority = MSALAuthority(url: URL(string: kAuthority)!)
self.applicationContext = try MSALPublicClientApplication(clientId: kClientID, authority: authority)
```

> |各値の説明: ||
> |---------|---------|
> | `clientId` | *portal.azure.com* に登録されているアプリケーションの Application ID |
> | `authority` | Microsoft ID プラットフォーム エンドポイント。 ほとんどの場合は *https<span/>://login.microsoftonline.com/common* |

### <a name="requesting-tokens"></a>トークンの要求

MSAL には、トークンの取得に使用する 2 つのメソッド `acquireToken`、`acquireTokenSilent` があります。

#### <a name="getting-an-access-token-interactively"></a>アクセス トークンを対話形式で取得する

ユーザーは Microsoft ID プラットフォーム エンドポイントの操作を強制される場合があります。その場合、コンテキストがシステム ブラウザーに切り替わり、ユーザーの資格情報の検証または同意が求められます。 次に例をいくつか示します。

* ユーザーが初めてアプリケーションにサインインした場合
* パスワードの有効期限が切れているため、ユーザーが資格情報を再入力する必要がある場合
* ご使用のアプリケーションが、ユーザーによる同意が必要なリソースへのアクセスを要求している場合
* 2 要素認証が必須である場合

```swift
applicationContext.acquireToken(forScopes: self.kScopes) { (result, error) in /* Add your handling logic */}
```

> |各値の説明:||
> |---------|---------|
> | `forScopes` | 要求するスコープを含む (つまり、Microsoft Graph 用の `[ "user.read" ]` またはカスタム Web API 用の `[ "<Application ID URL>/scope" ]` (つまり、`api://<Application ID>/access_as_user`)) |

#### <a name="getting-an-access-token-silently"></a>アクセス トークンを自動的に取得する

リソースへのアクセスが必要になるたびに、ユーザーに自分の資格情報を検証させたくない場合があります。 ほとんどの場合は、ユーザーの操作なしにトークンの取得や更新を行います。 最初に `acquireToken` メソッドを呼び出した後は、`acquireTokenSilent` メソッドを使用して保護されたリソースにアクセス するトークンを取得することができます。

```swift
applicationContext.acquireTokenSilent(forScopes: self.kScopes, account: applicationContext.allAccounts().first) { (result, error) in /* Add your handling logic */}
```

> |各値の説明: ||
> |---------|---------|
> | `forScopes` | 要求するスコープを含む (つまり、Microsoft Graph 用の `[ "user.read" ]` またはカスタム Web API 用の `[ "<Application ID URL>/scope" ]` (つまり、`api://<Application ID>/access_as_user`)) |
> | `account` | トークンを要求しているアカウント (MSAL は、1 つのアプリで複数のアカウントをサポート)。 このクイック スタートの場合、値はキャッシュ内の最初のアカウントを指す (`applicationContext.allAccounts().first`)。 |

## <a name="next-steps"></a>次の手順

アプリケーションや新機能の構築についての完全なステップ バイ ステップ ガイドは、iOS チュートリアルをお試しください。このクイック スタートの完全な説明も含まれています。

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>このクイック スタートで使用されているアプリケーションを作成する手順

> [!div class="nextstepaction"]
> [Graph API 呼び出し iOS チュートリアル](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-ios)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
