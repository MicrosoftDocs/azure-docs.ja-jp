---
title: ゲスト ユーザーとして組織を脱退する - Azure Active Directory | Microsoft Docs
description: Azure AD B2B のゲスト ユーザーがアクセス パネルから組織を脱退する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26d9eb883cc014c1bea092a12e22b6d144a37994
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67112995"
---
# <a name="leave-an-organization-as-a-guest-user"></a>ゲスト ユーザーとして組織を脱退する

組織からアプリを利用する必要がなくなるか、関連付けを維持する必要がなくなった場合、Azure Active Directory (Azure AD) B2B のゲスト ユーザーはいつでも組織を脱退する決定をすることができます。 ユーザーは、管理者に連絡することなく独自の判断で組織を脱退することができます。

> [!NOTE]
> ホーム テナントまたはリソース テナントのいずれかでアカウントが無効な場合、ゲスト ユーザーは組織を離れることができません。 アカウントが無効な場合は、ゲスト ユーザーがテナント管理者に連絡する必要があります。テナント管理者は、ゲスト アカウントを削除するか、ゲスト アカウントを有効にしてユーザーが組織を離れられるようにします。

## <a name="leave-an-organization"></a>組織を脱退する

組織を脱退するには、以下の手順を実行します。

1. 次のいずれかの手順を実行して、[アクセス パネル プロファイル] ページに移動します。
   
   - [Azure portal](https://portal.azure.com) で右上の自分の名前をクリックし、 **[View account]\(アカウントの表示\)** を選択します。
   - [アクセス パネル](https://myapps.microsoft.com)を開き、右上の自分の名前をクリックし、 **[組織]** の横にある設定アイコン (歯車) を選択します。
 
   ![アクセス パネルのユーザー設定のスクリーンショット](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > 脱退する組織にまだサインインしていない場合、 **[組織]** の下で、組織名の横にある **[サインインして組織から脱退する]** リンクをクリックします。 サインインしたら、右上の **[組織]** の横にある自分の名前をもう一度クリックし、設定アイコン (歯車) を選択します。

3. **Organizations** のところで脱退する組織を確認し、**Leave organization** を選択します。

   ![ユーザー インタフェースにある組織を脱退するオプションのスクリーンショット](media/leave-the-organization/LeaveOrg.png)

4. 確認を求められたら、**Leave** を選択します。 

## <a name="account-removal"></a>アカウントの削除

組織を脱退すると、そのユーザーのアカウントはディレクトリから "論理的に削除" されます。 既定では、ユーザーのオブジェクトは Azure AD の **[削除済みのユーザー]** エリアに移動しますが、30日の間は完全には削除されません。 この論理的削除により、ユーザーが 30 日の期間内にアカウントの復活を要求した場合、管理者はそのユーザー アカウント （グループおよびアクセス許可を含む） を復活させることができます。

30 日の期間中、テナント管理者はいつでも必要に応じてアカウントを完全削除できます。 これを行うには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]** を選択します。
2. **[管理]** にある **[Users]** を選択します。
3. **Deleted users** を選択します。
4. 削除済みユーザーの横のチェック ボックスを選択して、**Delete permanently** を選択します。

ユーザーを完全削除した場合、操作の取り消しはできません。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>次の手順

- Azure AD B2B の概要については、「[Azure AD B2B コラボレーションとは](what-is-b2b.md)」を参照してください。



