---
title: Azure Active Directory の条件付きアクセスにおけるベースラインの保護とは  (プレビュー) | Microsoft Docs
description: ベースラインの保護により、Azure Active Directory 環境で少なくともベースライン レベルのセキュリティを有効にする方法について説明します。
services: active-directory
keywords: アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.subservice: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2018
ms.author: joflore
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b3d8b1daef9f780bbba53f33ae451c63f80249b
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519767"
---
# <a name="what-is-baseline-protection-preview"></a>ベースラインの保護とは (プレビュー)  

昨年は ID 攻撃が 300% 増加しました。 ますます増加する攻撃から環境を守るために、Azure AD (Azure AD) にはベースラインの保護と呼ばれる新しい機能が導入されています。 ベースラインの保護は、事前に定義された[条件付きアクセス ポリシー](../active-directory-conditional-access-azure-portal.md)のセットです。 これらのポリシーの目標は、少なくともベースライン レベルのセキュリティを Azure AD のすべてのエディションで有効にすることです。 

この記事では、Azure Active Directory のベースラインの保護について概要を説明します。


 
## <a name="require-mfa-for-admins"></a>Require MFA for admins \(管理者に MFA を要求する\)

特権アカウントにアクセスできるユーザーは、環境に無制限にアクセスできます。 これらのアカウントには権限があるので、特別な注意を払って対処する必要があります。 特権アカウントの保護を向上するための一般的な方法の 1 つは、特権アカウントがサインインに使用されるときに、強力な形式のアカウント検証を必須にすることです。 Azure Active Directory では、多要素認証 (MFA) を必須にすることで、アカウント検証を強力にすることができます。  

**[Require MFA for admins]\(管理者に MFA を要求する\)** は、以下のディレクトリ ロールに MFA を要求するベースライン ポリシーです。 

- 全体管理者  

- SharePoint 管理者  

- Exchange 管理者  

- 条件付きアクセス管理者  

- セキュリティ管理者  


![Azure Active Directory](./media/baseline-protection/01.png)

このベースライン ポリシーを使用すると、ユーザーを除外できるようになります。 テナントからロックアウトされないように、1 つの *[緊急アクセス管理アカウント](../users-groups-roles/directory-emergency-access.md)* を除外することができます。


## <a name="enable-a-baseline-policy"></a>ベースライン ポリシーを有効にする 

ベースライン ポリシーはプレビュー段階であり、既定では有効ではありません。 このポリシーを有効にするには、手動で有効にする必要があります。 プレビュー段階で明示的にベースライン ポリシーを有効にすると、この機能が一般公開に達したときに、それらのポリシーはアクティブなままになります。 動作変更が予定されているため、アクティブにするオプションと非アクティブ化するオプションに加えて、ポリシーの状態を設定する 3 つ目のオプション、**[将来、ポリシーを自動的に有効化]** が用意されています。 このオプションを選択すると、プレビュー中はポリシーを無効のままにして、この機能が一般公開に達したときに、Microsoft が自動的に有効にするようにできます。 現在ベースライン ポリシーを明示的に有効にしていない場合は、**[将来、ポリシーを自動的に有効化]** オプションを選択しないでください。この機能が一般公開に達したときに、ポリシーは無効のままになります。


**ベースライン ポリシーを有効にするには:**  

1. [Azure portal](https://portal.azure.com) にグローバル管理者、セキュリティ管理者、または条件付きアクセス管理者としてサインインします。

2. **Azure Portal** の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. **[Azure Active Directory]** ページの **[セキュリティ]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/baseline-protection/05.png)

4. ポリシーの一覧で、**ベースライン ポリシー**から始まるポリシーをクリックします。 

5. ポリシーを有効にするには、**[ポリシーをすぐに使用する]** をクリックします。

6. **[Save]** をクリックします。 
 
  
 

## <a name="what-you-should-know"></a>知っておくべきこと 

カスタムの条件付きアクセス ポリシーを管理するには Azure AD Premium ライセンスが必要ですが、ベースライン ポリシーは Azure AD のすべてのエディションで利用できます。     

ベースライン ポリシーに含まれるディレクトリ ロールは、最も特権のある Azure AD ロールです。 

スクリプトで使用されている特権アカウントを持っている場合は、[Azure リソースのマネージド ID](../managed-identities-azure-resources/overview.md) または[サービス プリンシパルと証明書](../develop/howto-authenticate-service-principal-powershell.md)で置き換える必要があります。 一時的な回避策として、ベースライン ポリシーから特定のユーザー アカウントを除外することができます。 

ベースライン ポリシーは、POP、IMAP、古い Office デスクトップ クライアントなどの従来の認証フローに適用されます。 




## <a name="next-steps"></a>次の手順

詳細については、次を参照してください。

- [ID インフラストラクチャをセキュリティ保護する 5 つのステップ](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [Azure Active Directory の条件付きアクセスとは](overview.md) 

