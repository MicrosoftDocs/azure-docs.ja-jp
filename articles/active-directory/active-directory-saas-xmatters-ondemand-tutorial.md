---
title: "チュートリアル: Azure Active Directory と xMatters OnDemand の統合 | Microsoft Docs"
description: "Azure Active Directory で xMatters OnDemand を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ca0633db-4f95-432e-b3db-0168193b5ce9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 86742dc7ee1429a0cdb23f4da3fa58341468afbc
ms.openlocfilehash: 02ed9be91db6de7f27810fe6b91d8604425073ae
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-xmatters-ondemand"></a>チュートリアル: Azure Active Directory と xMatters OnDemand の統合
このチュートリアルでは、Azure と xMatters OnDemand の統合について説明します。 このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* xMatters OnDemand テナント

このチュートリアルを完了すると、xMatters OnDemand に割り当てた Azure AD ユーザーは、xMatters OnDemand 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」の説明に従って、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. xMatters OnDemand のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シナリオ](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776788.png "Scenario")

## <a name="enabling-the-application-integration-for-xmatters-ondemand"></a>xMatters OnDemand のアプリケーション統合の有効化
このセクションでは、xMatters OnDemand のアプリケーション統合を有効にする方法を説明します。

### <a name="to-enable-the-application-integration-for-xmatters-ondemand-perform-the-following-steps"></a>xMatters OnDemand のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-xmatters-ondemand-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-xmatters-ondemand-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**xMatters OnDemand**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776789.png "Application Gallery")

7. 結果ウィンドウで **[XMatters OnDemand]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![xMatters OnDemand](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776790.png "xMatters OnDemand")

## <a name="configuring-single-sign-on"></a>シングル サインオンの構成
このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで xMatters OnDemand に対する認証を行うことができるようにする方法を説明します。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **XMatters OnDemand** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックし、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776791.png "Configure single sign-on")

2. **[ユーザーの XMatters OnDemand へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776792.png "Configure single sign-on")

3. **[アプリケーション URL の構成]** ページで、次の手順を実行します。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776793.png "Configure app URL")
   
    a. **[XMatters OnDemand サインイン URL]** ボックスに、`https://<tenant-name>.XMattersOnDemandapp.com` という形式で URL を入力します。
   
    b. **[次へ]**をクリックします。

4. **[XMatters OnDemand でのシングル サインオン構成]** ページで、証明書をダウンロードするには、**[証明書のダウンロード]** をクリックし、証明書ファイルをローカルで **c:\\XMatters OnDemand.cer** としてコンピューターのローカルに保存します。
   
    > [!IMPORTANT]
    > XMatters サポート チームに証明書を転送する必要があります。 シングル サインオンの構成を確定するには、その前に、xMatters サポート チームによって証明書がアップロードされる必要があります。
    > 
    > 
   
    ![シングル サインオンの構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776794.png "Configure single sign on")

5. 別の Web ブラウザーのウィンドウで、XMatters OnDemand の企業サイトに管理者としてログインします。

6. 上部のツールバーで、**[管理者]** をクリックし、左側にあるナビゲーション バーで **[会社の詳細]** をクリックします。
   
    ![管理](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776795.png "Admin")

7. **[SAML 構成]** ページで、次の手順を実行します。
   
    ![SAML 構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776796.png "SAML 構成")
   
    a. **[Enable SAML]**を選択します。
   
    b. Azure クラシック ポータルの **[XMatters OnDemand でのシングル サインオンの構成]** ダイアログ ページで、**[ID プロバイダーの ID]** 値をコピーして、**[ID プロバイダーの ID]** ボックスに貼り付けます。
   
    c. Azure クラシック ポータルの **[XMatters OnDemand でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[シングル サインオン サービス URL]** ボックスに貼り付けます。
   
    d. Azure クラシック ポータルの **[XMatters OnDemand でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインアウト サービス URL]** の値をコピーし、**[シングル ログアウト URL]** ボックスに貼り付けます。
   
    e. [会社の詳細] ページで、上部にある **[変更の保存]**をクリックします。
    
    ![会社の詳細](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776797.png "Company details")

8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776798.png "Configure single sign on")

## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成
Azure AD ユーザーが XMatters OnDemand にログインできるようにするには、そのユーザーを XMatters OnDemand にプロビジョニングする必要があります。  
XMatters OnDemand の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **XMatters OnDemand** テナントにログインします。

2. **[ユーザー]** タブをクリックします。

3. **[ユーザーの追加]**をクリックします。
  
    ![ユーザー](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781048.png "Users")

4. **[アクティブ]**を選択します。

5. **[ユーザーの追加]** セクションで、次の手順に従います。
   
    ![Add a User](./media/active-directory-saas-xmatters-ondemand-tutorial/IC781049.png "Add a User")
   
    a. プロビジョニングする有効な AAD アカウントの**ユーザー ID**、**名**、**姓**、**サイト**を入力します。
    
    b. **[保存]**をクリックします。


## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-xmatters-ondemand-perform-the-following-steps"></a>ユーザーを XMatters OnDemand に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。

2. **XMatters OnDemand** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-xmatters-ondemand-tutorial/IC776799.png "Assign users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-xmatters-ondemand-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。


