---
title: 開発するモバイル アプリでシングル サインオンとアプリ保護ポリシーをサポートする | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームを使用し、Azure Active Directory と統合してシングル サインオンとアプリ保護ポリシーをサポートするモバイル アプリケーションの構築に関する説明と概要。
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/14/2020
ms.author: nichola
ms.openlocfilehash: 4f0588667df6acb11a43e8c3469c67f65ed3cdd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165180"
---
# <a name="support-single-sign-on-and-app-protection-policies-in-mobile-apps-you-develop"></a>開発するモバイル アプリでシングル サインオンとアプリ保護ポリシーをサポートする

シングル サインオン (SSO) は、アプリ ユーザーに簡単で安全なログインを提供する、Microsoft ID プラットフォームと Azure Active Directory の重要なオファリングです。 さらに、アプリ保護ポリシー (APP) を使用すると、ユーザーのデータを安全に保つ主要なセキュリティ ポリシーをサポートできます。 これらの機能を併用することにより、ユーザーのログインとアプリのデータの管理がセキュリティで保護されます。

> [!VIDEO https://www.youtube.com/embed/JpeMeTjQJ04]

この記事では、SSO と APP が重要である理由について説明し、これらの機能をサポートするモバイル アプリケーションをビルドするための概略的なガイダンスを示します。 これは、電話とタブレットの両方のアプリに適用されます。 組織の Azure Active Directory テナント全体に SSO をデプロイする IT 管理者は、[シングル サインオンのデプロイを計画するためのガイダンス](../manage-apps/plan-sso-deployment.md)を確認してください。

## <a name="about-single-sign-on-and-app-protection-policies"></a>シングル サインオンとアプリ保護ポリシーについて

[シングル サインオン (SSO)](../manage-apps/plan-sso-deployment.md) を使用すると、ユーザーは 1 回サインインするだけで、資格情報を再入力することなく他のアプリケーションにアクセスできます。 これにより、アプリへのアクセスが簡単になり、ユーザーがユーザー名とパスワードの長いリストを記憶する必要がなくなります。 これをアプリに実装すると、アプリへのアクセスと使用が簡単になります。

さらに、アプリでシングル サインオンを有効にすると、[パスワードなしログイン](../authentication/concept-authentication-passwordless.md)などの先進認証を使用する新しい認証メカニズムがロック解除されます。 ユーザー名とパスワードは、アプリケーションに対する最も一般的な攻撃ベクトルの 1 つです。SSO を有効にすると、さらにセキュリティを強化したり、より安全な認証メカニズムに依存する条件付きアクセスまたはパスワードなしログインを適用することで、このリスクを軽減することができます。 最後に、シングル サインオンを有効にすると、[シングル サインアウト](v2-protocols-oidc.md#single-sign-out)も有効になります。これは、共有デバイスで使用される作業アプリケーションのような状況で役立ちます。

[アプリ保護ポリシー (APP)](/mem/intune/apps/app-protection-policy) を使用すると、組織のデータが安全に格納され続けます。 これにより、企業はアプリ内のデータを管理および保護し、アプリとそのデータにアクセスできるユーザーを制御できるようになります。 アプリ保護ポリシーを実装すると、アプリは条件付きアクセス ポリシーによって保護されているリソースにユーザーを接続し、他の保護されたアプリとの間で安全にデータを転送することができます。 アプリ保護ポリシーによってロック解除されるシナリオとしては、アプリを開くために PIN を要求する場合、アプリ間でデータの共有を制御する場合、会社のアプリ データが個人の保存場所に保存されるのを防ぐ場合などがあります。

## <a name="implementing-single-sign-on"></a>シングル サインオンの実装

アプリでシングル サインオンを利用できるようにするには、次のことをお勧めします。

### <a name="use-the-microsoft-authentication-library-msal"></a>Microsoft Authentication Library (MSAL) を使用する

アプリケーションでシングル サインオンを実装する場合は、[Microsoft Authentication Library (MSAL)](msal-overview.md) を使用することをお勧めします。 MSAL を使用すると、最小限のコードおよび API 呼び出しでアプリに認証を追加し、[Microsoft ID プラットフォーム](./index.yml)のすべての機能を取得して、セキュリティで保護された認証ソリューションのメンテナンスを Microsoft で処理できるようにすることができます。 既定では、MSAL によってアプリケーションの SSO サポートが追加されます。 さらに、アプリ保護ポリシーも実装する場合は、MSAL の使用が必須となります。

> [!NOTE]
> 埋め込み Web ビューを使用するように MSAL を構成することもできます。 この場合は、シングル サインオンが阻止されます。 SSO を確実に機能させるには、既定の動作 (つまり、システム Web ブラウザー) を使用します。

アプリケーションで [ADAL ライブラリ](../azuread-dev/active-directory-authentication-libraries.md)を現在使用している場合、[ADAL は非推奨となる](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)ため、[MSAL に移行する](msal-migration.md)ことを強くお勧めします。

iOS アプリケーションの場合は、MSAL を使用してサインインを設定する方法と、[さまざまな SSO シナリオに合わせて MSAL を構成するためのガイダンス](single-sign-on-macos-ios.md)を示す、[クイックスタート](quickstart-v2-ios.md)が用意されています。

Android アプリケーションの場合は、MSAL を使用してサインインを設定する方法を示す[クイックスタート](quickstart-v2-android.md)と、[Android で MSAL を使用してクロスアプリ SSO を有効にする方法](msal-android-single-sign-on.md)についてのガイダンスが用意されています。

### <a name="use-the-system-web-browser"></a>システム Web ブラウザーを使用する

対話型の認証には Web ブラウザーが必要です。 MSAL 以外の先進認証ライブラリ (つまり、他の OpenID Connect または SAML ライブラリ) を使用するモバイル アプリの場合、あるいは独自の認証コードを実装する場合は、認証画面としてシステム ブラウザーを使用して SSO を有効にする必要があります。

Google では、Android アプリケーションでこれを行うためのガイダンス: 「[Chrome Custom Tabs - Google Chrome](https://developer.chrome.com/multidevice/android/customtabs)」(Chrome のカスタム タブ - Google Chrome) が用意されています。

Apple では、iOS アプリケーションでこれを行うためのガイダンス: 「[Authenticating a User Through a Web Service | Apple Developer Documentation](https://developer.apple.com/documentation/authenticationservices/authenticating_a_user_through_a_web_service)」(Web サービスを使用したユーザーの認証 | Apple 開発者ドキュメント) が用意されています。

> [!TIP]
> [Apple デバイス用の SSO プラグイン](apple-sso-plugin.md)を使用すると、Intune により、管理対象デバイスで埋め込み Web ビューを使用する iOS アプリに対して SSO が許可されます。 すべてのユーザーに対して SSO を有効にするアプリを開発するための最適なオプションとして、MSAL とシステム ブラウザーを使用することをお勧めします。ただし、この場合、SSO を使用できない一部のシナリオで SSO が許可されます。

## <a name="enable-app-protection-policies"></a>アプリ保護ポリシーを有効にする

アプリ保護ポリシーを有効にするには、[Microsoft Authentication Library (MSAL)](msal-overview.md) を使用します。 MSAL は Microsoft ID プラットフォームの認証および承認ライブラリであり、Intune SDK はこれと連動するように開発されています。

また、認証にブローカー アプリを使用する必要があります。 ブローカーにより、アプリのコンプライアンスを確保するために、アプリケーションとデバイスの情報を提供するようアプリに要求されます。 [ブローカー認証](./msal-android-single-sign-on.md)に、iOS ユーザーは [Microsoft Authenticator アプリ](../user-help/user-help-auth-app-sign-in.md)を使用し、Android ユーザーは Microsoft Authenticator アプリまたは[ポータル サイト アプリ](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)を使用します。 既定では、MSAL によって、認証要求を満たすための最初の選択肢としてブローカーが使用されます。したがって、既製の MSAL を使用する場合、認証のためのブローカーの使用がアプリに対して自動的に有効になります。

最後に、アプリ保護ポリシーを有効にするために、アプリに [Intune SDK を追加](/mem/intune/developer/app-sdk-get-started)します。 ほとんどの場合、SDK はインターセプト モデルに従い、アプリ保護ポリシーが自動的に適用されて、アプリで実行されているアクションが許可されるかどうかが判断されます。 特定のアクションに制限があるかどうかをアプリに通知するために、手動で呼び出すことができる API もあります。

## <a name="additional-resources"></a>その他のリソース

- [Microsoft Azure Active Directory シングル サインオンのデプロイ計画](../manage-apps/plan-sso-deployment.md)
- [方法: macOS と iOS で SSO を構成する](single-sign-on-macos-ios.md)
- [Apple デバイス用の Microsoft Enterprise SSO プラグイン (プレビュー)](apple-sso-plugin.md)
- [Android のブローカー認証](./msal-android-single-sign-on.md)
- [承認エージェントとそれらを有効にする方法](./msal-android-single-sign-on.md)
- [Microsoft Intune アプリ SDK の概要](/mem/intune/developer/app-sdk-get-started)
- [Intune App SDK の設定を構成する](/mem/intune/developer/app-sdk-ios#configure-settings-for-the-intune-app-sdk)
- [保護されている Microsoft Intune アプリ](/mem/intune/apps/apps-supported-intune-apps)
