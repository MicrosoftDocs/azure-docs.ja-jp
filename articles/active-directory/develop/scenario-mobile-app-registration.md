---
title: Web API を呼び出すモバイル アプリを登録する | Azure
titleSuffix: Microsoft identity platform
description: Web API を呼び出すモバイル アプリを構築する方法 (アプリの登録) について説明します
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 6385f03556d155941139b77333d6f4a25081fe67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100103160"
---
# <a name="register-mobile-apps-that-call-web-apis"></a>Web API を呼び出すモバイル アプリを登録する

この記事では、作成中のモバイル アプリケーションを登録するための手順について説明します。

## <a name="supported-account-types"></a>サポートされているアカウントの種類

モバイル アプリケーションでサポートされるアカウントの種類は、有効にするエクスペリエンスと使用するフローによって異なります。

### <a name="audience-for-interactive-token-acquisition"></a>対話型トークン取得の対象ユーザー

ほとんどのモバイル アプリケーションでは、対話型認証が使用されています。 アプリケーションでこの形式の認証を使用する場合は、任意の[アカウントの種類](quickstart-register-app.md)からユーザーをサインインできます。

### <a name="audience-for-integrated-windows-authentication-username-password-and-b2c"></a>統合 Windows 認証の対象ユーザー、ユーザー名とパスワード、B2C

ユニバーサル Windows プラットフォーム (UWP) アプリがある場合は、統合 Windows 認証を使用してユーザーにサインインできます。 統合 Windows 認証またはユーザー名とパスワード認証を使用するには、アプリケーションでは自分の基幹業務 (LOB) 開発者テナントにユーザーをサインインする必要があります。 独立系ソフトウェア ベンダー (ISV) のシナリオでは、アプリケーションは Azure Active Directory 組織内のユーザーにサインインできます。 これらの認証フローは、Microsoft の個人用アカウントではサポートされていません。

B2C の機関およびポリシーを渡すソーシャル ID を使用してユーザーにサインインすることもできます。 この方法を使用するには、対話型認証およびユーザー名とパスワード認証のみを使用できます。 ユーザー名とパスワード認証は現在、Xamarin.iOS、Xamarin.Android、および UWP でのみサポートされています。

詳細については、「[シナリオとサポートされている認証フロー](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)」と「[シナリオとサポートされているプラットフォームと言語](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)」を参照してください。

## <a name="platform-configuration-and-redirect-uris"></a>プラットフォーム構成とリダイレクト URI

### <a name="interactive-authentication"></a>対話型認証

対話型認証を使用するモバイル アプリをビルドするときに最も重要な登録手順は、リダイレクト URI です。 対話型認証は、[ **[認証]** ブレードのプラットフォーム構成](https://aka.ms/MobileAppReg)を使用して設定できます。

このエクスペリエンスにより、お使いのアプリで Microsoft Authenticator (および Android の Intune ポータル サイト) を介してシングル サインオン (SSO) できるようになります。 また、デバイス管理ポリシーもサポートされます。

アプリ登録ポータルでは、iOS および Android アプリケーションの仲介型応答 URI を計算するのに役立つプレビュー エクスペリエンスがあります。

1. アプリの登録で、 **[認証]**  >  **[新しいエクスペリエンスを試す]** を選択します。

   ![新しいエクスペリエンスを選択する [認証] ブレード](https://user-images.githubusercontent.com/13203188/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. **[プラットフォームの追加]** を選択します。

   ![プラットフォームの追加](https://user-images.githubusercontent.com/13203188/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. プラットフォームの一覧がサポートされている場合は、 **[iOS]** を選択します。

   ![モバイル アプリケーションの選択](https://user-images.githubusercontent.com/13203188/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. バンドル ID を入力し、 **[登録]** を選択します。

   ![バンドル ID の入力](https://user-images.githubusercontent.com/13203188/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

手順を完了すると、次の画像のようにリダイレクト URI が計算されます。

![結果として得られるリダイレクト URI](https://user-images.githubusercontent.com/13203188/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

リダイレクト URI を手動で構成する場合は、アプリケーション マニフェストを介して行えます。 マニフェストの推奨される形式は次のとおりです。

- **iOS**: `msauth.<BUNDLE_ID>://auth`
  - たとえば、「`msauth.com.yourcompany.appName://auth`」と入力します。
- **Android**: `msauth://<PACKAGE_NAME>/<SIGNATURE_HASH>`
  - Android の署名のハッシュは、KeyTool コマンドを通じて、リリース キーまたはデバッグ キーを使用して生成できます。

### <a name="username-password-authentication"></a>ユーザー名とパスワード認証

アプリでユーザー名とパスワード認証のみを使用する場合は、アプリケーションのリダイレクト URI を登録する必要はありません。 このフローによって、Microsoft ID プラットフォームへのラウンド トリップが実行されます。 アプリケーションが特定の URI でコールバックされることはありません。

ただし、アプリケーションはパブリック クライアント アプリケーションとして識別してください。 そのためには次を行います。

1. 引き続き <a href="https://portal.azure.com/" target="_blank">Azure portal</a> の **[アプリの登録]** でアプリを選択し、 **[認証]** を選択します。
1. **[詳細設定]**  >  **[Allow public client flows]\(パブリック クライアント フローを許可する\)**  >  **[Enable the following mobile and desktop flows:]\(次のモバイルとデスクトップのフローを有効にする\)** で **[はい]** を選択します。

   :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Azure portal の [認証] ウィンドウでパブリック クライアント設定を有効にする":::

## <a name="api-permissions"></a>API のアクセス許可

モバイル アプリケーションでは、サインインしたユーザーの代わりに API を呼び出せます。 アプリは、委任されたアクセス許可を要求する必要があります。 これらのアクセス許可は、スコープとも呼ばれます。 必要なエクスペリエンスに応じて、Azure portal を使用して、委任されたアクセス許可を静的に要求できます。 または、これらを実行時に動的に要求することができます。

アクセス許可を静的に登録すれば、管理者は簡単にアプリを承認できます。 静的な登録をお勧めします。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[アプリ コードの構成](scenario-mobile-app-configuration.md)に関する記事に進みます。
