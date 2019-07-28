---
title: Azure Active Directory B2C の iOS アプリケーションで AppAuth を使用する | Microsoft Docs
description: この記事では、Azure Active Directory B2C と AppAuth を使用してユーザー ID の管理とユーザーの認証を行う iOS アプリを作成する方法を説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 1f7c864102a4985aa1b2c66e12b42cbe3bc19bca
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510099"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C:iOS アプリケーションを使用してサインインする

Microsoft の ID プラットフォームには、OAuth2 や OpenID Connect といったオープンな標準が使用されています。 オープンな標準プロトコルを使用すると、開発者がサービスに統合するライブラリを選択する際の選択肢が増えます。 このチュートリアルも、他のチュートリアルと同じように、開発者が、Microsoft ID プラットフォームに接続するアプリケーションの記述を支援するために提供されています。 Microsoft の ID プラットフォームには、[RFC6749 OAuth2 仕様](https://tools.ietf.org/html/rfc6749)を実装するほとんどのライブラリから接続できます。

> [!WARNING]
> Microsoft では、サード パーティ製のライブラリ用の修正プログラムは提供していません。また、これらのライブラリのレビューも実施していません。 このサンプルでは、Azure AD B2C を使用する基本的なシナリオでの互換性がテスト済みである、AppAuth と呼ばれるサード パーティ製のライブラリを使用しています。 問題や機能に関する要望は、ライブラリのオープン ソース プロジェクトにお送りください。 詳細については、 [こちらの記事](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)を参照してください。
>
>

ここで紹介する構成サンプルは OAuth2 や OpenID Connect に精通している読者を想定しており、それ以外の方にとっては、あまり参考にならない可能性があります。 その場合は、 [対応プロトコルについて簡単に解説したこちらの記事](active-directory-b2c-reference-protocols.md)に目を通すことをお勧めします。

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの取得
Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。 ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、 [B2C ディレクトリを作成](tutorial-create-tenant.md) してから先に進んでください。

## <a name="create-an-application"></a>アプリケーションの作成
次に、B2C ディレクトリにアプリを作成する必要があります。 アプリを登録すると、Azure AD がアプリと安全に通信するために必要な情報が提供されます。 モバイル アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従ってください。 次を行ってください。

* アプリケーションに**ネイティブ クライアント**を含めます。
* アプリに割り当てられた **アプリケーション ID** をコピーしておきます。 この GUID は後で必要になります。
* カスタム スキーマを使用する**リダイレクト URI** を設定します (例: com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)。 この URI は後で必要になります。

## <a name="create-your-user-flows"></a>ユーザー フローを作成する
Azure AD B2C では、すべてのユーザー エクスペリエンスが[ユーザー フロー](active-directory-b2c-reference-policies.md)によって定義されます。 このアプリケーションには、サインインとサインアップを組み合わせた 1 つの ID エクスペリエンスが含まれています。 ユーザー フローを作成するときは、必ず次のようにします。

* **[サインアップ属性]** で、属性 **[表示名]** を選択します。  他の属性を選択することもできます。
* **[アプリケーション クレーム]** で、要求の**表示名**と**ユーザーのオブジェクト ID** を選択します。 他の要求を選択することもできます。
* ユーザー フローの作成後、各ユーザー フローの **[名前]** をコピーしておきます。 ユーザー フローを保存するときに、ユーザー フロー名の先頭に `b2c_1_` が付加されます。  ユーザー フロー名は後で必要になります。

ユーザー フローを作成したら、アプリを構築できます。

## <a name="download-the-sample-code"></a>サンプル コードのダウンロード
AppAuth と Azure AD B2C を使用する実稼働するサンプルが [github](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c) に用意されています。 そのコードをダウンロードして実行できます。 独自の Azure AD B2C テナントを使用するには、[README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) の指示に従います。

このサンプルは、[GitHub の iOS AppAuth プロジェクト](https://github.com/openid/AppAuth-iOS)の README の指示に従って作成されています。 サンプルとライブラリの動作の詳細については、GitHub の AppAuth README を参照します。

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Azure AD と B2C AppAuth を使用するようにアプリを変更する

> [!NOTE]
> AppAuth は、サポート iOS 7 以降をサポートします。  ただし、Google でのソーシャル ログインをサポートするには SFSafariViewController が必要であり、iOS 9 以降が必要です。
>

### <a name="configuration"></a>構成

Azure AD B2C との通信は、承認エンドポイント URI とトークン エンドポイント URI の両方を指定することで構成できます。  これらの URI を生成するには、次の情報が必要です。
* テナント ID (例: contoso.onmicrosoft.com)
* ユーザー フロー名 (例: B2C\_1\_SignUpIn)

トークン エンドポイント URI は、次の URL の Tenant\_ID と Policy\_Name を置き換えることで生成できます。

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

承認エンドポイント URI は、次の URL の Tenant\_ID と Policy\_Name を置き換えることで生成できます。

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

AuthorizationServiceConfiguration オブジェクトを作成するには、次のコードを実行します。

```objc
OIDServiceConfiguration *configuration = 
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>承認

承認サービスの構成を行うか取得した後、承認要求を構築できます。 この要求を作成するには、次の情報が必要です。  
* クライアント ID (例: 00000000-0000-0000-0000-000000000000)
* カスタム スキーマを使用するリダイレクト URI (例: com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect)

どちらの項目も、[アプリの登録](#create-an-application)を行ったときに保存されています。

```objc
OIDAuthorizationRequest *request = 
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow = 
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

カスタム スキームを使用する URI へのリダイレクトを処理するようにアプリケーションを設定するには、Info.pList の 'URL スキーム' の一覧をを更新する必要があります。
* Info.pList を開きます。
* 'Bundle OS Type Code' のような行の上にマウス ポインタ―を置き、\+ 記号をクリックします。
* 新しい行の 'URL types' の名前を変更します。
* 'URL types' の左側にある矢印をクリックしてツリーを開きます。
* 'Item 0' の左側にある矢印をクリックしてツリーを開きます。
* Item 0 の下の最初の項目の名前を 'URL Schemes' に変更します。
* 'URL Schemes' の左側にある矢印をクリックしてツリーを開きます。
* 'Value' 列には、 'URL Schemes' の下の 'Item 0' の左側に空のフィールドがあります。  アプリケーションの一意のスキームの値を設定します。  値は、OIDAuthorizationRequest オブジェクトの作成時に redirectURL で使用するスキームと一致させる必要があります。  この例では、"com.onmicrosoft.fabrikamb2c.exampleapp" スキームを使用しています。

残りのプロセスを完了する方法については、[AppAuth ガイド](https://openid.github.io/AppAuth-iOS/)を参照してください。 動作するアプリをすぐに開始する必要がある場合は、[サンプル](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c)をチェックしてください。 [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) の手順に従って、独自の Azure AD B2C 構成を入力してください。

ご意見とご提案をお待ちしております。 この記事に問題がある場合、またはこのコンテンツを改善するためのご提案がある場合には、ページの下部でフィードバックを送信できます。 機能についてのご要望は、[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c) までお寄せください。
