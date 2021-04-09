---
title: 組織のプライバシー情報の追加 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory Properties エリアに組織のプライバシー情報を追加する方法について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
ms.date: 04/17/2018
ms.author: ajburnle
ms.reviewer: bpham
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f47ae9b087615a77f6bd4d3f14f1b0052037cba6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996758"
---
# <a name="add-your-organizations-privacy-info-using-azure-active-directory"></a>Azure Active Directory を使用して組織のプライバシー情報を追加する
この記事では、テナント管理者が Azure portal を使用して、組織の Azure Active Directory (Azure AD) テナントにプライバシー関連の情報を追加する方法について説明します。

社内の従業員と外部のゲストがポリシーを確認できるように、グローバル プライバシー連絡先と組織のプライバシーに関する声明の両方を追加することを強くお勧めします。 プライバシーに関する声明はそれぞれの会社に合わせて独自に作成､変更されるため､弁護士に相談することを強くお勧めします｡

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="add-your-privacy-info-on-azure-ad"></a>Azure AD でプライバシー情報を追加する
組織のプライバシー情報は、Azure AD の **[Properties]** エリアで追加します。

### <a name="to-access-the-properties-area-and-add-your-privacy-information"></a>[Properties] エリアにアクセスしてプライバシー情報を追加するには

1. グローバル管理者として Azure Portal に サインインします。

2. 左側のナビゲーション バーで **Azure Active Directory** を選択して､**Properties** を選択します｡

    **Properties** エリアが表示されます｡

    ![Azure AD Properties エリア - プライバシー情報を強調表示した状態](media/active-directory-properties-area/properties-area.png)

3. 従業員のプライバシー情報を追加します｡

    - **技術部連絡先｡** 社内の技術的なサポートを受けるさいの連絡先の電子メール アドレスを入力します｡
    
    - **グローバル プライバシー連絡先｡** 個人データのプライバシーに関する問い合わせをするさいの連絡先の電子メール アドレスを入力します｡ この連絡先は､データに関する違反があった場合に Microsoft が問い合わせを行う連絡先でもあります｡ 問い合わせの記載がない場合､Microsoft はグローバル管理者に問い合わせます｡

    - **プライバシーに関する声明 URL｡** 組織が社内と外部のゲストの両方のデータのプライバシーをどのように処理するかを説明している組織のドキュメントへのリンクを入力します。

        >[!Important]
        >プライバシーに関する独自の声明とプライバシー連絡先のどちらも含めていない場合は、外部のゲストの **[アクセス許可の確認]** ボックスに、 **<_組織名_> は、ユーザーが確認する使用条件へのリンクを提供していません** というテキストが表示されます。 たとえば､このメッセージは､B2B コラボレーションでゲスト ユーザーが組織にアクセスするための招待を受けたときに表示されます｡

        ![B2B Collaboration Review Permissions ボックスとメッセージ](media/active-directory-properties-area/active-directory-no-privacy-statement-or-contact.png)

4. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ
- [Azure Active Directory B2B コラボレーションの招待の利用](../external-identities/redemption-experience.md)
- [Azure Active Directory でユーザーのプロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)