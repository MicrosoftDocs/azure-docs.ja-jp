---
title: ブローカーを使用する Xamarin Android アプリを MSAL.NET に移行する
titleSuffix: Microsoft identity platform
description: Microsoft Authenticator または Intune ポータル サイトを使用する Xamarin Android アプリを ADAL.NET から MSAL.NET に移行する方法について説明します。
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 01af0e620ecb100839f7b1101e5ff9fcfc874eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206671"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>ブローカーを使用する Android アプリケーションを ADAL.NET から MSAL.NET に移行する

Xamarin Android アプリで現在 Azure Active Directory Authentication Library for .NET (ADAL.NET) と[認証ブローカー](msal-android-single-sign-on.md)が使用されている場合は、この時点で [Microsoft Authentication Library for .NET](msal-overview.md) (MSAL.NET) に移行します。

## <a name="prerequisites"></a>前提条件

* Xamarin Android アプリが既に、ブローカー ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) または [Intune ポータル サイト](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) と MSAL.NET に移行する必要がある ADAL.NET に統合されている。

## <a name="step-1-enable-the-broker"></a>手順 1:ブローカーを有効にします

<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
ADAL.NET では、ブローカーのサポートは認証ごとのコンテキストで有効になっています。

ブローカーを呼び出すには、`PlatformParameters` コンストラクターで `useBroker` を *true* に設定する必要がありました。

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

Android 用プラットフォーム固有のページ レンダラー コードでは、`useBroker` フラグを true に設定します。

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

次に、トークンを取得する呼び出しで、次のパラメーターを含めます。

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
MSAL.NET では、ブローカーのサポートは PublicClientApplication ごとに有効になります。

(既定で true に設定されている) `WithBroker()` パラメーターを使用して、ブローカーを呼び出します。

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

次に、AcquireToken 呼び出しで次の手順に従います。

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>手順 2:アクティビティを設定する

ADAL.NET で、[手順 1: ブローカーを有効にする](#step-1-enable-the-broker)で示したように、PlatformParameters の一部としてアクティビティ (通常は MainActivity) を渡しました。

MSAL.NET でもアクティビティが使用されますが、ブローカーを使用せずに通常どおりに Android を使用する場合は必要ありません。 ブローカーを使用するには、ブローカーから応答が送受信されるようにアクティビティを設定します。

<table>
<tr><td>現在の ADAL でのコード:</td><td>MSAL での該当部分:</td></tr>
<tr><td>
アクティビティは、Android 固有のプラットフォームで PlatformParameters に渡されます。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

MSAL.NET で Android のアクティビティを設定するには、次の 2 つの操作を行います。

1. `MainActivity.cs` で、`App.RootViewController` を `MainActivity` に設定して、アクティビティでブローカーが呼び出されるようにします。

    正しく設定されていないと、「`"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`」というエラーが表示されることがあります。

1. AcquireTokenInteractive の呼び出しで、`.WithParentActivityOrWindow(App.RootViewController)` を使用し、使用するアクティビティへの参照を渡します。 この例では、MainActivity を使用します。

**例:**

*App.cs* の場合:

```CSharp
   public static object RootViewController { get; set; }
```

*MainActivity.cs* の場合:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

AcquireToken 呼び出しの場合:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>次のステップ

Xamarin で MSAL.NET を使用する際の Android 固有の考慮事項の詳細については、[MSAL.NET を使用した Xamarin Android の構成要件とトラブルシューティング ヒント](msal-net-xamarin-android-considerations.md)に関するページを参照してください。