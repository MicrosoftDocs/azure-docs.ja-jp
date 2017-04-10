---
title: "チュートリアル: Azure Active Directory と Replicon の統合 | Microsoft Docs"
description: "Azure Active Directory で Replicon を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 4f0519865bf41d045791905ff4eb7bbf92981dcc
ms.lasthandoff: 01/05/2017


---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>チュートリアル: Azure Active Directory と Replicon の統合
このチュートリアルでは、Azure と Replicon の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Replicon テナント

このチュートリアルを完了すると、Replicon に割り当てた Azure AD ユーザーは、Replicon 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Replicon のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-replicon-tutorial/IC777798.png "Scenario")

## <a name="enabling-the-application-integration-for-replicon"></a>Replicon のアプリケーション統合の有効化
このセクションでは、Replicon のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-replicon-perform-the-following-steps"></a>Replicon のアプリケーション統合を有効にするには、次の手順に従います。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-replicon-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-replicon-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-replicon-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Replicon**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-replicon-tutorial/IC777799.png "Application Gallery")

7. 結果ウィンドウで **[Replicon]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、SAML プロトコルに基づくフェデレーションを使用して、ユーザーが Azure AD のアカウントで Replicon に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Replicon** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-replicon-tutorial/IC777801.png "Configure single sign-on")

2. **[ユーザーの Replicon へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-replicon-tutorial/IC777802.png "Configure single sign-on")

3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-replicon-tutorial/IC777803.png "Configure app URL")
   
    a. **[Replicon サインオン URL]** ボックスに、Replicon テナント URL (例: *https://na2.replicon.com/company/saml2/sp-sso/post*) を入力します。

    b. **[Replicon 応答 URL]** ボックスに、Replicon **AssertionConsumerService** の URL (例: *https://global.replicon.com/!/saml2/company/sso/post*) を入力します。  
      
    > [!NOTE]
    > **https://global.replicon.com/!/saml2/\<企業キー\>** でReplicon メタデータから URL を取得できます。
    > 
    > 
 
    c. **[次へ]**

4. **[Replicon でのシングル サインオンの構成]** ページで、**[メタデータのダウンロード]** をクリックしてメタデータをダウンロードし、メタデータをコンピューターのローカルに保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-replicon-tutorial/IC777804.png "Configure single sign-on")

5. 別の Web ブラウザー ウィンドウで、 Replicon 企業サイトに管理者としてログインします。

6. SAML 2.0 を構成するには、次の手順に従います。
   
    ![Enable SAML authentication](./media/active-directory-saas-replicon-tutorial/IC777805.png "Enable SAML authentication")
   

   1. **[EnableSAML Authentication2]** ダイアログを表示するには、URL の企業キーの後に次のパスを追加します。  
      **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
      完全な URL のスキーマを次に示します。  
      **https://na2.replicon.com/\<企業キー\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**

   2. **+** をクリックして、**[v20Configuration]** セクションを展開します。

   3. **+** をクリックして、**[metaDataConfiguration]** セクションを展開します。

   4. **[Choose File]** をクリックして、ID プロバイダー メタデータ XML ファイルを選択し、**[送信]** をクリックします。

7. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-replicon-tutorial/IC778418.png "Configure single sign-on")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Replicon にログインできるようにするには、そのユーザーを Replicon にプロビジョニングする必要があります。  
Replicon の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。
1. Web ブラウザー ウィンドウで、 Replicon 企業サイトに管理者としてログインします。

2. **[Administration] \> [Users]** に移動します。
   
    ![ユーザー](./media/active-directory-saas-replicon-tutorial/IC777806.png "Users")

3. **[+Add User]**をクリックします。
   
    ![ユーザーの追加](./media/active-directory-saas-replicon-tutorial/IC777807.png "Add User")

4. **[User Profile]** セクションで、次の手順に従います。
   
    ![User profile](./media/active-directory-saas-replicon-tutorial/IC777808.png "User profile")
   
    1. **[Login Name]** ボックスに、プロビジョニングする Azure AD ユーザーの Azure AD メール アドレスを入力します。

    2. **[Authentication Type]** として **[SSO]** を選択します。

    3. **[Department]** テキストボックスに、ユーザーの所属を入力します。

    4. **[Employee Type]** として **[Administrator]** を選択します。

    5. **[Save User Profile]**をクリックします。

> [!NOTE]
> Replicon から提供されている他の Replicon ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
> 
> 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-replicon-perform-the-following-steps"></a>ユーザーを Replicon に割り当てるには、次の手順に従います。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **Replicon** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-replicon-tutorial/IC777809.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-replicon-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


