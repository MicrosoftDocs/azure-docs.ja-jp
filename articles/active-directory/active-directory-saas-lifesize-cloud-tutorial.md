---
title: "チュートリアル: Azure Active Directory と Lifesize Cloud の統合 | Microsoft Docs"
description: "Azure Active Directory と Lifesize Cloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7cc133d6289bffbc3b7fc591104bc51ebfc67ddd
ms.openlocfilehash: 8edbee8e554a7818b97669a4bb64b31ada67b1a5
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>チュートリアル: Azure Active Directory と Lifesize Cloud の統合
このチュートリアルでは、Lifesize Cloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Lifesize Cloud と Azure AD の統合には、次の利点があります。

* Lifesize Cloud にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Lifesize Cloud にシングル サインオン (SSO) できるようにします。
* 1 つの中央サイト (Azure クラシック ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Lifesize Cloud と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Lifesize Cloud でのシングル サインオン () が有効なサブスクリプション

>[!NOTE]
>このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。 
> 

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

* 必要な場合を除き、運用環境は使用しないでください。
* Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から&1; か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。

このチュートリアルで説明するシナリオは、主に次の&2; つの要素で構成されています。

* ギャラリーからの Lifesize Cloud の追加
* Azure AD SSO の構成とテスト

## <a name="add-lifesize-cloud-from-the-gallery"></a>ギャラリーからの Lifesize Cloud の追加
Azure AD への Lifesize Cloud の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lifesize Cloud を追加する必要があります。

**ギャラリーから Lifesize Cloud を追加するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Active Directory][1]
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. アプリケーション ビューを開くには、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![[アプリケーション]][2]
4. ページの下部にある **[追加]** をクリックします。
   
    ![アプリケーション][3]
5. **[実行する内容]** ダイアログで、**[ギャラリーからアプリケーションを追加します]** をクリックします。
   
    ![アプリケーション][4]
6. 検索ボックスに、「**Lifesize Cloud**」と入力します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_01.png)
7. 結果ウィンドウで **[Lifesize Cloud]** を選択し、**[完了]** をクリックしてアプリケーションを追加します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_02.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Lifesize Cloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Lifesize Cloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Lifesize Cloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Lifesize Cloud の **[Username]** の値として割り当てることで確立されます。

Lifesize Cloud での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Lifesize Cloud のテスト ユーザーの作成](#creating-a-lifesize-cloud-test-user)** - Lifesize Cloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
このセクションでは、クラシック ポータルで Azure AD の SSO を有効にして、Lifesize Cloud アプリケーションでシングル サインオンを構成します。

**Lifesize Cloud との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. クラシック ポータルの **Lifesize Cloud** アプリケーション統合ページで **[シングル サインオンの構成]** をクリックして、**[シングル サインオンの構成]** ダイアログを開きます。
   
    ![[シングル サインオンの構成]][6] 
2. **[ユーザーの Lifesize Cloud へのアクセスを設定してください]** ページで、**[Microsoft Azure AD のシングル サインオン]** を選択し、**[次へ]** をクリックします。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_03.png) 
3. **[アプリケーション設定の構成]** ダイアログ ページで、次の手順に従います。
   
   ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_04.png)   
  1. **[サインオン URL]** ボックスに、次のパターンを使用して、ユーザーが Lifesize Cloud アプリケーションへのサインオンに使用する URL を入力します。**https://login.lifesizecloud.com/ls/?acs**
  2. **[次へ]**をクリックします。
4. **[Lifesize Cloud でのシングル サインオンの構成]** ページで、次の手順を実行します。
   
   ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_05.png)
   1. **[証明書のダウンロード]** をクリックし、コンピューターにファイルを保存します。
   2. **[次へ]**をクリックします。
5. アプリケーション用に構成された SSO を取得するには、管理者権限で Lifesize Cloud アプリケーションにログインします。
6. ページの右上にある自分の名前をクリックし、**[詳細設定]** をクリックします。
   
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)
7. [詳細設定] で **[SSO 構成]** のリンクをクリックします。 インスタンスの [SSO 構成] ページが開きます。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)
8. SSO 構成 UI で、次の値を構成します。    
   
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
  1. Azure AD から [発行者の URL] の値をコピーし、**[ID プロバイダー発行者]** ボックスに貼り付けます。 
  2. Azure AD から [リモート ログイン URL] の値をコピーし、**[ログイン URL]** ボックスに貼り付けます。   
  3. ダウンロードした証明書をメモ帳で開き、証明書の内容 (BEGIN CERTIFICATE と END CERTIFICATE の行を除く) をコピーして、**[X.509 Certificate]** ボックスに貼り付けます。
  4. **[名]** ボックスの SAML 属性マッピングに、値を「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**」と入力します。
  5. **[姓]** ボックスの SAML 属性マッピングに、値を「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**」と入力します。
  6. **[電子メール]** ボックスの SAML 属性マッピングに、値を「**http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**」と入力します。
9. 構成を確認するには、**[テスト]** ボタンをクリックします。
   
   >[!NOTE]
   >テストを成功させるには、Azure AD の構成ウィザードを完了し、テストを実行するユーザーやグループにもアクセスを提供する必要があります。
   >  
