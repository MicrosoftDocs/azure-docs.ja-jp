---
title: "Azure Active Directory B2B コラボレーションのライセンスに関するガイダンス | Microsoft Docs"
description: "Azure Active Directory B2B コラボレーションでは、Azure AD 有料ライセンスは必要ありませんが、B2B ゲスト ユーザー用の有料機能を利用することもできます。"
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
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: dfef32c05af157ae8d3a5434016f87f488a35051
ms.contentlocale: ja-jp
ms.lasthandoff: 08/12/2017

---

# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Azure Active Directory B2B コラボレーションのライセンスに関するガイダンス

Azure AD B2B コラボレーション機能を使用すると、Azure AD サービスおよび組織内の他のリソースにアクセスできるように、Azure AD テナントにゲスト ユーザーを招待できます。 Azure AD の有料機能へのアクセスを提供する場合は、B2B コラボレーション ゲスト ユーザーに適切な Azure AD ライセンスを付与する必要があります。 

具体的には次の処理が行われます。
* Azure AD Free 機能は、ゲスト ユーザーに無償で提供され、追加のライセンスは必要ありません。
* B2B ユーザーに Azure AD の有償機能へのアクセスを提供するには、それらの B2B ゲスト ユーザーをサポートするために十分なライセンスが必要です。
* Azure AD の有料ライセンスを所有する招待元のテナントは、追加で 5 人のゲスト ユーザーを招待できる B2B コラボレーション ユーザーの権利を所有しています。
* 招待元のテナントを所有するお客様が、Azure AD 有料機能を必要とする B2B コラボレーション ユーザーの数を判断する必要があります。 ゲスト ユーザーに必要な Azure AD 有料機能に応じて、同じ 5:1 の割合で B2B コラボレーション ユーザーに十分な Azure AD の有料ライセンスが必要になります。

B2B コラボレーション ゲスト ユーザーは、お客様の組織の従業員、または複合企業内の組織の従業員のユーザーとしてではなく、パートナー企業のユーザーとして追加されます。 この記事で説明されているように、B2B ゲスト ユーザーは、外部資格情報またはお客様の組織が所有する資格情報でサインインできます。 

つまり、B2B ライセンスは、ユーザーの認証方法ではなく、お客様の組織との関係によって設定されます。 これらのユーザーがパートナーではない場合、ライセンス上は異なるユーザーとして扱われます。 ユーザー タイプが "ゲスト"としてマークされている場合でも、ライセンス上は、B2B コラボレーション ユーザーとはみなされません。 これらは、通常のライセンスと同様、ユーザーあたり 1 つのライセンスを取得する必要があります。 これらのユーザーは次のとおりです。
* お客様の組織の従業員
* 外部の ID を使用してサインインする従業員
* 複合企業内の各企業の従業員


## <a name="licensing-examples"></a>ライセンスの例
- 100 人の B2B コラボレーション ユーザーを Azure AD テナントに招待したいと考えているお客様がいます。 このすべてのユーザーに対してグループ ベースのアクセス管理とプロビジョニングを割さてますが、50 人のユーザーには MFA と条件付きアクセスも必要です。 このお客様の場合、B2B ユーザーに適切に対応するには、10 個の Azure AD Basic ライセンスと 10 個の Azure AD Premium P1 ライセンスを購入する必要があります。 お客様が B2B ユーザーに対して Identity Protection の機能を使用する場合、同じ 5:1 の比率で招待されたユーザーに対応できる数の Azure AD Premium P2 ライセンスが必要となります。
- お客様には、現在 Azure AD Premium P1 ライセンスが付与された 10 人の従業員がいます。 これらの従業員は、60 人の B2B ユーザーを招待する必要があり、すべてのユーザーに Multi-Factor Authentication (MFA) が必要となります。 5:1 のライセンス ルールに従い、このお客様が全 60 人の B2B コラボレーション ユーザーに対応するには、少なくとも 12 個の Azure AD Premium P1 ライセンスが必要です。 10 人の従業員の 10 個の Premium P1 ライセンスが既にあるため、MFA などの Premium P1 の機能を使用する 50 人の B2B ユーザーを招待する権利があります。 この例では、残りの 10 人の B2B コラボレーション ユーザーに対応するために、Premium P1 ライセンスをあと 2 つ購入する必要があります。

