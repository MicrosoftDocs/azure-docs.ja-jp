---
title: "Azure Active Directory Identity Protection の有効化 | Microsoft Docs"
description: "Azure Active Directory Identity Protection を有効にする方法について説明します。"
services: active-directory
keywords: "Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: f7a7ffaf-76bf-4cc7-96a1-86c944275c82
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: f231252c222c5520d0a66f9b40749bd203545f16
ms.openlocfilehash: 44ee1e58b65b23eeb3fa3710fcb29a1646d5f3ad
ms.lasthandoff: 01/12/2017


---
# <a name="enabling-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection の有効化
Azure Active Directory Identity Protection は、不審なサインイン アクティビティと潜在的な脆弱性に関する統合ビューを提供し、通知、修復に関するアドバイス、リスクベースのポリシーによってビジネスを保護する新しい機能です。 

このサービスは、ブルート フォース攻撃、資格情報の漏えい、不明な場所からのサインイン、感染したデバイスなどの前兆に基づいて、エンド ユーザーや特権 (管理) ID の不審なアクティビティを検出し、これらのアクティビティからリアルタイムで保護します。 さらに重要なことですが、これらの不審なアクティビティに基づいてユーザーのリスクの重要度が計算されるので、リスクベースのポリシーを構成し、組織の ID を自動的に保護することができます。 詳細については、「 [Azure Active Directory Identity Protection](active-directory-identityprotection.md)」をご覧ください。

このトピックでは、Azure Active Directory Identity Protection を有効にする方法について説明します。

## <a name="steps-to-enable-azure-active-directory-identity-protection"></a>Azure Active Directory Identity Protection を有効にする手順
1. Azure Portal に全体管理者として[サインオン](https://ms.portal.azure.com/)します。 
2. Azure Portal で **[Marketplace]**をクリックします。
   
    ![作成](./media/active-directory-identityprotection-enable/01.png "作成")
3. アプリケーションの一覧で **[セキュリティ + ID]**をクリックします。
   
    ![作成](./media/active-directory-identityprotection-enable/02.png "作成")
4. **[Azure AD Identity Protection]**をクリックします。
   
    ![作成](./media/active-directory-identityprotection-enable/03.png "作成")
5. **[Azure AD Identity Protection]** ブレードで、**[作成]** をクリックします。
   
    ![作成](./media/active-directory-identityprotection-enable/04.png "作成")

## <a name="next-steps"></a>次のステップ
* [Azure Active Directory Identity Protection](active-directory-identityprotection.md)


