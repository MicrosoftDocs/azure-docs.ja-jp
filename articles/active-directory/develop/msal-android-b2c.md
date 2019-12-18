---
title: Azure AD B2C (MSAL Android) | Azure
titleSuffix: Microsoft identity platform
description: Android 用 Microsoft Authentication Library (MSAL.Android) で Azure AD B2C を使用する場合の固有の考慮事項について説明します
services: active-directory
author: brianmel
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 9/18/2019
ms.author: brianmel
ms.reviewer: rapong
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98a31ea2daffba19242e73362af5a44e3a392342
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917116"
---
# <a name="use-msal-for-android-with-b2c"></a>Android 用 MSAL と B2C を一緒に使用する

Microsoft Authentication Library (MSAL) を使用すると、アプリケーション開発者は、[Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/) を使用してソーシャル ID およびローカル ID でユーザーを認証できます。 Azure AD B2C は、ID 管理サービスです。 それを使用すると、貴社のアプリケーションを顧客が使用するときに、顧客がサインアップ、サインイン、およびプロファイル管理を行う方法をカスタマイズおよび制御することができます。

## <a name="configure-known-authorities-and-redirect-uri"></a>既知の機関とリダイレクト URI を構成する

Android 用 MSAL では、B2C ポリシー (ユーザー体験) は個々の機関として構成されます。

次の 2 つのポリシーを持つ B2C アプリケーションがあるとします。
- サインアップ/サインイン
    * `B2C_1_SISOPolicy` と呼ばれます
- プロファイルの編集
    * `B2C_1_EditProfile` と呼ばれます

アプリ用の構成ファイルの場合、2 つの `authorities` が宣言されます。 ポリシーごとに 1 つです。 各機関の `type` プロパティは `B2C` です。

### `app/src/main/res/raw/msal_config.json`
```json
{
    "client_id": "<your_client_id_here>",
    "redirect_uri": "<your_redirect_uri_here>",
    "authorities": [{
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/",
            "default": true
        },
        {
            "type": "B2C",
            "authority_url": "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_EditProfile/"
        }
    ]
}
```

`redirect_uri` は、アプリ構成で登録する必要があります。さらに、[承認コード付与フロー](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-oauth-code)中にリダイレクトをサポートするために、`AndroidManifest.xml` に登録する必要があります。

## <a name="initialize-ipublicclientapplication"></a>IPublicClientApplication を初期化する

`IPublicClientApplication` は、アプリケーション構成を非同期的に解析できるように、ファクトリ メソッドによって構築されます。

```java
PublicClientApplication.createMultipleAccountPublicClientApplication(
    context, // Your application Context
    R.raw.msal_config, // Id of app JSON config
    new IPublicClientApplication.ApplicationCreatedListener() {
        @Override
        public void onCreated(IMultipleAccountPublicClientApplication pca) {
            // Application has been initialized.
        }

        @Override
        public void onError(MsalException exception) {
            // Application could not be created.
            // Check Exception message for details.
        }
    }
);
```

## <a name="interactively-acquire-a-token"></a>トークンを対話形式で取得する

MSAL を使用して対話形式でトークンを取得するには、`AcquireTokenParameters` インスタンスを作成し、それを `acquireToken` メソッドに指定します。 以下のトークン要求では、`default` の機関が使用されています。

```java
IMultipleAccountPublicClientApplication pca = ...; // Initialization not shown

AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccessful, inspect the exception
        }

        @Override
        public void onCancel() {
            // The user cancelled the flow
        }
    }).build();

pca.acquireToken(parameters);
```

## <a name="silently-renew-a-token"></a>トークンをサイレント更新する

MSAL を使用してトークンをサイレントで取得するには、`AcquireTokenSilentParameters` インスタンスを作成し、それを `acquireTokenSilentAsync` メソッドに指定します。 `acquireToken` メソッドとは異なり、トークンをサイレントで取得するには、`authority` を指定する必要があります。

```java
IMultilpeAccountPublicClientApplication pca = ...; // Initialization not shown
AcquireTokenSilentParameters parameters = new AcquireTokenSilentParameters.Builder()
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .forAccount(account)
    // Select a configured authority (policy), mandatory for silent auth requests
    .fromAuthority("https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_SISOPolicy/")
    .callback(new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            // Token request was successful, inspect the result
        }

        @Override
        public void onError(MsalException exception) {
            // Token request was unsuccesful, inspect the exception
        }
    })
    .build();

pca.acquireTokenSilentAsync(parameters);
```

## <a name="specify-a-policy"></a>ポリシーを指定する

B2C のポリシーは個別の機関として表されるため、既定値以外のポリシー呼び出しを実現するには、`acquireToken` または `acquireTokenSilent` パラメーターを構築するときに `fromAuthority` 句を指定します。  例:

