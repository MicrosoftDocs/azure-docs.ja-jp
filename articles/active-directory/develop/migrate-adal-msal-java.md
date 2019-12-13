---
title: Java 用 ADAL から MSAL への移行ガイド | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory Authentication Library (ADAL) Java アプリを Microsoft Authentication Library (MSAL) に移行する方法について説明します。
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: Java
ms.workload: identity
ms.date: 11/04/2019
ms.author: sagonzal
ms.reviewer: nacanuma, twhitney
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e99cee3f21a4e0088fa97dcbec8fdcfdf982d80
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74917388"
---
# <a name="adal-to-msal-migration-guide-for-java"></a>Java 用 ADAL から MSAL への移行ガイド

この記事では、Microsoft Authentication Library (MSAL) を使用するために、Azure Active Directory Authentication Library (ADAL) を使っているアプリを移行するのに必要な変更について重点的に説明します。

Microsoft Authentication Library for Java (MSAL4J) と Azure AD Authentication Library for Java (ADAL4J) はどちらも、Azure AD エンティティを認証し、Azure AD からのトークンを要求するために使用されます。 これまで、ほとんどの開発者は Azure AD 開発者プラットフォーム (v1.0) を使い、Azure AD Authentication Library (ADAL) を使用してトークンを要求することで、Azure AD ID (職場および学校のアカウント) を認証していました。

MSAL には、次のような利点があります。

- より新しい Microsoft ID プラットフォーム エンドポイントが使用されているため、より広範な Microsoft ID (Azure AD の ID、Microsoft アカウント、ソーシャル アカウントおよびローカル アカウントなど) を、Azure AD Business to Consumer (B2C) を通じて認証できます。
- ユーザーは、最高のシングル サインオン エクスペリエンスを得られます。
- アプリケーションでは、増分同意を有効にできるほか、条件付きアクセスのサポートがより簡単になります。

MSAL for Java は、Microsoft ID プラットフォームで使用することをお勧めする認証ライブラリです。 ADAL4J には新しい機能は実装されません。 今後のすべての取り組みは、MSAL の向上に重点が置かれます。

## <a name="differences"></a>相違点

開発者向け Azure AD (v1.0) エンドポイント (および ADAL4J) を使用している場合は、「[Microsoft ID プラットフォーム (v2.0) エンドポイントの違い](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)」に関するページを読むことをお勧めします。

## <a name="scopes-not-resources"></a>リソースではなくスコープ

ADAL4J はリソースのトークンを取得しますが、MSAL for Java はスコープのトークンを取得します。 多くの MSAL for Java クラスには、スコープ パラメーターが必要です。 このパラメーターは、要求された必要とするアクセス許可とリソースを宣言する文字列のリストです。 スコープの例については、「[Microsoft Graph のスコープ](https://docs.microsoft.com/graph/permissions-reference)」に関するページを参照してください。

## <a name="core-classes"></a>コア クラス

ADAL4J では `AuthenticationContext` クラスは、セキュリティ トークン サービス (STS) または承認サーバーへの、証明機関を介した接続を表します。 しかしながら、MSAL for Java はクライアント アプリケーションを中心に設計されています。 これには、クライアント アプリケーションを表す、`PublicClientApplication` と `ConfidentialClientApplication` という 2 つの異なるクラスが用意されています。  後者の `ConfidentialClientApplication` は、デーモン アプリのアプリケーション ID などのシークレットを、安全に維持するように設計されたアプリケーションを表します。

次の表は、ADAL4J 関数が新しい MSAL for Java 関数にどのようにマップされるかを示しています。

| ADAL4J メソッド| MSAL4J メソッド|
|------|-------|
|acquireToken(String resource, ClientCredential credential, AuthenticationCallback callback) | acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, ClientAssertion assertion, AuthenticationCallback callback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, AsymmetricKeyCredential credential, AuthenticationCallback callback)|acquireToken(ClientCredentialParameters)|
|acquireToken(String resource, String clientId, String username, String password, AuthenticationCallback callback)| acquireToken(UsernamePasswordParameters)|
|acquireToken(String resource, String clientId, String username, String password=null, AuthenticationCallback callback)|acquireToken(IntegratedWindowsAuthenticationParameters)|
|acquireToken(String resource, UserAssertion userAssertion, ClientCredential credential, AuthenticationCallback callback)| acquireToken(OnBehalfOfParameters)|
|acquireTokenByAuthorizationCode() | acquireToken(AuthorizationCodeParameters) |
| acquireDeviceCode() and acquireTokenByDeviceCode()| acquireToken(DeviceCodeParameters)|
|acquireTokenByRefreshToken()| acquireTokenSilently(SilentParameters)|

## <a name="iaccount-instead-of-iuser"></a>IUser の代わりの IAccount

