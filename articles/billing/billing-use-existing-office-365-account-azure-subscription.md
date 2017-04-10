---
title: "Office 365 アカウントを使用して Azure にサインアップする | Microsoft Docs"
description: "Office 365 Azure AD テナントとそのユーザーを Azure サブスクリプションと共有する方法、またはその逆の方法について説明します。"
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 488ea81fcdad156371b89b2c94ef7c90bb83ca33


---
# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>既存の Office 365 アカウントを Azure サブスクリプションに使用する (またはその逆)
シナリオ: 既に Office 365 サブスクリプションがあり、Azure サブスクリプションを開始するにあたって既存の Office 365 ユーザー アカウントを Azure サブスクリプションに使用したい場合。 または、既に Azure サブスクライバーであり、既存の Azure Active Directory のユーザー用に Office 365 サブスクリプションを取得したい場合。 いずれの方法も簡単です。この記事ではその方法について説明します。

> [!NOTE]
> この記事は、Enterprise Agreement (EA) のお客様には適用されません。 この記事についてさらにヘルプが必要な場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
>
>

## <a name="quick-guidance"></a>クイック ガイド
* 既に Office 365 サブスクリプションがあり、Azure にサインアップする場合、**[組織のアカウントを使用してサインインする]** オプションを使用すると、 現在の Office 365 アカウントで引き続き Azure にサインアップできます。 この記事の「[詳細な手順](#s1)」を参照してください。
* 既に Azure サブスクリプションがあり、Office 365 サブスクリプションを取得する場合は、Azure アカウントで Office 365 にサインインし、 サインアップ手順を続行します。 サインアップが完了すると、Azure サブスクリプションが属しているのと同じ Azure Active Directory に Office 365 サブスクリプションが追加されます。 詳細については、この記事の「[詳細な手順](#s2)」を参照してください。

> [!NOTE]
> Office 365 サブスクリプションを取得するには、サインアップに使用するアカウントが、Azure Active Directory テナントでグローバル管理者または課金管理者ディレクトリ ロールのメンバーである必要があります。 「[Azure Active Directory のロールを確認する方法](#how-to-know-your-role-in-your-azure-active-directory)」を参照してください。
>
>

サブスクリプションをアカウントに追加するときの処理内容については、後の「背景情報」を参照してください。

## <a name="detailed-steps"></a>詳細な手順
<a id="s1"></a>

### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>シナリオ 1: Azure を購入する Office 365 ユーザー
このシナリオでは、Office 365 サブスクリプションがある Kelley Wall というユーザーが、Azure にサブスクライブしようとしています。 その他に、Jane と Tricia という&2; 人のアクティブ ユーザーがいます。 Kelley のアカウントは admin@contoso.onmicrosoft.com です。

![Office 365 ユーザー管理センター](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Azure にサインアップするには、次の手順を実行します。

1. [Azure.com](https://azure.microsoft.com/)で Azure にサインアップします。 **[無料アカウント]** をクリックします。 次のページで、**[無料で始める]** をクリックします。

    ![Azure を無料で試す](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)
2. Office 365 アカウントでサインインします。 この例では Kelly の Office 365 アカウントです。

    ![Office 365 アカウントでサインインします。](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)
3. 情報を入力し、サインアップ プロセスを完了します。

    ![情報を入力し、サインアップを完了します。](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![[サービスの管理を開始する] をクリックします。](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

設定が完了しました。 Azure ポータルに、同じユーザーが表示されます。 確認する手順は次のとおりです。

1. 前に示した画面で **[サービスの管理を開始する]** をクリックします。
2. **[参照]** をクリックし、**[Active Directory]** をクリックします。

    ![[参照]、[Active Directory] の順にクリック](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. **[ユーザー]**をクリックします。

    ![[ユーザー] タブ](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)
4. 想定どおりに Kelley を含む全ユーザーの一覧が表示されます。

    ![ユーザーの一覧](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>

### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>シナリオ 2: Office 365 を購入する Azure ユーザー
このシナリオの Kelley Wall は、アカウント admin@contoso.onmicrosoft.com の Azure サブスクリプションがあるユーザーです。 Kelley は Office 365 にサブスクライブし、Azure と同じ既存のディレクトリを使用したいと考えています。

> [!NOTE]
> Office 365 サブスクリプションを取得するには、サインインに使用するアカウントが、Azure Active Directory テナントでグローバル管理者または課金管理者ディレクトリ ロールのメンバーである必要があります。 「[Azure Active Directory のロールを確認する方法](#how-to-know-your-role-in-your-azure-active-directory)」を参照してください。
>
>

![Azure ポータルのサブスクリプションの設定](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Azure ポータルの Active Directory ユーザー](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Office 365 にサブスクライブするには、次の手順を実行します。

1. [Office 365 製品ページ](https://products.office.com/business)にアクセスし、適切なプランを選択します。
2. プランを選択すると、次のページが表示されます。 フォームには入力しないでください。 ページの右上隅にある **[サインイン]** をクリックします。

    ![Office 365 試用版のページ](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)
3. アカウントの資格情報でサインインします。 この例では Kelly のアカウントです。

    ![Office 365 のサインイン](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)
4. **[今すぐ試す]**をクリックします。

    ![Office 365 の注文を確認](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)
5. 注文の受領書ページで、 **[続行]**をクリックします。

    ![Office 365 の注文受付](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

設定が完了しました。 Office 365 管理センターに、アクティブ ユーザーに表示される Contoso ディレクトリと同じユーザーが表示されます。 確認する手順は次のとおりです。

1. Office 365 管理センターを開きます。
2. **[ユーザー]** を展開し、**[アクティブ ユーザー]** をクリックします。

    ![Office 365 管理センターのユーザー](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Azure Active Directory のロールを確認する方法
1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[参照]** をクリックし、**[Active Directory]** をクリックします。

    ![Azure ポータルの Active Directory](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. **[ユーザー]**をクリックします。

    ![Azure ポータルの既定の Active Directory ユーザー](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)
4. ユーザーをクリックします。 この例では、ユーザーは Kelley Wall です。

    **[組織のロール]**フィールドに注目してください。

    ![Azure ポータルのユーザー ID](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Azure サブスクリプションと Office 365 サブスクリプションの背景情報
Office 365 と Azure では、ユーザーとサブスクリプションの管理に Azure Active Directory サービスが使用されています。 Azure のディレクトリは、ユーザーとサブスクリプションをグループ化できるコンテナーだと考えてください。 Azure サブスクリプションと Office 365 サブスクリプションに同じユーザー アカウントを使用するには、サブスクリプションを同じディレクトリに作成する必要があります。 以下の点に注意してください。

* サブスクリプションは、他の場所ではなく、ディレクトリに作成されます。
* ユーザーは、他の場所ではなく、ディレクトリに属します。
* サブスクリプションは、サブスクリプションを作成したユーザーのディレクトリに属します。 そのため、Azure サブスクリプションを使用して Office 365 サブスクリプションを使用すると、Office 365 サブスクリプションは、Azure サブスクリプションと同じアカウントに関連付けられます。

![背景情報](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../active-directory/active-directory-how-subscriptions-associated-directory.md)」を参照してください。

> [!NOTE]
> Azure サブスクリプションの所有者は、ディレクトリ内の各ユーザーです。
>
> [!NOTE]
> Office 365 サブスクリプションの所有者は、ディレクトリ自体です。 ディレクトリ内のユーザーが必要なアクセス許可を持っている場合は、そのサブスクリプションで操作を行えます。
>
>

## <a name="next-steps"></a>次のステップ
過去に Azure サブスクリプションと Office 365 サブスクリプションを別々に購入したが、今後は Azure サブスクリプションから Office 365 テナントにアクセスする場合は、「[Office 365 テナントの Azure サブスクリプションへの関連付け](billing-add-office-365-tenant-to-azure-subscription.md)」を参照してください。

> [!NOTE]
> まだ他に質問がある場合は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
>
>



<!--HONumber=Feb17_HO2-->