```java
AcquireTokenParameters parameters = new AcquireTokenParameters.Builder()
    .startAuthorizationFromActivity(activity)
    .withScopes(Arrays.asList("https://contoso.onmicrosoft.com/contosob2c/read")) // Provide your registered scope here
    .withPrompt(Prompt.LOGIN)
    .callback(...) // provide callback here
    .fromAuthority("<url_of_policy_defined_in_configuration_json>")
    .build();
```

## <a name="handle-password-change-policies"></a>パスワード変更ポリシーを処理する

ローカル アカウントのサインアップまたはサインイン ユーザー フローでは、"**パスワードを忘れた場合**" のリンクをクリックするように促します。 このリンクをクリックしても、パスワード リセット ユーザー フローは自動的にはトリガーされません。

代わりに、エラー コード `AADB2C90118` がご利用のアプリに返されます。 アプリケーションでは、パスワードをリセットする特定のユーザー フローを実行して、このエラー コードを処理する必要があります。

パスワード リセット エラー コードをキャッチするには、ご利用の `AuthenticationCallback` の内部で次の実装を使用します。

```java
new AuthenticationCallback() {

    @Override
    public void onSuccess(IAuthenticationResult authenticationResult) {
        // ..
    }

    @Override
    public void onError(MsalException exception) {
        final String B2C_PASSWORD_CHANGE = "AADB2C90118";

        if (exception.getMessage().contains(B2C_PASSWORD_CHANGE)) {
            // invoke password reset flow
        }
    }

    @Override
    public void onCancel() {
        // ..
    }
}
```

## <a name="use-iauthenticationresult"></a>IAuthenticationResult を使用する

トークンの取得が成功すると、`IAuthenticationResult` オブジェクトが作成されます。 これには、アクセス トークン、ユーザー要求、およびメタデータが含まれます。

### <a name="get-the-access-token-and-related-properties"></a>アクセス トークンと関連するプロパティを取得する

```java
// Get the raw bearer token
String accessToken = authenticationResult.getAccessToken();

// Get the scopes included in the access token
String[] accessTokenScopes = authenticationResult.getScope();

// Gets the access token's expiry
Date expiry = authenticationResult.getExpiresOn();

// Get the tenant for which this access token was issued
String tenantId = authenticationResult.getTenantId();
```

### <a name="get-the-authorized-account"></a>承認されたアカウントを取得する

```java
// Get the account from the result
IAccount account = authenticationResult.getAccount();

// Get the id of this account - note for B2C, the policy name is a part of the id
String id = account.getId();

// Get the IdToken Claims
//
// For more information about B2C token claims, see reference documentation
// https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens
Map<String, ?> claims = account.getClaims();

// Get the 'preferred_username' claim through a convenience function
String username = account.getUsername();

// Get the tenant id (tid) claim through a convenience function
String tenantId = account.getTenantId();
```

### <a name="idtoken-claims"></a>IdToken 要求

IdToken で返される要求は、MSAL ではなく、セキュリティ トークン サービス (STS) によって設定されます。 使用する ID プロバイダー (IdP) によっては、一部の要求が存在しない場合があります。 現在、一部の IdP では `preferred_username` 要求が提供されていません。 この要求は MSAL によってキャッシュに使用されるため、プレースホルダーの値 (`MISSING FROM THE TOKEN RESPONSE`) が代わりに使用されます。 B2C IdToken 要求の詳細については、「[Azure Active Directory B2C のトークンの概要](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#claims)」を参照してください。

## <a name="managing-accounts-and-policies"></a>アカウントとポリシーの管理

B2C では、各ポリシーが個別の機関として扱われます。 したがって、各ポリシーから返されたアクセストークン、更新トークン、および ID トークンは交換できません。 つまり、各ポリシーからは、他のポリシーの呼び出しに使用できないトークンを持つ個別の `IAccount` オブジェクトが返されます。

各ポリシーでは、各ユーザーのキャッシュに `IAccount` が追加されます。 ユーザーは、アプリケーションにサインインし、2 つのポリシーを呼び出すと、2 つの `IAccount` を持つことになります。 このユーザーをキャッシュから削除するには、ポリシーごとに `removeAccount()` を呼び出す必要があります。

`acquireTokenSilent` を使用してポリシーのトークンを更新する場合は、ポリシーの前の呼び出しで返されたのと同じ `IAccount` を `AcquireTokenSilentParameters` に指定します。 別のポリシーによって返されたアカウントを指定すると、エラーが発生します。

## <a name="next-steps"></a>次の手順

Azure Active Directory B2C (Azure AD B2C) の詳細については、「[Azure Active Directory B2C とは](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)」を参照してください。
