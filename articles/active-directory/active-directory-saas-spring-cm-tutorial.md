---
title: "チュートリアル: Azure Active Directory と SpringCM の統合 | Microsoft Docs"
description: "Azure Active Directory で SpringCM を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4a42f797-ac58-4aca-a8e6-53bfe5529083
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 5600adfbc20fb499bdd206e966a9730f49a03e40
ms.lasthandoff: 03/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-springcm"></a>チュートリアル: Azure Active Directory と SpringCM の統合
このチュートリアルでは、Azure Active Directory と SpringCM の間でのシングル サインオン (SSO) を設定する方法を説明します。

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* SpringCM でのシングル サインオン (SSO) が有効なサブスクリプション

このチュートリアルを完了すると、SpringCM に割り当てられている Azure Active Directory ユーザーは、AAD アクセス パネルを使用して SSO ができます。

1. SpringCM のアプリケーション統合の有効化
2. シングル サインオン (SSO) の構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-spring-cm-tutorial/IC797044.png "Scenario")

## <a name="enabling-the-application-integration-for-springcm"></a>SpringCM のアプリケーション統合の有効化
このセクションでは、SpringCM のアプリケーション統合を有効にする方法について説明します。

**SpringCM のアプリケーション統合を有効にするには、次の手順に従います。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-spring-cm-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-spring-cm-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-spring-cm-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-spring-cm-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**SpringCM**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-spring-cm-tutorial/IC797045.png "Application Gallery")

7. 結果ウィンドウで **[SpringCM]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![SpringCM](./media/active-directory-saas-spring-cm-tutorial/IC797046.png "SpringCM")

## <a name="configure-single-sign-on"></a>Configure single sign-on
このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、SpringCM で Azure AD のユーザー アカウントを使用してユーザーを認証できるようにする方法を説明します。

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **[SpringCM]** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![Configure single Sign-On](./media/active-directory-saas-spring-cm-tutorial/IC797047.png "Configure single Sign-On")

2. **[ユーザーの SpringCM へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![Configure single Sign-On](./media/active-directory-saas-spring-cm-tutorial/IC797048.png "Configure single Sign-On")

3. **[アプリの URL を構成する]** ページの **[SpringCM サインオン URL]** テキスト ボックスに、ユーザーが SpringCM アプリケーションのサインオンに使用する URL を入力して、　**[次へ]** をクリックします。 
   
    アプリケーション URL は SpringCM テナント URL です (例: *https://na11.springcm.com/atlas/SSO/SSOEndpoint.ashx?aid=16826*)。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-spring-cm-tutorial/IC797049.png "Configure App URL")

4. **[SpringCM でのシングル サインオン構成]** ページで、証明書をダウンロードするために **[証明書のダウンロード]** をクリックし、コンピューターにローカルで証明書ファイルを保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-spring-cm-tutorial/IC797050.png "Configure Single SignOn")

5. 別の Web ブラウザーのウィンドウで、管理者として **SpringCM** 企業サイトにサインオンします。

6. 上部にあるメニューの **[GO TO]** をクリックし、**[Preferences]** をクリックします。次に、**[Account Preferences]** セクションで、**[SAML SSO]** をクリックします。
   
    ![SAML SSO](./media/active-directory-saas-spring-cm-tutorial/IC797051.png "SAML SSO")

7. [Identity Provider Configuration] セクションで、次の手順に従います。
   
    ![Identity Provider Configuration](./media/active-directory-saas-spring-cm-tutorial/IC797052.png "Identity Provider Configuration")   
  1. ダウンロードした Azure Active Directory 証明書をアップロードするために、**[Select Issuer Certificate]** または **[Change Issuer Certificate]** をクリックします。
  2. Azure クラシック ポータルの **[SpringCM でのシングル サインオンの構成]** ページで、**[発行者の URL]** の値をコピーし、**[発行者]** ボックスに貼り付けます。
  3. Azure クラシック ポータルの **[SpringCM でのシングル サインオンの構成]** ダイアログ ページで **[シングル サインオン サービスの URL]** の値をコピーし、それを **[サービス プロバイダー (SP) によって開始されたエンドポイント]** ボックスに貼り付けます。
  4. **[SAML Enabled]** として、**[Enable]** を選択します。
  5. **[Save]**をクリックします。

8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-spring-cm-tutorial/IC797053.png "Configure Single SignOn")

## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]
Azure Active Directory ユーザーが SpringCM にログインできるようにするには、そのユーザーを SpringCM にプロビジョニングする必要があります。  

SpringCM の場合、プロビジョニングは手動で行います。

>[!NOTE]
>詳細については、[Create and Edit a SpringCM UserSpringCM](http://knowledge.springcm.com/create-and-edit-a-springcm-user) (ユーザーの作成と編集に関するページ) をご覧ください。 
> 

**ユーザー アカウントを SpringCM にプロビジョニングするには、次の手順に従います。**

1. **SpringCM** 企業サイトに管理者としてログインします。

2. **[GOTO]**、**[Address Book]** の順にクリックします。
   
    ![Create User](./media/active-directory-saas-spring-cm-tutorial/IC797054.png "Create User")

3. **[Create User]**をクリックします。

4. **[User Role]**を選択します。

5. **[Send Activation Email]**を選択します。

6. 関連するテキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの姓名と電子メール アドレスを入力します。

7. ユーザーを **[Security group]**に追加します。

8. [ **Save**] をクリックします。

  >[!NOTE]
  >SpringCM から提供されている他の SpringCM ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。  
  > 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを SpringCM に割り当てるには、次の手順に従います。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **SpringCM** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-spring-cm-tutorial/IC797055.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-spring-cm-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


