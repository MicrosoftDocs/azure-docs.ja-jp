---
title: "Azure Active Directory での条件付きアクセスの基本 - プレビュー | Microsoft Docs"
description: "場所の条件を使用して、条件付きアクセスをテストします。"
services: active-directory
keywords: "アプリへの条件付きアクセス, Azure AD での条件付きアクセス, 企業リソースへの安全なアクセス, 条件付きアクセス ポリシー"
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/23/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: ae60cd810b4beb26f714e2ea2566e5354c482fa8
ms.openlocfilehash: 3022f5e27ddaf125161bbf7132b41a27024f40b6
ms.lasthandoff: 01/19/2017


---
# <a name="get-started-with-conditional-access-in-azure-active-directory---preview"></a>Azure Active Directory での条件付きアクセスの基本 - プレビュー

このトピックで説明する動作は、現在[プレビュー段階](active-directory-preview-explainer.md)にあります。

条件付きアクセスは、条件の定義を可能にする Azure Active Directory の&1; 機能で、この条件では、承認されたユーザーがアプリにアクセスできるようになります。 

このトピックでは、環境内の場所の条件に基づいて条件付きアクセスをテストするための手順を説明します。  


## <a name="scenario-description"></a>シナリオの説明

多くの組織で共通する要件の&1; つとして、企業のイントラネットから実行されないアプリへのアクセスに対してのみ多要素認証が必要になることが挙げられます。 Azure Active Directory を使えば、場所ベースの条件付きアクセス ポリシーを構成して、この目標を簡単に実現できます。 このトピックでは、関連するポリシーを構成するための詳細な手順を説明します。 ポリシーは[信頼できる IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) を利用して、企業のイントラネットからのアクセス試行と他のすべての場所からのアクセス試行を区別します。


## <a name="prerequisites"></a>前提条件

このトピックで説明されたシナリオでは、「[Azure Active Directory conditional access](active-directory-conditional-access-azure-portal.md)」(Azure Active Directory の条件付きアクセス) に掲載された概念を把握していることを前提としています。

このシナリオのテストでは、以下のことが必要です。

- テスト ユーザーの作成 

- Azure AD Premium ライセンスをテスト ユーザーに割り当てる

- 管理されたアプリを構成してテスト ユーザーを割り当てる

- 信頼できる IP の構成

信頼できる IP について詳しくは、「[信頼できる IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)」をご覧ください。


## <a name="policy-configuration-steps"></a>ポリシーの構成の手順

**条件付きアクセス ポリシーを構成するには、次の操作を実行します。**

1. Azure Portal の左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。 

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. **[Azure Active Directory]** ブレードの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. **[条件付きアクセス]** ブレードで、**[新規]** ブレードを開くために、ツール バーの上部の **[追加]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. **[新規]** ブレードの **[名前]** テキストボックスにポリシーの名前を入力します。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. **[割り当て]** セクションで **[ユーザーとグループ]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. **[ユーザーとグループ]** ブレードで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. **[ユーザーとグループの選択]** をクリックします。

    b. **[選択]**をクリックします。

    c. **[選択]** ブレードでテスト ユーザーを選択し、**[選択]** をクリックします。

    d. **[ユーザーとグループ]** ブレードで、**[完了]** をクリックします。

7. **[新規]** ブレードで **[クラウド アプリ]** ブレードを開くために、**[割り当て]** セクションで **[クラウド アプリ]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. **[クラウド アプリ]** ブレードで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. **[アプリを選択]** をクリックします。

    b. **[選択]**をクリックします。

    c. **[選択]** ブレードでクラウド アプリを選択し、**[選択]** をクリックします。

    d. **[クラウド アプリ]** ブレードで、**[完了]** をクリックします。

9. **[新規]** ブレードで **[条件]** ブレードを開くために、**[割り当て]** セクションで **[条件]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. **[条件]** ブレードで、**[場所]** ブレードを開くために **[場所]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. **[場所]** ブレードで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. **[構成]** で、**[はい]** をクリックします。

    b. **[含む]** で **[All locations (すべての場所)]** をクリックします。

    c. **[除外]**、**[All trusted IPs (信頼できるすべての IP)]** の順にクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. **[Done]**をクリックします。

12. **[条件]** ブレードで、**[完了]** をクリックします。

13. **[新規]** ブレードで、**[許可]** ブレードを開くために、**[コントロール]** セクションで **[許可]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. **[Grant (許可)]** ブレードで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. **[多要素認証が必要です]** を選択します。

    b. **[選択]**をクリックします。

15. **[新規]** ブレードの **[ポリシーの有効化]** で、**[On (オン)]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. **[新規]** ブレードで **[作成]** をクリックします。


## <a name="testing-the-policy"></a>ポリシーをテストする

ポリシーをテストするには、次のようなデバイスからアプリにアクセスする必要があります。 

1. 構成された信頼できる IP 内にある IP アドレスを持つデバイス 

1. 構成された信頼できる IP 内にない IP アドレスを持つデバイス

多要素認証は、構成された信頼できる IP 内にないデバイスから実行した接続試行時にのみ必要となるはずです。 


## <a name="next-steps"></a>次のステップ

条件付きアクセスの詳細については、[Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)に関する記事を参照してください。


