---
title: クライアント アプリケーションの初期化 (.NET 用 Microsoft Authentication Library) | Azure
description: .NET 用 Microsoft Authentication Library (MSAL.NET) を使用したパブリック クライアント アプリケーションと機密性の高いクライアント アプリケーションの初期化について説明します。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5012da8f2ff41971df674fd35162fe14e1de8fc9
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532647"
---
# <a name="initialize-client-applications-using-msalnet"></a>MSAL.NET を使用してクライアント アプリケーションを初期化する
この記事では、.NET 用 Microsoft Authentication Library (MSAL.NET) を使用したパブリック クライアント アプリケーションと機密性の高いクライアント アプリケーションの初期化について説明します。  クライアント アプリケーションの種類とアプリケーションの構成オプションの詳細については、[概要](msal-client-applications.md)に関するページを参照してください。

MSAL.NET 3.x でアプリケーションをインスタンス化するには、アプリケーション ビルダー `PublicClientApplicationBuilder` および `ConfidentialClientApplicationBuilder` を使用することをお勧めします。 これらは、コードまたは構成ファイルから、または両方のアプローチを組み合わせることで、アプリケーションを構成する強力なメカニズムを提供します。

## <a name="prerequisites"></a>前提条件
アプリケーションを初期化する前に、まず、そのアプリケーションを[登録](quickstart-register-app.md)して、Microsoft ID プラットフォームに統合できるようにする必要があります。  登録後に、次の情報が必要な場合があります (Azure portal で検索できます)。

- クライアント ID (GUID を表す文字列)
- ID プロバイダーの URL (名前付きインスタンス) とアプリケーションのサインイン対象ユーザー。 これら 2 つのパラメーターは機関と総称されます。
- 組織専用の基幹業務アプリケーション (および名前付きシングル テナント アプリケーション) を作成している場合はテナント ID。
- アプリケーション シークレット (クライアント シークレット文字列) または機密クライアント アプリの場合は (X509Certificate2 種類の) 証明書。
- Web アプリ、および場合によってはパブリック クライアント アプリの場合 (特に、アプリでブローカーを使用する必要がある場合)、ID プロバイダーがセキュリティ トークンを使用してアプリケーションに連絡する redirectUri も設定します。

## <a name="ways-to-initialize-applications"></a>アプリケーションを初期化する方法
クライアント アプリケーションをインスタンス化するにはさまざまな方法があります。

### <a name="initializing-a-public-client-application-from-code"></a>コードからパブリック クライアント アプリケーションを初期化する

次のコードは、ユーザーが自身の職場および学校のアカウントを使用して、または個人の Microsoft アカウントを使用して、Microsoft Azure パブリック クラウドにサインインすることで、パブリック クライアント アプリケーションをインスタンス化します。

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>コードから機密性の高いクライアント アプリケーションを初期化する

同じ方法で、次のコードは、Microsoft Azure パブリック クラウド内のユーザーからのトークンをユーザーの職場および学校のアカウントを使用して、または個人の Microsoft アカウントを使用して処理することで、機密性の高いアプリケーション (`https://myapp.azurewebsites.net` にある Web アプリ) をインスタンス化します。 アプリケーションは、クライアント シークレットを共有することで、ID プロバイダーで識別されます。

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

ご存知かもしれませんが、運用環境では、クライアント シークレットを使用するよりも、Azure AD と証明書を共有した方がよい場合があります。 コードは次のようになります。

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>構成オプションからパブリック クライアント アプリケーションを初期化する

次のコードは、構成オブジェクトからパブリック クライアント アプリケーションをインスタンス化します。構成オブジェクトは、プログラムで入力することも、構成ファイルから読み取ることもできます。

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>構成オプションから機密性の高いクライアント アプリケーションを初期化する

機密性の高いクライアント アプリケーションにも同じ種類のパターンが適用されます。 `.WithXXX` 修飾子 (ここでは certificate) を使用して他のパラメーターを追加することもできます。

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>ビルダー修飾子