> [!NOTE]
> これらの B2B コラボレーション ユーザーの権利を有効にするために、B2B ユーザーにライセンスを直接割り当てる手段はありません。

招待元のテナントを所有するお客様が、Azure AD 有料機能を必要とする B2B コラボレーション ユーザーの数を判断する必要があります。 ゲスト ユーザーにどの Azure AD 有料機能が必要であるかに応じて、5:1 の割合で B2B コラボレーション ユーザーに対応するうえで十分な Azure AD の有料ライセンスが必要になります。 

## <a name="additional-licensing-details"></a>ライセンスのその他の詳細
- B2B ユーザー アカウントにライセンスを実際に割り当てる必要はありません。 5:1 の割合に基づいて、ライセンスは自動的に計算され、報告されます。
- テナントに Azure AD 有料ライセンスがない場合、招待された各ユーザーは、Azure AD Free エディションで提供される権利を取得します。
- B2B コラボレーション ユーザーが自分の組織の Azure AD 有料ライセンスを既に持っている場合、招待元のテナントの B2B コラボレーション ライセンスは使用しません。

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>高度なディスカッション: 貴社の API を使用して複合組織からユーザーを「メンバー」として追加する際のライセンスに関する考慮事項は何ですか?
B2B ゲスト ユーザーとは、パートナー組織から招待されてホスト組織で仕事をするユーザーです。 一般に、ほかのケースでは、B2B 機能を使用しても B2B とはみなされません。 具体的に 2 つのケースを見てみましょう。

1. ホストがコンシューマー アドレスを使用して従業員を招待する場合
  * このシナリオはライセンス ポリシーに準拠しておらず、推奨できません。

2. ホスト組織が別の複合組織からユーザーを追加する場合
  1. この場合、ユーザーは B2B API を使用して招待されますが、このケースは従来型の B2B ではありません。 理想として、これらの組織は他の組織ユーザーをメンバーとして招待するべきです (当社の API もこれなら可能です)。 この場合、これらのメンバーにライセンスを割り当てて、招待側組織のリソースにアクセスできるようにしなければなりません。

  2. 組織の中には、他の組織のユーザーをポリシー上「ゲスト」として追加したい場合もあるでしょう。 次の 2 つのケースがあります。
      * 複合組織が既に Azure AD を使用しており、招待されたユーザーが他の組織でライセンスを与えられている: この場合、招待されたユーザーは本書で既出の 1:5 の式に従う必要はありません。 

      * 複合組織が Azure AD を使用していないか、適切なライセンスを持っていない: この場合は、このドキュメントで既出の 1:5 の式に従います。

## <a name="next-steps"></a>次のステップ

Azure AD B2B コラボレーションに関する他の記事を参照してください。

* [Azure AD B2B コラボレーションとは](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Azure Active Directory 管理者が B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-admin-add-users.md)
* [インフォメーション ワーカーが B2B コラボレーション ユーザーを追加する方法](active-directory-b2b-iw-add-users.md)
* [B2B コラボレーションの招待メールの要素](active-directory-b2b-invitation-email.md)
* [B2B コラボレーションの招待の利用](active-directory-b2b-redemption-experience.md)
* [Azure Active Directory B2B コラボレーションのトラブルシューティング](active-directory-b2b-troubleshooting.md)
* [Azure Active Directory B2B コラボレーションに関してよく寄せられる質問 (FAQ)](active-directory-b2b-faq.md)
* [Azure Active Directory B2B コラボレーションの API とカスタマイズ](active-directory-b2b-api.md)
* [B2B コラボレーション ユーザーの多要素認証](active-directory-b2b-mfa-instructions.md)
* [招待を使用せずに B2B コラボレーション ユーザーを追加する](active-directory-b2b-add-user-without-invite.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)

