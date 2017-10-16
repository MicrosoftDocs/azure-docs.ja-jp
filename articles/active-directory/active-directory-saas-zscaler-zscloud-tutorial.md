---
title: "チュートリアル: Azure Active Directory と Zscaler ZSCloud の統合 | Microsoft Docs"
description: "Azure Active Directory と Zscaler ZSCloud の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: 2b6eb113e5725260bc04f50e9218939bf28b1ff0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>チュートリアル: Azure Active Directory と Zscaler ZSCloud の統合

このチュートリアルでは、Zscaler ZSCloud と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zscaler ZSCloud と Azure AD の統合には、次の利点があります。

- Zscaler ZSCloud にアクセスするユーザーを Azure AD で管理できます
- ユーザーが Azure AD アカウントで Zscaler ZSCloud に自動的にサインオン (シングル サインオン) できるように設定することが可能です
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zscaler ZSCloud と Azure AD の統合を構成するには、次の項目が必要です。

- Azure AD サブスクリプション
- Zscaler ZSCloud でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zscaler ZSCloud の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>ギャラリーからの Zscaler ZSCloud の追加
Azure AD への Zscaler ZSCloud の統合を構成するには、管理対象の SaaS アプリ一覧に Zscaler ZSCloud をギャラリーから追加する必要があります。

**ギャラリーから Zscaler ZSCloud を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Zscaler ZSCloud**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. 結果ウィンドウで **[Zscaler ZSCloud]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zscaler ZSCloud で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zscaler ZSCloud ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zscaler ZSCloud の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を Zscaler ZSCloud の **[Username]** の値として割り当てることで確立されます。

Zscaler ZSCloud で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[プロキシ設定の構成](#configuring-proxy-settings)** - Internet Explorer でプロキシ設定を構成します
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Zscaler ZSCloud のテスト ユーザーの作成](#creating-a-zscaler-zscloud-test-user)** - Zscaler ZSCloud で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Zscaler ZSCloud アプリケーションでシングル サインオンを構成します。

**Zscaler ZSCloud で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Zscaler ZSCloud** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. **[Zscaler ZSCloud Domain and URLs (Zscaler ZSCloud のドメインと URL)]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     **[サインオン URL]** ボックスに、ユーザーが ZScaler ZSCloud アプリケーションへのサインオンに使用する URL を入力します。
    
    > [!NOTE] 
    > この値は実際のサインオン URL で更新する必要があります。 この値を取得するには、[Zscaler ZSCloud クライアント サポート チーム](https://support.zscaler.com/hc/articles/210172606-Zscaler-is-Expanding-Its-Zscloud-Global-Footprint)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_400.png)

6. **[Zscaler ZSCloud Configuration (Zscaler ZSCloud 構成)]**セクションで、 **[Configure Zscaler ZSCloud (Zscaler ZSCloud を構成する)]**をクリックして、**[サインオンの構成]**ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. 別の Web ブラウザー ウィンドウで、ZScaler ZSCloud 企業サイトに管理者としてログインします。

8. 上部のメニューで **[管理]**をクリックします。
   
    ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800206.png "Administration")

9. **[管理者とロールの管理]** をクリックし、**[ユーザーと認証の管理]** をクリックします。   
            
    ![ユーザーと認証の管理](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800207.png "Manage Users & Authentication")

10. **[組織の認証オプションの選択]** セクションで、次の手順を実行します。   
                
    ![Authentication](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800208.png "Authentication")
   
    a. **[SAML シングル サインオンを使用した認証]**を選択します。

    b. **[SAML シングル サインオン パラメーターの構成]**をクリックします。

11. **[SAML シングル サインオン パラメーターの構成]** ダイアログ ページで、次の手順に従い、**[完了]** をクリックします

    ![シングル サインオン](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800209.png "Single Sign-On")
    
    a. **[URL of the SAML Portal to which users are sent for authentication (ユーザーが認証に送られる SAML ポータルの URL)]** ボックスに **[SAML Single Sign-On Service URL (SAML シングル サインオン サービス URL)]** 値を貼り付けます。
    
    b. **[ログイン名を含む属性]** ボックスに「**NameID**」と入力します。
    
    c. ダウンロードした証明書をアップロードするには、 **[Zscaler pem]**をクリックします。
    
    d. **[SAML 自動プロビジョニングを有効にする]**を選択します。

12. **[ユーザー認証の構成]** ダイアログ ページで、次の手順に従います。

    ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800210.png "Administration")
    
    a. [ **Save**] をクリックします。

    b. **[今すぐ認証する]**をクリックします。

## <a name="configuring-proxy-settings"></a>プロキシ設定の構成
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer でプロキシ設定を構成するには

1. **Internet Explorer**を開始します。

2. **[ツール]** メニューの **[インターネット オプション]** を選択し、**[インターネット オプション]** ダイアログを開きます。   
    
     ![インターネット オプション](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769492.png "Internet Options")

3. **[接続]** タブをクリックします。   
  
     ![接続](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769493.png "Connections")

4. **[LAN の設定]** をクリックして **[LAN の設定]** ダイアログを開きます。

5. [プロキシ サーバー] セクションで、次の手順を実行します。   
   
    ![プロキシ サーバー](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769494.png "Proxy server")

    a. **[LAN にプロキシ サーバーを使用する]** をオンにします。

    b. [アドレス] ボックスに「 **gateway.zscalerone.net**」と入力します。

    c. [ポート] ボックスに「 **80**」と入力します。

    d. **[ローカル アドレスにはプロキシ サーバーを使用しない]**を選択します。

    e. **[OK]** をクリックして **[ローカル エリア ネットワーク (LAN) の設定]** ダイアログを閉じます。

6. **[OK]** をクリックして **[インターネット オプション]** ダイアログを閉じます。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。

### <a name="creating-a-zscaler-zscloud-test-user"></a>Zscaler ZSCloud テスト ユーザーの作成

Azure AD ユーザーが ZScaler ZSCloud にログインできるようにするには、そのユーザーを ZScaler ZSCloud にプロビジョニングする必要があります。  
ZScaler ZSCloud の場合、プロビジョニングは手動で行います。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>ユーザー プロビジョニングを構成するには、次の手順に従います。

1. **Zscaler** テナントにログインします。

2. **[管理]**をクリックします。   
   
    ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781035.png "Administration")

3. **[ユーザー管理]**をクリックします。   
        
     ![Add](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Add")

4. **[ユーザー]** タブで、**[追加]** をクリックします。
      
    ![Add](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Add")

5. [ユーザーの追加] セクションで、次の手順を実行します。
        
    ![ユーザーの追加](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781038.png "Add User")
   
    a. プロビジョニングする有効な AAD アカウントの **[ユーザー ID]**、**[ユーザー表示名]**、**[パスワード]**、**[パスワードの確認]** に入力し、**[グループ]** と **[部署]** を選択します。

    b. [ **Save**] をクリックします。

> [!NOTE]
> ZScaler ZSCloud から提供されている他の ZScaler ZSCloud ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zscaler ZSCloud へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Zscaler ZSCloud に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Zscaler ZSCloud]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。

アクセス パネルで Zscaler ZSCloud のタイルをクリックすると、自動的に Zscaler ZSCloud アプリケーションにサインオンします。

アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_203.png

