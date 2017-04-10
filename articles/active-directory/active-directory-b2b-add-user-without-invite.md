---

title: "招待を使用せずに B2B コラボレーション ユーザーを Azure Active Directory に追加する | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションの招待を利用しないでゲスト ユーザーが他のゲスト ユーザーを Azure AD に追加することができます。"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 03/15/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 91b9477cdb679851e7d8d2942c06999a05f64e46
ms.lasthandoff: 03/17/2017


---

# <a name="add-b2b-collaboration-guest-users-without-an-invitation"></a>招待を使用せずに B2B コラボレーション ゲスト ユーザーを追加する

パートナーの担当者などのユーザーに、招待を利用しなくてもパートナーのユーザーを組織に追加することを許可できます。 行う必要があるのは、パートナー組織に使用しているディレクトリにユーザー列挙権限を付与することだけです。 

これらの権限は次の場合に付与します。

1. ホスト組織のユーザー (たとえば、WoodGrove) が、パートナー組織の 1 人のユーザー (たとえば、Sam@litware.com) をゲストとして招待します。
2. ホスト組織の管理者が、パートナー組織 (Litware) の他のユーザーを特定し追加することを Sam に許可するポリシーをセットアップします。
3. これで、Sam は Litware の他のユーザーを WoodGrove のディレクトリ、グループ、またはアプリケーションに追加できます。招待に応じる操作は必要ありません。 Sam が Litware での適切な列挙権限を持っていれば、自動的に処理されます。

### <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure AD B2B コラボレーションのライセンス](active-directory-b2b-licensing.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
