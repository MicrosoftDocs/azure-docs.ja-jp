---
title: "Azure Active Directory B2B コラボレーションとは | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションは、会社のアプリケーションにビジネス パートナーが選択的にアクセスできるようにすることで会社間のリレーションシップをサポートします。"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: fbc12a52555b190d43b5e953fd4d19923a25b0ed
ms.contentlocale: ja-jp
ms.lasthandoff: 06/28/2017


---

# <a name="what-is-azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーションとは

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

Azure AD を利用する組織は、Azure AD B2B (Business-To-Business) コラボレーション機能を通じて、他の任意の組織のユーザーと安全に共同作業を行うことができます。相手となる組織の規模の大小は関係がありません。 これらの組織の Azure AD の有無どころか、IT 組織の有無さえも関係ありません。 

Azure AD を使用する組織は、独自の社内データに対するコントロールを維持した状態で、そのパートナーに対してドキュメントやリソース、アプリケーションへのアクセスを提供することができます。 開発者は、Azure AD B2B API を使えば、2 つの組織を安全につなぐアプリケーションを開発することができます。 また、これにより、エンドユーザーがはるかに移動しやすくなります。

Microsoft のお客様の 97% が、Azure AD B2B コラボレーションがきわめて重要な意味を持つと回答しています。

![円グラフ](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

2017 年 4 月上旬の時点で、既に 300 万ユーザーが Azure AD B2B コラボレーションの機能を利用しています。 さらにその機能は、ユーザー数が 10 人を超える Azure AD 組織の 23% 以上で既に利用されています。

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>Azure AD B2B コラボレーションが組織にもたらす主な利点

### <a name="work-with-any-user-from-any-partner"></a>あらゆるパートナーのユーザーと連携可能

* パートナーはそれぞれ独自の資格情報を使用する

* Azure AD を使用するパートナーには負担が一切ない

* 外部のディレクトリや複雑なセットアップが不要

### <a name="simple-and-secure-collaboration"></a>単純かつ安全なコラボレーション

* 完成度の高い Azure AD の強力な承認ポリシーを適用しながら会社のあらゆるアプリやデータへのアクセスを提供

* ユーザーにとって容易

* エンタープライズ レベルのセキュリティをアプリとデータに確保

### <a name="no-management-overhead"></a>管理のオーバーヘッドを排除

* 外部アカウントやパスワードの管理が不要

* 同期や手動によるアカウント ライフサイクル管理が不要

* 外部の管理オーバーヘッドが発生しない

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>B2B コラボレーションのユーザーを自社組織に簡単に追加できる

B2B コラボレーションの (ゲスト) ユーザーは、管理者が [Azure Portal](https://portal.azure.com) から追加できます。

![ゲスト ユーザーの追加](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>コラボレーターによる ID の持ち込みの実現

B2B のコラボレーターは、自分が選んだ任意の ID でサインインすることができます。 Microsoft アカウントも Azure AD アカウントも持っていなければ、オファーに応じた時点でシームレスに作成されます。

![サインイン ID の選択](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>アプリケーションとグループの所有者への委任 
アプリケーションとグループの所有者は、Microsoft のアプリケーションであるかどうかに関係なく、自分が管理する任意のアプリケーションに対して B2B ユーザーを直接追加することができます。 管理者は、非管理者ユーザーに対し、B2B ユーザーを追加する権限を委任することができます。 非管理者は、[Azure AD のアプリケーション アクセス パネル](https://myapps.microsoft.com)を使って B2B コラボレーション ユーザーをアプリケーションまたはグループに追加できます。

![アクセス パネル](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![メンバーの追加](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>承認ポリシーによる会社のコンテンツの保護

多要素認証などの条件付きアクセス ポリシーを次のように適用できます。
- テナント レベルで
- アプリケーション レベルで
- 特定のユーザーが会社のアプリケーションとデータを保護するために

![メンバーの追加](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Microsoft 提供の API とサンプル コードを使用した、ユーザー受け入れのためのアプリケーションの容易な構築
貴社のニーズに応じて、外部のパートナーの受け入れ方法をカスタマイズすることができます。

[B2B コラボレーションの招待 API](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) を使用すると、セルフサービス サインアップ ポータルの作成など、オンボーディング エクスペリエンスをカスタマイズすることができます。 セルフサービス ポータル用のサンプル コードは [Github](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web) で提供しています。

![サインアップ ポータル](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Azure AD B2B コラボレーションを利用すれば、Azure AD の強みをフルに活かしながら、エンド ユーザーにとって簡単かつ直感的な方法でパートナー関係を保護することができます。 Azure AD B2B を使った外部とのコラボレーションは、既に数千の企業によって使用されている実績があります。ぜひご利用ください。

## <a name="next-steps"></a>次のステップ

* 管理者の操作は [Azure Portal](https://portal.azure.com) から実行できます。

* インフォメーション ワーカーの操作は[アクセス パネル](https://myapps.microsoft.com)から実行できます。

* また、フィードバックやご意見、ご提案があれば、[Microsoft Tech Community](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) からいつでも製品チームにお寄せください。

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [B2B コラボレーション ユーザーの監査およびレポート](active-directory-b2b-auditing-and-reporting.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