10. **[SSO を有効にする]** ボタンをクリックして、SSO を有効にします。
11. **[更新]** ボタンをクリックして、すべての設定を保存します。 これにより、RelayState 値が生成されます。 テキスト ボックスに生成された RelayState 値をコピーします。 この値は、次の手順で必要になります。
12. クラシック ポータルで、シングル サインオンの構成確認を選択し、 **[次へ]**をクリックします。
    
   ![Azure AD のシングル サインオン][10]
13. **[シングル サインオンの確認]** ページで、**[完了]** をクリックします。  
    
    ![Azure AD のシングル サインオン][11]
    
**Microsoft Azure 管理ポータルにログインするには、次の手順に従います。**

1. 管理者の資格情報を使用して **https://portal.azure.com** にログインします。
2. 左のナビゲーション ウィンドウで、**[その他のサービス]** のリンクをクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_09.png)
3. Azure Active Directory を検索し、**[Azure Active Directory]** のリンクをクリックします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_10.png)
4. **[エンタープライズ アプリケーション]** ボタンで、すべての SaaS アプリケーションが表示されます。
    
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_11.png)
5. 次のブレードで、**[すべてのアプリケーション]** のリンクをクリックします。
    
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_12.png)
6. RelayState をセットアップする Lifesize アプリケーションを検索します。 
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_13.png)
7. ブレードの **[シングル サインオン]** のリンクをクリックします。
    
    ![Configure Single Sign-On](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_14.png)
8. **[詳細な URL 設定の表示]** チェック ボックスが表示されます。 チェック ボックスをオンにします。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_15.png)
9. Lifesize アプリケーションの [SSO 構成] ページに表示される、アプリケーションの RelayState を構成します。 
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_16.png)
10. 設定を保存します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、クラシック ポータルで Britta Simon というテスト ユーザーを作成します。

![Azure AD ユーザーの作成][20]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure クラシック ポータル**の左側のナビゲーション ウィンドウで、**[Active Directory]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_09.png) 
2. **[ディレクトリ]** の一覧から、ディレクトリ統合を有効にするディレクトリを選択します。
3. 上部のメニューで **[ユーザー]**をクリックして、ユーザーの一覧を表示します。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_03.png) 
4. 下部にあるツール バーで **[ユーザーの追加]** をクリックして、**[ユーザーの追加]** ダイアログ ボックスを開きます。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_04.png) 
5. **[このユーザーに関する情報の入力]** ダイアログ ページで、次の手順に従います。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_05.png) 
   1. [ユーザーの種類] として [組織内の新しいユーザー] を選択します。
   2. [ユーザー名] **ボックス**に「**BrittaSimon**」と入力します。
   3. **[次へ]**をクリックします。
6. **[ユーザー プロファイル]** ダイアログ ページで、次の手順に従います。

   ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_06.png) 
   1. **[名]** ボックスに「**Britta**」と入力します。   
   2. **[姓]** ボックスに「**Simon**」と入力します。
   3. **[表示名]** ボックスに「**Britta Simon**」と入力します。
   4. **[ロール]** 一覧で **[ユーザー]** を選択します。
   5. **[次へ]**をクリックします。
7. **[一時パスワードの取得]** ダイアログ ページで、**[作成]** をクリックします。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_07.png) 
8. **[一時パスワードの取得]** ダイアログ ページで、次の手順に従います。
   
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-lifesize-cloud-tutorial/create_aaduser_08.png) 
    1. **[新しいパスワード]** の値を書き留めます。
    2. **[完了]** をクリックします。   

### <a name="create-an-lifesize-cloud-test-user"></a>Lifesize Cloud のテスト ユーザーの作成
このセクションでは、Lifesize Cloud で Britta Simon というユーザーを作成します。 Lifesize Cloud では、ユーザーの自動プロビジョニングがサポートされています。 Azure AD での認証に成功すると、ユーザーはアプリケーションで自動的にプロビジョニングされます。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
このセクションでは、Britta Simon に Lifesize Cloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Lifesize Cloud に Britta Simon を割り当てるには、次の手順に従います。**

1. クラシック ポータルでアプリケーション ビューを開くために、ディレクトリ ビューでトップ メニューの **[アプリケーション]** をクリックします。
   
    ![ユーザーの割り当て][201] 
2. アプリケーションの一覧で **[Lifesize Cloud]** を選択します。
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_lifesizecloud_50.png) 
3. 上部のメニューで **[ユーザー]**をクリックします。
   
    ![ユーザーの割り当て][203]
4. ユーザーの一覧で **[Britta Simon]**を選択します。
5. 下部にあるツール バーで **[割り当て]**をクリックします。
   
    ![ユーザーの割り当て][205]

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
このセクションでは、アクセス パネルを使用して Azure AD の SSO 構成をテストします。

アクセス パネルで Lifesize Cloud のタイルをクリックすると、自動的に Lifesize Cloud アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-lifesize-cloud-tutorial/tutorial_general_205.png

