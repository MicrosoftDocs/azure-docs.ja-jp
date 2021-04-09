---
title: Azure AD B2C における Identity Protection と条件付きアクセス
description: Identity Protection を使用して、危険なサインインとリスク検出を可視化する方法について説明します。 条件付きアクセスを使用し、Azure AD B2C テナントでのリスク イベントに基づいて組織のポリシーを適用する方法について見ていきましょう。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 03/03/2021
ms.author: mimart
author: msmimart
manager: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2ec8af824d573493d1da743923956e587ded62e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033828"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C における Identity Protection と条件付きアクセス

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

Azure AD Identity Protection と条件付きアクセスを使用して、Azure Active Directory B2C (Azure AD B2C) のセキュリティを強化しましょう。 Azure AD B2C テナントには、危険なユーザーや危険なサインインなど、Identity Protection のリスク検出機能が自動的に検出されて表示されます。 それらのリスク検出を使用してアクションを決定したり組織のポリシーを適用したりする条件付きアクセス ポリシーを作成することができます。 Azure AD B2C アプリケーションの所有者は、こうした機能を組み合わせて、危険な認証やアクセス ポリシーをより厳しく管理することができます。
  
既に Azure AD で [Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) と[条件付きアクセス](../active-directory/conditional-access/overview.md)を使ったことがあれば、Azure AD B2C でも、同じようにこれらの機能を使用できます。両者には、若干の違いもありますが、その点については、この記事で説明しています。

![B2C テナントにおける条件付きアクセス](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> 条件付きアクセスを使用するには、Azure AD B2C Premium P2 が必要です。

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C における Identity Protection と条件付きアクセスの利点  

条件付きアクセス ポリシーと Identity Protection のリスク検出とを組み合わせると、危険な認証に対して適切なポリシー アクションで対応することができます。

- **アプリや顧客ベースの認証リスクをこれまでにないレベルまで可視化**。 Azure AD と Microsoft アカウントに対する認証は毎月数十億件にも上ります。それらの認証からのシグナルにより、リスク検出アルゴリズムは、ローカルのコンシューマーや一般ユーザーの認証のリスクを低、中、高に分けてフラグを設定します。
- **独自のアダプティブ認証を構成することによってリスクに自動的に対応**。 特定のアプリケーションにアクセスする、特定のユーザーのグループには、多要素認証 (MFA) と同様、第 2 の認証要素の提供を求めることができます。 または、検出されたリスク レベルに基づいてアクセスをブロックすることもできます。 最終的なエンドユーザー エクスペリエンスは、Azure AD B2C の他のエクスペリエンスと同様、組織の考えや方針、ブランドによってカスタマイズすることができます。 アクセスを得られなかったユーザーに、リスクを軽減する他の手段を表示することもできます。
- **場所、グループ、アプリに基づいてアクセスを制御**。  リスクに基づく状況以外でも、条件付きアクセスを使用して制御できます。 たとえば、特定のアプリにアクセスする顧客に MFA を求めたり、指定された地域からのアクセスをブロックしたりすることができます。
- **Azure AD B2C のユーザー フローや Identity Experience Framework のカスタム ポリシーと連携**。 既にあるカスタマイズしたエクスペリエンスを使用し、条件付きアクセスと連携させる必要のある制御を追加します。 アクセス権を付与するための高度なシナリオを実現することもできます (ナレッジベースのアクセス、独自に選んだ MFA プロバイダーなど)。

## <a name="feature-differences-and-limitations"></a>機能の相違と制限

通常、Azure AD B2C における Identity Protection と条件付きアクセスは、Azure AD での場合と同じように機能しますが、次の例外があります。

- Azure AD B2C では Security Center が利用できません。

- Azure AD B2C テナントの ROPC サーバー間フローでは、Identity Protection と条件付きアクセスはサポートされません。

- Azure AD B2C テナントでは、Identity Protection のリスク検出が利用できるのはローカル B2C アカウントのみで、Google や Facebook などのソーシャル ID では利用できません。

- Azure AD B2C テナントで利用できるのは、Identity Protection のリスク検出のサブセットです。 [ID 保護によるリスクの調査](identity-protection-investigate-risk.md)および[ユーザー フローへの条件付きアクセスの追加](conditional-access-user-flow.md)に関する記事をご覧ください。

- Azure AD B2C テナントでは、条件付きアクセスのデバイス コンプライアンス機能は利用できません。


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>ユーザー フローやカスタム ポリシーに条件付きアクセスを統合する

Azure AD B2C では、組み込みのユーザー フローから条件付きアクセスの条件をトリガーすることができます。 カスタム ポリシーに条件付きアクセスを組み込むこともできます。 B2C ユーザー フローの他の部分と同様、エンドユーザー エクスペリエンスのメッセージングも、組織の考えやブランド、リスクを軽減する代替手段に応じてカスタマイズすることができます。 [ユーザー フローへの条件付きアクセスの追加](conditional-access-user-flow.md)に関する記事をご覧ください。

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Azure AD B2C の条件付きアクセス ポリシーは、Microsoft Graph API で管理することもできます。 詳細については、[条件付きアクセスに関するドキュメント](../active-directory/conditional-access/overview.md)および [Microsoft Graph 操作](microsoft-graph-operations.md#conditional-access)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ

- [ユーザー フローに条件付きアクセスを追加する](conditional-access-user-flow.md)
- [Azure AD の Identity Protection について学ぶ](../active-directory/identity-protection/overview-identity-protection.md)
- [条件付きアクセスについて学ぶ](../active-directory/conditional-access/overview.md)