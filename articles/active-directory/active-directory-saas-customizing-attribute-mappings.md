---
title: "Azure AD 属性マッピングのカスタマイズ | Microsoft Docs"
description: "Azure Active Directory における SaaS アプリの属性マッピングとは何かと、この属性マッピングをビジネス ニーズに合わせて変更する方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ca2fdc9c68ea0030d938eeaebd57aafa0e2790f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Azure Active Directory の SaaS アプリケーションに対するユーザー プロビジョニング属性マッピングのカスタマイズ
Microsoft Azure AD では、Salesforce、Google Apps、およびその他のサードパーティの SaaS アプリケーションへのユーザー プロビジョニングのサポートを提供します。 サード パーティの SaaS アプリケーションでユーザー プロビジョニングを有効にした場合、Microsoft Azure 管理ポータルでは "属性マッピング" と呼ばれる構成の形でその属性値を管理します。

Azure AD ユーザー オブジェクトと各 SaaS アプリのユーザー オブジェクトの間には、構成済みの一連の属性マッピングが存在します。 アプリによっては、グループや連絡先といった他のタイプのオブジェクトを管理するものもあります。 <br> 
 既定の属性マッピングをビジネスのニーズに合わせてカスタマイズできます。 つまり、既存の属性マッピングを変更、削除したり、新規の属性マッピングを作成したりすることができます。

Azure AD ポータルでこの機能にアクセスするには、**[エンタープライズ アプリケーション]** の **[管理]** セクションの **[プロビジョニング]** にある **[マッピング]** 構成をクリックします。


![Salesforce][5] 

**[マッピング]** 構成をクリックして、関連する **[属性マッピング]** ブレードを開きます。  
SaaS アプリケーションが正常に機能するために必要となる属性マッピングがあります。 必須の属性の場合は、**[削除]** 機能を使用できません。


![Salesforce][6]  

上の例で、Salesforce の管理オブジェクトの **Username** 属性には、リンクされている Azure Active Directory オブジェクトの **userPrincipalName** 値が設定されています。

既存の **[属性マッピング]** をカスタマイズするには、マッピングをクリックします。 これで **[属性の編集]** ブレードが開きます。

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>属性マッピングの種類の概要
属性マッピングでは、サードパーティの SaaS アプリケーションに属性を設定する方法を管理します。 次の 4 つの異なるマッピングの種類がサポートされます。

* **直接** - ターゲットの属性に、Azure AD でリンクされているオブジェクトの属性値を設定します。
* **定数** – ターゲットの属性に、指定した特定の文字列を設定します。
* **式** - ターゲットの属性を、スクリプトのような式の結果に基づいて設定します。 
  詳細については、「[Azure Active Directory における属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)」を参照してください。
* **なし** - ターゲットの属性を変更しません。 ただし、ターゲットの属性が空の場合は、指定した既定値が設定されます。

これら 4 つの基本的な属性マッピングの種類に加えて、カスタム属性マッピングではオプションの**既定**値の割り当てという概念をサポートします。 既定値の割り当てでは、Azure AD にもターゲット オブジェクトにも値がない場合にも、ターゲットの属性にかならず値を設定します。 最も一般的な構成では、これを空白のままにします。


## <a name="understanding-attribute-mapping-properties"></a>属性マッピングのプロパティの概要

前のセクションでは、属性マッピングの種類プロパティについて紹介しました。
属性マッピングは、このプロパティ以外にも次の属性もサポートしています。

- **ソース属性** - ソース システムのユーザー属性 (例: Azure Active Directory)。
- **対象の属性** - ターゲット システムのユーザー属性 (例: ServiceNow)。
- **この属性を使用してオブジェクトを照合する** - このマッピングを使用して、ソースとターゲットのシステム間でユーザーを一意に識別するかどうかを示します。 通常、これは Azure AD の userPrincipalName または mail 属性で設定され、一般的にターゲット アプリケーションの username フィールドにマップされます。
- **照合の優先順位** - 一致させる属性を複数設定できます。 複数の場合は、このフィールドで定義された順序で評価されます。 1 件でも一致が見つかると、一致する属性の評価はそれ以上行われません。
- **このマッピングを適用する**
    - **常に** - このマッピングをユーザーの作成と更新の両方のアクションに適用します
    - **作成中のみ** - このマッピングをユーザーの作成アクションのみに適用します


## <a name="what-you-should-know"></a>知っておくべきこと

Microsoft Azure AD では、同期プロセスの効率的な実装を提供します。 初期化された環境では、更新が必要なオブジェクトのみが同期サイクル中に処理されます。 属性マッピングの更新は、同期サイクルのパフォーマンスに影響を与えます。 属性マッピングの構成を更新するには、すべての管理オブジェクトを再評価する必要があります。 推奨されるベスト プラクティスとして、属性マッピングに対する連続的な変更の回数は最小限に抑えてください。

## <a name="next-steps"></a>次のステップ

* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)
* [Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](active-directory-saas-app-provisioning.md)
* [属性マッピングの式の書き方](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [ユーザーのプロビジョニング用のフィルターのスコープ](active-directory-saas-scoping-filters.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](active-directory-scim-provisioning.md)
* [アカウント プロビジョニング通知](active-directory-saas-account-provisioning-notifications.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

