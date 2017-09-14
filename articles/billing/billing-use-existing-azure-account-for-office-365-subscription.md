---
title: "Azure アカウントで Office 365 にサインアップする | Microsoft Docs"
description: "Azure アカウントを使用して、Office 365 サブスクリプションを作成する方法を説明します"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: 
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/03/2017
ms.author: cjiang
ms.translationtype: HT
ms.sourcegitcommit: 9633e79929329470c2def2b1d06d95994ab66e38
ms.openlocfilehash: 1c6e277e321980aaf30f821dbb41c7eaf296b4cf
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Azure アカウントで Office 365 サブスクリプションにサインアップする
Azure サブスクライバ―は、Azure アカウントを使用して、Office 365 サブスクリプションにサインアップできます。 Azure サブスクリプションがある組織に属している場合は、既存の Azure Active Directory (Azure AD) にユーザーの Office 365 サブスクリプションを作成できます。 Azure Active Directory テナントでグローバル管理者または課金管理者権限を持っているアカウントを使用して、Office 365 サブスクリプションにサインアップします。 詳細については、「[Azure AD でアカウントのアクセス許可を確認する](#RoleInAzureAD)」と「[Azure Active Directory の管理者ロールの割り当て](../active-directory/active-directory-assign-admin-roles.md)」を参照してください。

既に Office 365 アカウントと Azure サブスクリプションの両方がある場合は、[Office 365 テナントの Azure サブスクリプションへの関連付け](billing-add-office-365-tenant-to-azure-subscription.md)を行うことができます。

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Azure アカウントを使用して Office 365 サブスクリプションを取得する

1. [Office 365 の製品ページ](https://products.office.com/business)に移動し、プランを選択します。
2. ページの右上隅にある **[サインイン]** をクリックします。

    ![Office 365 試用版ページのスクリーン ショット](./media/billing-use-existing-azure-account-office-365-subscription/12-office-365-trial-page.png)
3. Azure アカウントの資格情報でサインインします。 組織のサブスクリプションを作成する場合は、Azure Active Directory テナントでグローバル管理者または課金管理者ディレクトリ ロールのメンバーである Azure アカウントを使用します。

    ![Office 365 のサインインのスクリーン ショット](./media/billing-use-existing-azure-account-office-365-subscription/13-office-365-sign-in.png)
4. **[今すぐ試す]**をクリックします。

    ![Office 365 の注文確認のスクリーン ショット](./media/billing-use-existing-azure-account-office-365-subscription/14-office-365-confirm-your-order.png)
5. 注文の受領書ページで、 **[続行]**をクリックします。

    ![Office 365 の注文受付のスクリーン ショット](./media/billing-use-existing-azure-account-office-365-subscription/15-office-365-order-receipt.png)

設定が完了しました。 組織のための Office 365 サブスクリプションを作成した場合は、次の手順を使用して、Azure AD ユーザーが Office 365 ユーザーになっていることを確認するます。

1. Office 365 管理センターを開きます。
2. **[ユーザー]** を展開し、**[アクティブ ユーザー]** をクリックします。

    ![Office 365 管理センターのユーザーのスクリーンショット](./media/billing-use-existing-azure-account-office-365-subscription/16-office-365-admin-center-users.png)

サインアップが完了すると、Azure サブスクリプションが属しているのと同じ Azure Active Directory に Office 365 サブスクリプションが追加されます。 詳細については、「[Azure サブスクリプションと Office 365 サブスクリプションの背景情報](billing-use-existing-office-365-account-azure-subscription.md#more-about-subs)」と「[Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

## <a id="RoleInAzureAD"></a>Azure AD でアカウントのアクセス許可を確認する
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[その他のサービス]** をクリックし、**[Active Directory]** を検索します。

    ![Azure ポータルでの Active Directory のスクリーンショット](./media/billing-use-existing-azure-account-office-365-subscription/billing-more-services-active-directory.png)
3. **[ユーザーとグループ]** > **[すべてのユーザー]** をクリックします。
4. ユーザー名を選択します。 

    ![Azure Active Directory ユーザーの表示のスクリーン ショット](./media/billing-use-existing-azure-account-office-365-subscription/billing-users-groups.png)

5. **[ディレクトリ ロール]** をクリックします。
  
    ![Azure ポータルでのディレクトリ ロールの表示のスクリーン ショット](./media/billing-use-existing-azure-account-office-365-subscription/billing-user-directory-role.png)
6.  既存の Azure Active Directory にユーザーの Office 365 サブスクリプションを作成するには、**グローバル管理者**ロール、または**制限付き管理者** > **課金管理者**ロールが必要です。

    ![Azure ポータルでの課金管理者ディレクトリ ロールの表示のスクリーン ショット](./media/billing-use-existing-azure-account-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。 

