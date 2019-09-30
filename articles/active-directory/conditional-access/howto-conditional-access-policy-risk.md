---
title: 条件付きアクセス - リスクベースの条件付きアクセス - Azure Active Directory
description: 条件付きアクセス ポリシーを作成して、ポリシーに対する ID 保護強化を有効にします
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cf3281e00560a289fbb4adfe57083644e1a86e3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125441"
---
# <a name="conditional-access-risk-based-conditional-access"></a>条件付きアクセス:リスクベースの条件付きアクセス

Azure AD Premium P2 のライセンスを所持する組織では、Azure AD Identity Protection の検出を組み込んだ条件付きアクセス ポリシーを作成できます。 最初から有効にできる 3 つの既定のポリシーがあります。 

* すべてのユーザーに対して Azure Multi-Factor Authentication への登録を必須にする。
* リスクの高いユーザーに対してパスワードの変更を必須にする。
* サインインのリスクが中以上のユーザーに対して多要素認証を必須にする。

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>すべてのユーザーに対して Azure Multi-Factor Authentication への登録を必須にする

このポリシーを有効にすると、すべてのユーザーが 14 日以内に Azure Multi-Factor Authentication に登録することを求められます。 

1. **Azure Portal** にサインインします。
1. **[すべてのサービス]** をクリックし、 **[Azure AD Identity Protection]** に移動します。
1. **[MFA 登録]** をクリックします。
1. **[割り当て]** で、 **[ユーザー]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[対象外とするユーザーの選択]** を選択し、組織の緊急アクセス用または非常用アカウントを選択して、 **[選択]** を選びます。 
   1. **[完了]** を選択します。
1. **[ポリシーの適用]** を **[オン]** に設定します。
1. **[Save]** をクリックします。

## <a name="require-a-password-change-high-risk-users"></a>リスクの高いユーザーに対してパスワードの変更を必須にする

Microsoft では、研究者、法執行機関、Microsoft のさまざまなセキュリティ チーム、その他の信頼できる情報源と協力して、ユーザー名とパスワードのペアを調査しています。 それらのペアのいずれかが環境内のアカウントに一致すると、次のポリシーを使用して、リスクベースのパスワード変更がトリガーされます。

1. **Azure Portal** にサインインします。
1. **[すべてのサービス]** をクリックし、 **[Azure AD Identity Protection]** に移動します。
1. **[ユーザーのリスク ポリシー]** をクリックします。
1. **[割り当て]** で、 **[ユーザー]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[対象外とするユーザーの選択]** を選択し、組織の緊急アクセス用または非常用アカウントを選択して、 **[選択]** を選びます。
   1. **[完了]** を選択します。
1. **[条件]** の下の **[ユーザーのリスク]** を選択し、 **[高]** を選択します。
   1. **[選択]** 、 **[完了]** の順にクリックします。
1. **[Controls]\(制御\)**  >  **[アクセス]** で、 **[アクセスを許可]** を選択し、 **[パスワードの変更を必須とする]** を選択します。
   1. **[選択]** をクリックします。
1. **[ポリシーの適用]** を **[オン]** に設定します。
1. **[Save]** をクリックします。

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>サインインのリスクが中以上のユーザーに対して MFA を必須にする

ほとんどのユーザーは、追跡できる正常な動作をしています。この規範から外れた場合は、そのユーザーにサインインを許可すると危険であることがあります。 そのユーザーをブロックしたり、多要素認証を実行してユーザーが本人であることを証明するように求めたりすることが必要な場合もあります。 危険なサインインが検出されたときに MFA を必要とするポリシーを有効にするには、次のポリシーを有効にします。

1. **Azure Portal** にサインインします。
1. **[すべてのサービス]** をクリックし、 **[Azure AD Identity Protection]** に移動します。
1. **[サインインのリスク ポリシー]** をクリックします。
1. **[割り当て]** で、 **[ユーザー]** を選択します。
   1. **[Include]\(含める\)** で、 **[すべてのユーザー]** を選択します。
   1. **[除外]** で、 **[対象外とするユーザーの選択]** を選択し、組織の緊急アクセス用または非常用アカウントを選択して、 **[選択]** を選びます。
   1. **[完了]** を選択します。
1. **[条件]** の下の **[Sign-in risk]\(サインインのリスク\)** を選択し、 **[中以上]** を選択します。
   1. **[選択]** 、 **[完了]** の順にクリックします。
1. **[Controls]\(制御\)**  >  **[アクセス]** で、 **[アクセスを許可]** を選択し、 **[Require multi-factor authentication]\(多要素認証を要求する\)** を選択します。
   1. **[選択]** をクリックします。
1. **[ポリシーの適用]** を **[オン]** に設定します。
1. **[Save]** をクリックします。

## <a name="next-steps"></a>次の手順

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

[Simulate sign in behavior using the Conditional Access What If tool](troubleshoot-conditional-access-what-if.md) (条件付きアクセスの What If ツールを使用したサインイン動作のシミュレート)

[動作のしくみ: Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)」を参照してください。

[Azure Active Directory Identity Protection とは](../identity-protection/overview.md)
