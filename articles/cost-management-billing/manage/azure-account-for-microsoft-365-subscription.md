---
title: Azure アカウントを使用した Microsoft 365 のサインアップ
description: Azure アカウントを使用して、Microsoft 365 サブスクリプションを作成する方法を説明します。 また、既存の Azure と Microsoft 365 のアカウントを相互に関連付けることもできます。
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: e2561b6cfce0da2ede9cda567091f9a7e23610de
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682996"
---
# <a name="sign-up-for-a-microsoft-365-subscription-with-your-azure-account"></a>Azure アカウントで Microsoft 365 サブスクリプションにサインアップする

Azure サブスクライバーは、Azure アカウントを使用して、Microsoft 365 サブスクリプションにサインアップできます。 Azure サブスクリプションがある組織に属している場合は、既存の Azure Active Directory (Azure AD) にユーザーの Microsoft 365 サブスクリプションを作成できます。 Azure Active Directory テナントでグローバル管理者または課金管理者権限を持っているアカウントを使用して、Microsoft 365 サブスクリプションにサインアップします。 詳細については、「[Azure AD でアカウントのアクセス許可を確認する](#RoleInAzureAD)」と「[Azure Active Directory の管理者ロールの割り当て](../../active-directory/users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

既に Microsoft 365 アカウントと Azure サブスクリプションの両方がある場合は、[Microsoft 365 テナントを Azure サブスクリプションに関連付け](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)てください。

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Azure アカウントを使用して Microsoft 365 サブスクリプションを取得する

1. [Microsoft 365 の製品ページ](https://www.microsoft.com/microsoft-365/business/all-business)に移動し、プランを選択します。
2. ページの右上隅にある **[サインイン]** をクリックします。

    ![Microsoft 365 試用版ページのスクリーンショット](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Azure アカウントの資格情報でサインインします。 組織のサブスクリプションを作成する場合は、Azure Active Directory テナントでグローバル管理者または課金管理者ディレクトリ ロールのメンバーである Azure アカウントを使用します。

    ![Microsoft のサインインのスクリーンショット](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. **[今すぐ試す]** をクリックします。

    ![Microsoft 365 の注文確認のスクリーンショット](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. 注文の受領書ページで、 **[続行]** をクリックします。

    ![Microsoft 365 の注文受付のスクリーンショット](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

設定が完了しました。 組織のための Microsoft 365 サブスクリプションを作成した場合は、次の手順を使用して、Azure AD ユーザーが Microsoft 365 ユーザーになっていることを確認します。

1. Microsoft 365 管理センターを開きます。
2. **[ユーザー]** を展開し、 **[アクティブ ユーザー]** をクリックします。

    ![Microsoft 365 管理センターのユーザーのスクリーンショット](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

サインアップが完了すると、Azure サブスクリプションが属しているのと同じ Azure Active Directory に Microsoft 365 サブスクリプションが追加されます。 詳細については、「[Azure サブスクリプションと Microsoft 365 サブスクリプションの背景情報](microsoft-365-account-for-azure-subscription.md#more-about-subs)」と「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Azure AD でアカウントのアクセス許可を確認する
1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]** をクリックし、 **[Active Directory]** を検索します。

    ![Azure ポータルでの Active Directory のスクリーンショット](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. **[ユーザーとグループ]**  >  **[すべてのユーザー]** をクリックします。
4. ユーザー名を選択します。

    ![Azure Active Directory ユーザーの表示のスクリーン ショット](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. **[ディレクトリ ロール]** をクリックします。

    ![Azure ポータルでのディレクトリ ロールの表示のスクリーン ショット](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  既存の Azure Active Directory にユーザーの Microsoft 365 サブスクリプションを作成するには、**グローバル管理者**ロール、または**制限付き管理者** > **課金管理者**ロールが必要です。

    ![Azure ポータルでの課金管理者ディレクトリ ロールの表示のスクリーン ショット](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Microsoft 365 テナントを Azure サブスクリプションに関連付ける](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
