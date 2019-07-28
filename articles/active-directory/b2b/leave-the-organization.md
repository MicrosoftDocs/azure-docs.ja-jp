---
title: ゲスト ユーザーとして組織を脱退する - Azure Active Directory | Microsoft Docs
description: Azure AD B2B のゲスト ユーザーがアクセス パネルから組織を脱退する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: b2b
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f5164130c5850b37d1e1323d172f9f9c46b4b30
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2019
ms.locfileid: "65768297"
---
# <a name="leave-an-organization-as-a-guest-user"></a>ゲスト ユーザーとして組織を脱退する

組織からアプリを利用する必要がなくなるか、関連付けを維持する必要がなくなった場合、Azure Active Directory (Azure AD) B2B のゲスト ユーザーはいつでも組織を脱退する決定をすることができます。 ユーザーは、管理者に連絡することなく独自の判断で組織を脱退することができます。

## <a name="leave-an-organization"></a>組織を脱退する

組織を脱退するには、以下の手順を実行します。

1. 次のいずれかを実行して、[Access Panel Profile]\(アクセス パネル プロファイル\) ページに移動します。
   
   - [Azure portal](https://portal.azure.com) で右上の自分の名前をクリックし、**[View account]\(アカウントの表示\)** を選択します。
   - [アクセス パネル](https://myapps.microsoft.com)を開き、右上の自分の名前をクリックし、**[組織]** の横にある設定アイコン (歯車) を選択します。
 
   ![アクセス パネルのユーザー設定のスクリーンショット](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > 脱退する組織にまだサインインしていない場合、**[組織]** の下で、組織名の横にある **[サインインして組織から脱退する]** リンクをクリックします。 サインインしたら、右上の **[組織]** の横にある自分の名前をもう一度クリックし、設定アイコン (歯車) を選択します。

3. **Organizations** のところで脱退する組織を確認し、**Leave organization** を選択します。

   ![ユーザー インタフェースにある組織を脱退するオプションのスクリーンショット](media/leave-the-organization/LeaveOrg.png)

4. 確認を求められたら、**Leave** を選択します。 

## <a name="account-removal"></a>アカウントの削除

組織を脱退すると、そのユーザーのアカウントはディレクトリから "論理的に削除" されます。 既定では、ユーザーのオブジェクトは Azure AD の **Deleted users** エリアに移動しますが、30日の間は完全には削除されません。 この論理的削除により、ユーザーが 30 日の期間内にアカウントの復活を要求した場合、管理者はそのユーザー アカウント （グループおよびアクセス許可を含む） を復活させることができます。

30 日の期間中、テナント管理者はいつでも必要に応じてアカウントを完全削除できます。 これを行うには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、**[Azure Active Directory]** を選択します。
2. **[管理]** にある **[Users]** を選択します。
3. **Deleted users** を選択します。
4. 削除済みユーザーの横のチェック ボックスを選択して、**Delete permanently** を選択します。

ユーザーを完全削除した場合、操作の取り消しはできません。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次の手順

- Azure AD B2B の概要については、「[Azure AD B2B コラボレーションとは](what-is-b2b.md)」を参照してください。



