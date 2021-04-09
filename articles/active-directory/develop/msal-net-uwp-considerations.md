---
title: UWP に関する考慮事項 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: ユニバーサル Windows プラットフォーム (UWP) を Microsoft Authentication Library for .NET (MSAL.NET) と使用する場合の考慮事項について説明します。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2021
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8a8aab447007eb574a7a4bc532d8177bd0d8b345
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122478"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>ユニバーサル Windows プラットフォームを MSAL.NET と使用する場合の考慮事項
ユニバーサル Windows プラットフォーム (UWP) を MSAL.NET と使用するアプリケーションの開発者は、この記事で紹介する概念を考慮する必要があります。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork プロパティ
Windows ランタイム (WinRT) プラットフォームでは、`PublicClientApplication` にブール型プロパティ `UseCorporateNetwork` があります。 このプロパティを使用すると、フェデレーション Azure Active Directory (Azure AD) テナントを持つアカウントにユーザーがサインインしている場合に、Windows 10 アプリケーションと UWP アプリケーションで統合 Windows 認証 (IWA) のメリットを活用できるようになります。 オペレーティング システムにサインインしているユーザーが、シングル サインオン (SSO) を使用することもできます。 `UseCorporateNetwork` プロパティを設定すると、MSAL.NET により、Web 認証ブローカー (WAB) が使用されます。

> [!IMPORTANT]
> `UseCorporateNetwork` プロパティを true に設定する場合は、アプリケーション開発者がアプリケーションで IWA を有効にしておくことが前提となります。 IWA を有効にするには、次を実行します。
> - UWP アプリケーションの `Package.appxmanifest` の **[機能]** タブで、以下の機能を有効にします。
>   - **エンタープライズ認証**
>   - **プライベート ネットワーク (クライアントとサーバー)**
>   - **共有ユーザー証明書**

IWA は既定では有効になっていません。これは、Microsoft Store では、エンタープライズ認証または共有ユーザー証明書の機能を要求するアプリケーションを受け入れる前に、高いレベルの検証が求められるからです。 すべての開発者がこのレベルの検証を望むとも限りません。

UWP プラットフォームでは、条件付きアクセスが有効になっているエンタープライズ シナリオでは、基になる WAB 実装が正しく動作しません。 ユーザーが Windows Hello を使用してサインインしようとすると、この問題が発生します。 ユーザーが証明書を選択するように求められたとき、以下が発生します。

- PIN に対応した証明書が見つからない。
- ユーザーが証明書を選択すると、PIN 入力が求められなくなる。

この問題を回避するために、ユーザー名、パスワード、電話認証などの代替方法を使用することはできますが、望ましいエクスペリエンスではありません。

## <a name="troubleshooting"></a>トラブルシューティング

インターネット接続があり、パブリック ネットワークでは接続が動作することがわかっている特定のエンタープライズ環境で、一部のお客様から次のサインイン エラーが報告されました。

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

この問題を回避するには、WAB (基礎となる Windows コンポーネント) でプライベート ネットワークが許可されていることを確認します。 これを行うには、次のようにレジストリ キーを設定します。

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

詳細については、[Web 認証ブローカー - Fiddler](/windows/uwp/security/web-authentication-broker#fiddler) に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
詳細については、以下の例を参照してください。

サンプル | プラットフォーム | 説明 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | MSAL.NET を使用する UWP クライアント アプリケーション。 Azure AD 2.0 エンドポイントを使用して認証を行うユーザーの Microsoft Graph にアクセスします。 <br>![トポロジ](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | MSAL を使用して、Microsoft ID プラットフォームを介して Microsoft の個人用アカウントと Azure AD を認証する方法を示す Xamarin Forms アプリ。 また、Microsoft Graph にアクセスする方法と、結果として生成されるトークンについても示します。 <br>![MSAL を使用して Microsoft ID プラットフォームを介して Microsoft の個人用アカウントと Azure AD を認証する方法を示している図。](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
