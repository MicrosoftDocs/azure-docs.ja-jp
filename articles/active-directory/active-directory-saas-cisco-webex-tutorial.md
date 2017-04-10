---
title: "チュートリアル: Azure Active Directory と Cisco Webex の統合 | Microsoft Docs"
description: "Azure Active Directory で Cisco Webex を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: ada8ffbbdc5565a517f5a06cb640a36aaf8879b4
ms.openlocfilehash: b44b1a5b3e988a51db3325ec8a181651fa84e768
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>チュートリアル: Azure Active Directory と Cisco Webex の統合
このチュートリアルでは、Azure と Cisco Webex の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Cisco Webex テナント

このチュートリアルを完了すると、Cisco Webex に割り当てた Azure AD ユーザーは、Cisco Webex 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

* Cisco Webex のアプリケーション統合の有効化
* シングル サインオン (SSO) の構成
* ユーザー プロビジョニングの構成
* ユーザーの割り当て

![シナリオ](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scenario")

## <a name="enable-the-application-integration-for-cisco-webex"></a>Cisco Webex のアプリケーション統合の有効化
このセクションでは、Cisco Webex のアプリケーション統合を有効にする方法を説明します。

**Cisco Webex のアプリケーション統合を有効にするには、次の手順を実行します。**

1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
   ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
   ![アプリケーション](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "Applications")
4. ページの下部にある **[追加]** をクリックします。
   
   ![アプリケーションの追加](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "Add application")
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
   ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "Add an application from gallerry")
6. **検索ボックス**に、「**Cisco Webex**」と入力します。
   
   ![アプリケーション ギャラリー](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Application Gallery")
7. 結果ウィンドウで **[Cisco Webex]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
## <a name="configure-single-sign-on"></a>Configure single sign-on

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Cisco Webex に対する認証を行えるようにする方法を説明します。  

この手順の途中で、Base-64 でエンコードされた証明書を作成する必要があります。 この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)

**シングル サインオンを構成するには、次の手順に従います。**

1. Azure クラシック ポータルの **Cisco Webex** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Configure single sign-on")
2. **[ユーザーの Cisco Webex へのアクセスを設定してください]** ページで、**[Microsoft Azure AD シングル サインオン]** を選択し、**[次へ]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Configure single sign-on")
3. **[アプリケーション URL の構成]** ページで、次の手順を実行し、**[次へ]** をクリックします。
   
   ![アプリケーション URL の構成](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "Configure app URL")   
   1. **[サインオン URL]** ボックスに、Cisco Webex テナント URL を入力します (例: *http://contoso.webex.com*)。
   2. **[Cisco Webex 応答 URL]** ボックスに、**Cisco Webex AssertionConsumerService URL** を入力します (例: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*)。
4. **[Cisco Webex でのシングル サインオンの構成]** ページで、証明書をダウンロードするために、**[証明書のダウンロード]** をクリックし、証明書ファイルをコンピューターに保存します。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Configure single sign-on")
5. 別の Web ブラウザー ウィンドウで、Cisco Webex 企業サイトに管理者としてログインします。
6. 上部のメニューで **[Site Administration]**をクリックします。
   
   ![Site Administration](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Site Administration")
7. **[Manage Site]** セクションで、**[SSO Configuration]** をクリックします。
   
   ![SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO Configuration")
8. [Federated Web SSO Configuration] セクションで、次の手順を実行します。
   
   ![Federated SSO Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federated SSO Configuration")  
   1. **[Federation Protocol]** ボックスの一覧で、**[SAML 2.0]** を選択します。
   2. ダウンロードした証明書から **Base-64 でエンコードされた** ファイルを作成します。  
    >[!TIP]
    >詳細については、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)
    >  
   3. Base&64; でエンコードされた証明書をメモ帳で開き、その内容をコピーします。
   4. **[Import SAML Metadata]**をクリックし、Base-64 でエンコードされた証明書を貼り付けます。
   5. Azure クラシック ポータルで、**[Cisco Webex でのシングル サインオンの構成]** ダイアログ ページの **[発行者 URL]** の値をコピーし、**[Issuer for SAML (IdP ID)]** ボックスに貼り付けます。
   6. Azure クラシック ポータルで、**[Cisco Webex でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Customer SSO Service Login URL]** ボックスに貼り付けます。
   7. **[NameID Format]** ボックスの一覧で、**[Email address]** を選択します。
   8. **[AuthnContextClassRef]** ボックスに、「**urn:oasis:names:tc:SAML:2.0:ac:classes:Password**」と入力します。
   9. Azure クラシック ポータルで、**[Cisco Webex でのシングル サインオンの構成]** ダイアログ ページの **[リモート ログイン URL]** の値をコピーし、**[Customer SSO Service Login URL]** ボックスに貼り付けます。
   10. **[Update]**をクリックします。
9. Azure クラシック ポータルの **[Cisco Webex でのシングル サインオンの構成]** ダイアログ ページで、シングル サインオンの構成確認を選択し、**[完了]** をクリックします。
   
   ![シングル サインオンの構成](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Configure single sign-on")
   
## <a name="configure-user-provisioning"></a>[ユーザー プロビジョニングの構成]

Azure AD ユーザーが Cisco Webex にログインできるようにするには、ユーザーを Cisco Webex にプロビジョニングする必要があります。  

* Cisco Webex の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Cisco Webex** テナントにログインします。
2. **[Manage Users]\>[Add User]** の順にクリックします。
   
   ![Add users](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "Add users")
3. [Add User] セクションで、次の手順を実行します。
   
   ![Add user](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Add user")   
   1. **[Account Type]** として **[Host]** を選択します。
   2. **[First name]、[Last name]**、**[User name]**、**[Email]**、**[Password]**、**[Confirm Password]** の各ボックスに、既存の Azure AD ユーザーの情報を入力します。
   3. **[追加]**をクリックします。

>[!NOTE]
>Cisco Webex から提供されている他の Cisco Webex ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 
> 

## <a name="assign-users"></a>[ユーザーの割り当て]
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

**ユーザーを Cisco Webex に割り当てるには、次の手順を実行します。**

1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Cisco Webex** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
   ![ユーザーの割り当て](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "Assign users")
3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
   ![はい](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