アプリケーション ビルダーを使用するコード スニペットでは、`.With` メソッドの数を修飾子として適用できます (例: `.WithCertificate` および `.WithRedirectUri`)。 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>パブリック クライアント アプリケーションと機密性の高いクライアント アプリケーションに共通の修飾子

パブリック クライアント アプリケーション ビルダーまたは機密の高いクライアント アプリケーション ビルダーに設定できる修飾子は次のとおりです。

|パラメーター | 説明|
|--------- | --------- |
|`.WithAuthority()` 7 overrides | アプリケーションの既定の機関を Azure AD 機関に設定します。可能な選択肢は、Azure Cloud、対象ユーザー、テナント (テナント ID、またはドメイン名)、または機関 URI を直接提供する、のいずれかです。|
|`.WithAdfsAuthority(string)` | アプリケーションの既定の機関が ADFS 機関になるように設定します。|
|`.WithB2CAuthority(string)` | アプリケーションの既定の機関が Azure AD B2C 機関になるように設定します。|
|`.WithClientId(string)` | クライアント ID をオーバーライドします。|
|`.WithComponent(string)` | MSAL.NET を使用して、ライブラリの名前を設定します (テレメトリの理由のため)。 |
|`.WithDebugLoggingCallback()` | 呼び出されると、アプリケーションは単にデバッグ トレースを有効にして、`Debug.Write` を呼び出します。 詳細については、「[Logging](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)」 (ログ記録) を参照してください。|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | すべての認証要求で送信されるアプリケーション レベルの追加のクエリ パラメーターを設定します。 これはトークンの取得メソッド レベルごとにオーバーライドできます (同じ `.WithExtraQueryParameters pattern` を使用)。|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | HTTP プロキシの構成などの高度なシナリオを有効にするか、または MSAL で強制的に (ASP.NET Core Web アプリ/API などで) 特定の HttpClient を使用します。|
|`.WithLogging()` | 呼び出されると、アプリケーションはデバッグ トレースを使用してコールバックを呼び出します。 詳細については、「[Logging](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)」 (ログ記録) を参照してください。|
|`.WithRedirectUri(string redirectUri)` | 既定のリダイレクト URI をオーバーライドします。 パブリック クライアント アプリケーションの場合、これはブローカーが関与するシナリオで役立ちます。|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | テレメトリを送信するために使用するデリゲートを設定します。|
|`.WithTenantId(string tenantId)` | テナント ID、またはテナントの説明をオーバーライドします。|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Xamarin.iOS アプリケーションに固有の修飾子

Xamarin.iOS でパブリック クライアント アプリケーション ビルダーに設定できる修飾子は次のとおりです。

|パラメーター | 説明|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS のみ**:iOS キー チェーンのセキュリティ グループを設定します (キャッシュの永続化のため)。|

### <a name="modifiers-specific-to-confidential-client-applications"></a>機密性の高いクライアント アプリケーションに固有の修飾子

機密性の高いクライアント アプリケーション ビルダーに設定できる修飾子は次のとおりです。

|パラメーター | 説明|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Azure AD でアプリケーションを識別する証明書を設定します。|
|`.WithClientSecret(string clientSecret)` | Azure AD でアプリケーションを識別する、クライアント シークレット (アプリ パスワード) を設定します。|

これらの修飾子は相互に排他的です。 両方を提供すると、意味のある例外が MSAL によってスローされます。

### <a name="example-of-usage-of-modifiers"></a>修飾子の使用例

アプリケーションが組織に固有の基幹業務アプリケーションだとします。  この場合、次のように記述できます。

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

興味深いのは、国内クラウドのプログラミングが簡略化されたことです。 国内クラウドでアプリケーションをマルチ テナント アプリケーションにする場合は、たとえば次のように記述できます。

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

ADFS のオーバーライドもあります (ADFS 2019 は現在サポートされていません)。
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

最後に、Azure AD B2C を開発している場合は、テナントを次のように指定できます。

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
