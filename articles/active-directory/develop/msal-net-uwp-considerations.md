---
title: ユニバーサル Windows プラットフォームに関する考慮事項 (.NET 用 Microsoft 認証ライブラリ) |Azure
description: .NET (MSAL.NET) 用 Microsoft 認証ライブラリでユニバーサル Windows プラットフォームを使用する場合の固有の考慮事項について説明します。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d78a64ee41e37fe53eba20eab6753c0b6eb8389
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277912"
---
# <a name="universal-windows-platform-specific-considerations-with-msalnet"></a>MSAL.NET でのユニバーサル Windows プラットフォームに固有の考慮事項
UWP には、MSAL.NET を使用する場合に考慮する必要がある考慮事項がいくつかあります。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork プロパティ
WinRT のプラットフォームにおける `PublicClientApplication` には、ブール型プロパティ ``UseCorporateNetwork`` があります。 このプロパティにより、ユーザーがフェデレーション Azure AD テナントのアカウントでサインインした場合、Win8.1 および UWP のアプリケーションで、統合 Windows 認証 (したがって、オペレーティング システムにサインインしたユーザーによる SSO) のメリットが得られます。 このプロパティを設定すると、MSAL.NET は WAB (Web Authentication Broker) を利用します。

> [!IMPORTANT]
> このプロパティを true に設定するにあたり、アプリケーション開発者がアプリケーションで統合 Windows 認証 (IWA) を有効にしておくことが前提となります。 この場合、
> - UWP アプリケーションの ``Package.appxmanifest`` の **[機能]** タブで、以下の機能を有効にします。
>   - エンタープライズ認証
>   - プライベート ネットワーク (クライアントとサーバー)
>   - 共有ユーザー証明書

エンタープライズ認証または共有ユーザー証明書の機能を要求するアプリケーションでは、Windows ストアに受け入れるためにより高いレベルの検証が必要であり、またすべての開発者がより高いレベルの検証を望むわけではないため、IWA は既定では有効になっていません。

UWP プラットフォーム (WAB) 上の基になる実装は、条件付きアクセスが有効となったエンタープライズ シナリオでは正しく動作しません。 症状は次のとおりです。ユーザーは Windows Hello でのサインインを試み、証明書を選択するように提案されますが:

- PIN に対応した証明書が見つかりません。
- または、ユーザーは証明書を選択しますが、PIN の指定を求められることはありません。

回避策として別の方法 (ユーザー名/パスワード + 電話認証) を使用できますが、あまり良い結果は得られません。

## <a name="troubleshooting"></a>トラブルシューティング

ある特定の企業環境で次のサインイン エラーが発生したという報告が、一部のお客様から寄せられています。

```Text
We can't connect to the service you need right now. Check your network connection or try this again later
```

一方で、インターネット接続があること、またパブリック ネットワークでは正しく動作することをお客様は把握しています。

回避策は、WAB (基礎となる Windows コンポーネント) がプライベート ネットワークを許可することの確認です。 これを行うには、次のようにレジストリ キーを設定します。

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

詳細については、[「Web 認証ブローカー」の「Fiddler」](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)を参照してください。

## <a name="next-steps"></a>次の手順
詳細は、以下の例に示されています。

サンプル | プラットフォーム | 説明 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | Azure AD v2.0 エンドポイントで認証されているユーザーの Microsoft Graph にアクセスする、msal.net を使用したユニバーサル Windows プラットフォームのクライアント アプリケーション。 <br>![トポロジ](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | AAD v 2.0 エンドポイント経由で MSA と Azure AD を認証し、結果のトークンで Microsoft Graph にアクセスするための MSAL の使用方法を示す、単純な Xamarin Forms アプリ。 <br>![トポロジ](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
