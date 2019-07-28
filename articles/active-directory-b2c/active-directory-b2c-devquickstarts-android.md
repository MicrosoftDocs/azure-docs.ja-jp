---
title: Azure Active Directory B2C で Android アプリケーションを使用してトークンを取得する | Microsoft Docs
description: この記事では、Azure Active Directory B2C と AppAuth を使用してユーザー ID の管理とユーザーの認証を行う Android アプリを作成する方法を説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 4db4806b6be018bfc53a155627de825bf62d8395
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510110"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で Android アプリケーションを使用してサインインする

Microsoft の ID プラットフォームには、OAuth2 や OpenID Connect といったオープンな標準が使用されています。 これらの標準により、Azure Active Directory B2C と統合する任意のライブラリを活用できます。 他のライブラリを使用できるように、このようなチュートリアルを使用して、サード パーティのライブラリから Microsoft の ID プラットフォームに接続するための構成方法を示すことができます。 Microsoft の ID プラットフォームには、[RFC6749 OAuth2 仕様](https://tools.ietf.org/html/rfc6749)を実装するほとんどのライブラリから接続できます。

> [!WARNING]
> Microsoft では、サード パーティ製のライブラリ用の修正プログラムは提供していません。また、これらのライブラリのレビューも実施していません。 このサンプルでは、Azure AD B2C を使用する基本的なシナリオでの互換性がテスト済みである、AppAuth と呼ばれるサード パーティ製のライブラリを使用しています。 問題や機能に関する要望は、ライブラリのオープン ソース プロジェクトにお送りください。 詳細については、[こちらの記事](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries)をご覧ください。  
>
>

ここで紹介する構成サンプルは OAuth2 や OpenID Connect に精通している読者を想定しており、それ以外の方にとっては、あまり参考にならない可能性があります。 その場合は、 [対応プロトコルについて簡単に解説したこちらの記事](active-directory-b2c-reference-protocols.md)に目を通すことをお勧めします。

## <a name="get-an-azure-ad-b2c-directory"></a>Azure AD B2C ディレクトリの取得

Azure AD B2C を使用するには、ディレクトリ (つまり、テナント) を作成しておく必要があります。 ディレクトリは、ユーザー、アプリ、グループなどをすべて格納するためのコンテナーです。 まだディレクトリを作成していない場合は、 [B2C ディレクトリを作成](tutorial-create-tenant.md) してから先に進んでください。

## <a name="create-an-application"></a>アプリケーションの作成

次に、B2C ディレクトリにアプリを作成する必要があります。 これにより、アプリと安全に通信するために必要な情報を Azure AD に提供します。 モバイル アプリを作成するには、[こちらの手順](active-directory-b2c-app-registration.md)に従ってください。 次を行ってください。

* アプリケーションに**ネイティブ クライアント**を含めます。
* アプリに割り当てられた **アプリケーション ID** をコピーしておきます。 この情報は後で必要になります。
* ネイティブ クライアントの**リダイレクト URI** (例: com.onmicrosoft.fabrikamb2c.exampleapp://oauth/redirect) を設定します。 この情報も後で必要になります。

## <a name="create-your-user-flows"></a>ユーザー フローを作成する

Azure AD B2C では、すべてのユーザー エクスペリエンスが [ユーザー フロー](active-directory-b2c-reference-policies.md)によって定義されます。これは、Azure AD の動作を制御するポリシーのセットです。 このアプリケーションには、サインインとサインアップのユーザー フローが必要です。 ユーザー フローを作成するときは、必ず次のようにします。

* ユーザー フローのサインアップ属性として **[表示名]** を選択します。
* すべてのユーザー フローで、アプリケーション要求として **[表示名]** と **[オブジェクト ID]** を選択します。 その他のクレームも選択できます。
* ユーザー フローの作成後、各ユーザー フローの **[名前]** をコピーしておきます。 名前には、 `b2c_1_`というプレフィックスが付加されています。  このユーザー フロー名は後で必要になります。

ユーザー フローを作成したら、アプリを構築できます。

## <a name="download-the-sample-code"></a>サンプル コードのダウンロード

AppAuth と Azure AD B2C を使用する実稼働するサンプルが [github](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c) に用意されています。 そのコードをダウンロードして実行できます。 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) の説明に従って、独自の Azure AD B2C 構成を使用してアプリを開始できます。

サンプルは、[AppAuth](https://openid.github.io/AppAuth-Android/) によって提供されたサンプルを変更したものです。 AppAuth とその機能の詳細については、AppAuth のページを参照してください。

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Azure AD と B2C AppAuth を使用するようにアプリを変更する

> [!NOTE]
> AppAuth は Android API 16 (Jellybean) 以降をサポートします。 API 23 以降の使用をお勧めします。
>

### <a name="configuration"></a>構成

Azure AD B2C との通信は、探索 URI を指定するか、承認エンドポイント URI とトークン エンドポイント URI の両方を指定することで構成できます。 どちらの場合でも、次の情報を指定する必要があります。

* テナント ID (例: contoso.onmicrosoft.com)
* ユーザー フロー名 (例: B2C\_1\_SignUpIn)

承認 URI とトークン エンドポイント URI を自動的に検出することを選択した場合は、探索 URI から情報を取得する必要があります。 探索 URI は、次の URL の Tenant\_ID と Policy\_Name を置き換えることで生成できます。

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

承認 URI とトークン エンドポイント URI を取得し、次を実行することで AuthorizationServiceConfiguration オブジェクトを作成できます。

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

探索を使用して承認 URI とトークン エンドポイント URI を取得する代わりに、次の URL のTenant\_ID と Policy\_Name を置き換えることで、それらを明示的に指定することもできます。

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

AuthorizationServiceConfiguration オブジェクトを作成するには、次のコードを実行します。

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>承認

承認サービスの構成を行うか取得した後、承認要求を構築できます。 この要求を作成するには、次の情報が必要です。

* クライアント ID (例: 00000000-0000-0000-0000-000000000000)
* カスタム スキーマを使用するリダイレクト URI (例: com.onmicrosoft.fabrikamb2c.exampleapp://oauthredirect)

どちらの項目も、[アプリの登録](#create-an-application)を行ったときに保存されています。

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

残りのプロセスを完了する方法については、[AppAuth ガイド](https://openid.github.io/AppAuth-Android/)を参照してください。 動作するアプリをすぐに開始する必要がある場合は、[用意されているサンプル](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c)をチェックしてください。 [README.md](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) の手順に従って、独自の Azure AD B2C 構成を入力してください。