ADAL4J はユーザーをうまく扱っていました。 ユーザーとは、単一の人間またはソフトウェア エージェントを表しますが、ユーザーはMicrosoft ID システム内で、1 つ以上のアカウントを持つことができます。 たとえば、ユーザーが複数の Azure AD、Azure AD B2C、または Microsoft の個人アカウントを持っている場合があります。

MSAL for Java では、`IAccount` インターフェイスを介してアカウントの概念を定義します。 これは ADAL4J からの重大な変更ですが、同じユーザーが複数のアカウントを持つことができ、場合によってはそれが異なる Azure AD ディレクトリに存在する可能性があるという事実を捉えるので、この方法が適しています。 MSAL for Java は、ホーム アカウント情報が提供されるため、ゲスト シナリオでより適切な情報を提供します。

## <a name="cache-persistence"></a>キャッシュの永続化

ADAL4J では、トークンのキャッシュをサポートしていませんでした。
MSAL for Java は、可能な場合に自動的に有効期限切れのトークンを更新し、ユーザーへの不要な資格情報の要求を防止することで、トークンの有効期間の管理を簡素化する、[トークン キャッシュ](msal-acquire-cache-tokens.md)を追加します。

## <a name="common-authority"></a>共通の機関

V1.0 では、`https://login.microsoftonline.com/common` 機関を使用する場合、ユーザーは任意の Azure Active Directory (AAD) アカウントで (任意の組織に対して) サインインできます。

V2.0 で `https://login.microsoftonline.com/common` 機関を使用する場合、ユーザーは任意の AAD 組織、または Microsoft の個人アカウント (MSA) を使用してサインインできます。 MSAL for Java で、ログインを任意の AAD アカウントに制限したい場合は、`https://login.microsoftonline.com/organizations` 機関 (これは ADAL4J と同じ動作です) を使用する必要があります。 機関を指定するには、`PublicClientApplication` クラスを作成するときに、[PublicClientApplication.Builder](https://javadoc.io/doc/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.Builder.html) メソッドの `authority` パラメーターを設定します。

## <a name="v10-and-v20-tokens"></a>v1.0 トークンと v2.0 トークン

v1.0 エンドポイント (ADAL で使用) では、v1.0 トークンのみが出力されます。

V2.0 エンドポイント (MSAL で使用) では、v1.0 と v2.0 のトークンを出力できます。 Web API のアプリケーション マニフェストのプロパティでは、開発者は、受け入れられるトークンのバージョンを選択することができます。 [アプリケーション マニフェスト](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)に関するリファレンス ドキュメントの `accessTokenAcceptedVersion` についての記事を参照してください。

v1.0 トークンと v2.0 トークンの詳細については、[Azure Active Directory アクセス トークン](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)に関するページを参照してください。

## <a name="adal-to-msal-migration"></a>ADAL から MSAL への移行

ADAL4J では、更新トークンが公開されました。これにより、開発者はこれをキャッシュできます。 次に、`AcquireTokenByRefreshToken()` を使用して、ユーザーが接続されていないときにユーザーの代わりにダッシュボードを更新する、実行時間の長いサービスの実装などのソリューションを有効にします。

MSAL for Java では、セキュリティ上の理由により、更新トークンは公開されません。 代わりに、MSAL がトークンの更新を処理します。

MSAL for Java には、ADAL4J で取得した更新トークンを ClientApplication に移行するための API が用意されています: [acquireToken(RefreshTokenParameters)](https://javadoc.io/static/com.microsoft.azure/msal4j/1.0.0/com/microsoft/aad/msal4j/PublicClientApplication.html#acquireToken-com.microsoft.aad.msal4j.RefreshTokenParameters-)。 この方法では、必要なスコープ (リソース) と共に、以前使用された更新トークンを提供することができます。 更新トークンは新しいものと交換され、アプリケーションで使用するためにキャッシュされます。

次のコード スニペットは、機密クライアント アプリケーションでの移行コードを示しています。

```java
String rt = GetCachedRefreshTokenForSIgnedInUser(); // Get refresh token from where you have them stored
Set<String> scopes = Collections.singleton("SCOPE_FOR_REFRESH_TOKEN");

RefreshTokenParameters parameters = RefreshTokenParameters.builder(scopes, rt).build();

PublicClientApplication app = PublicClientApplication.builder(CLIENT_ID) // ClientId for your application
                .authority(AUTHORITY)  //plug in your authority
                .build();

IAuthenticationResult result = app.acquireToken(parameters);
```

この `IAuthenticationResult` は、新しい更新トークンがキャッシュに格納されている間、アクセス トークンと ID トークンを返します。 アプリケーションには、IAccount も含まれるようになりました。

```java
Set<IAccount> accounts =  app.getAccounts().join();
```

現在キャッシュ内にあるトークンを使用するには、次のように呼び出します。

```java
SilentParameters parameters = SilentParameters.builder(scope, accounts.iterator().next()).build(); 
IAuthenticationResult result = app.acquireToken(parameters);
```
