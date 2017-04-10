---
title: "チュートリアル: Azure Active Directory と Gigya の統合 | Microsoft Docs"
description: "Azure Active Directory で Gigya を使用して、シングル サインオンや自動プロビジョニングなどを有効にする方法について説明します。"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: bf5588885de9c280eb70712dbf800efe509ee912
ms.openlocfilehash: 96eb66f35a91e769418d179b0997cd32e02cdc97


---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>チュートリアル: Azure Active Directory と Gigya の統合
このチュートリアルでは、Azure と Gigya の統合について説明します。  
このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* 有効な Azure サブスクリプション
* Gigya でのシングル サインオンが有効なサブスクリプション

このチュートリアルを完了すると、Gigya に割り当てた Azure AD ユーザーは、Gigya 企業サイト (サービス プロバイダーが開始したサインオン) で、または「 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)」を使用して、アプリケーションにシングル サインオンできるようになります。

このチュートリアルで説明するシナリオは、次の要素で構成されています。

1. Gigya のアプリケーション統合の有効化
2. シングル サインオンの構成
3. ユーザー プロビジョニングの構成
4. ユーザーの割り当て

![シングル サインオンの構成](./media/active-directory-saas-gigya-tutorial/IC789512.png "Configure Single Sign-On")

## <a name="enabling-the-application-integration-for-gigya"></a>Gigya のアプリケーション統合の有効化
このセクションでは、Gigya のアプリケーション統合を有効にする方法について説明します。

### <a name="to-enable-the-application-integration-for-gigya-perform-the-following-steps"></a>Gigya のアプリケーション統合を有効にするには、次の手順を実行します。
1. Azure クラシック ポータルの左側のナビゲーション ウィンドウで、 **[Active Directory]**をクリックします。
   
    ![Active Directory](./media/active-directory-saas-gigya-tutorial/IC700993.png "Active Directory")

2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。

3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![アプリケーション](./media/active-directory-saas-gigya-tutorial/IC700994.png "Applications")

4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーションの追加](./media/active-directory-saas-gigya-tutorial/IC749321.png "Add application")

5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![ギャラリーからのアプリケーションの追加](./media/active-directory-saas-gigya-tutorial/IC749322.png "Add an application from gallerry")

6. **検索ボックス**に、「**Gigya**」と入力します。
   
    ![アプリケーション ギャラリー](./media/active-directory-saas-gigya-tutorial/IC789513.png "Application Gallery")

7. 結果ウィンドウで **[Gigya]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Gigya](./media/active-directory-saas-gigya-tutorial/IC789527.png "Gigya")
   
## <a name="configuring-single-sign-on"></a>シングル サインオンの構成

このセクションでは、ユーザーが SAML プロトコルに基づくフェデレーションを使用して、Azure AD でのユーザーのアカウントで Gigya に対する認証を行うことができるようにする方法を説明します。  
この手順の途中で、base-64 でエンコードされた証明書ファイルを作成する必要があります。  
この手順に慣れていない場合は、「 [How to convert a binary certificate into a text file (バイナリ証明書をテキスト ファイルに変換する方法)](http://youtu.be/PlgrzUZ-Y1o)」をご覧ください。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>シングル サインオンを構成するには、次の手順を実行します。
1. Azure クラシック ポータルの **Gigya** アプリケーション統合ページで、**[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-gigya-tutorial/IC789528.png "Configure Single Sign-On")

2. **[ユーザーの Gigya へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![シングル サインオンの構成](./media/active-directory-saas-gigya-tutorial/IC789529.png "Configure Single Sign-On")

3. **[アプリ URL の構成]** ページの **[Gigya サインオン URL]** ボックスに、"*http://company.gigya.com*" というパターンの URL を入力して、**[次へ]** をクリックします。
   
    ![アプリケーション URL の構成](./media/active-directory-saas-gigya-tutorial/IC789530.png "Configure App URL")

4. **[Gigya でのシングル サインオンの構成]** ページで、**[証明書のダウンロード]** をクリックし、コンピューターに証明書ファイルを保存します。
   
    ![シングル サインオンの構成](./media/active-directory-saas-gigya-tutorial/IC789531.png "Configure Single Sign-On")

5. 別の Web ブラウザー ウィンドウで、Gigya 企業サイトに管理者としてログインします。

6. **[設定] \> [SAML ログイン]** の順にクリックし、**[追加]** をクリックします。
   
    ![SAML ログイン](./media/active-directory-saas-gigya-tutorial/IC789532.png "SAML ログイン")

7. **[SAML ログイン]** セクションで、次の手順を実行します。
   
    ![SAML の構成](./media/active-directory-saas-gigya-tutorial/IC789533.png "SAML の構成")
   
    a. **[名前]** テキスト ボックスに、構成の名前を入力します。
   
    b. Azure クラシック ポータルの **[Gigya でのシングル サインオンの構成]** ダイアログ ページで、**[発行者の URL]** の値をコピーし、**[Issuer (発行者)]** ボックスに貼り付けます。
   
    c. Azure クラシック ポータルの **[Gigya でのシングル サインオンの構成]** ダイアログ ページで、**[シングル サインオン サービス URL]** の値をコピーし、**[Single Sign-On Service URL (シングル サインオン サービス URL)]** ボックスに貼り付けます。
   
    d. Azure クラシック ポータルの **[Gigya でのシングル サインオンの構成]** ダイアログ ページで、**[名前識別子形式]** の値をコピーし、**[Name ID Format (名前識別子形式)]** ボックスに貼り付けます。
   
    e. ダウンロードした証明書から **base-64 でエンコードされた** ファイルを作成します。
      
    > [!TIP]
    > 詳細については、 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)
    > 
    > 
   
    f. base-64 でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 証明書]** テキストボックスに貼り付けます。
   
    g. **[設定の保存]**をクリックします。

8. Azure クラシック ポータルで、[シングル サインオンの構成の確認] を選択し、**[完了]** をクリックして **[シングル サインオンの構成]** ダイアログを閉じます。
   
    ![シングル サインオンの構成](./media/active-directory-saas-gigya-tutorial/IC789534.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>ユーザー プロビジョニングの構成

Azure AD ユーザーが Gigya にログインできるようにするには、ユーザーを Gigya にプロビジョニングする必要があります。  
Gigya の場合、プロビジョニングは手動で行います。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>ユーザー アカウントをプロビジョニングするには、次の手順に従います。
1. **Gigya** 企業サイトに管理者としてログインします。
2. **[管理者] \> [ユーザーの管理]** の順にクリックし、**[ユーザーの招待]** をクリックします。
   
    ![Manage Users](./media/active-directory-saas-gigya-tutorial/IC789535.png "Manage Users")

3. [ユーザーの招待] ダイアログで、次の手順を実行します。
   
    ![ユーザーの招待](./media/active-directory-saas-gigya-tutorial/IC789536.png "ユーザーの招待")
   
    a. **[電子メール]** テキスト ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール エイリアスを入力します。
    
    b. **[ユーザーの招待]**をクリックします。
      
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    > 
    > 

 

## <a name="assigning-users"></a>ユーザーの割り当て
構成をテストするには、アプリケーションの使用を許可する Azure AD ユーザーを割り当てて、そのユーザーに、アプリケーションへのアクセス権を付与する必要があります。

### <a name="to-assign-users-to-gigya-perform-the-following-steps"></a>ユーザーを Gigya に割り当てるには、次の手順を実行します。
1. Azure クラシック ポータルで、テスト アカウントを作成します。
2. **Gigya** アプリケーション統合ページで、**[ユーザーの割り当て]** をクリックします。
   
    ![ユーザーの割り当て](./media/active-directory-saas-gigya-tutorial/IC789537.png "Assign Users")

3. テスト ユーザーを選択して、**[割り当て]** をクリックし、**[はい]** をクリックして割り当てを確定します。
   
    ![はい](./media/active-directory-saas-gigya-tutorial/IC767830.png "Yes")

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、 [アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)を参照してください。




<!--HONumber=Feb17_HO2-->


