---
title: "チュートリアル: Azure Active Directory と SkyDesk Email の統合 | Microsoft Docs"
description: "Azure Active Directory と SkyDesk Email の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.openlocfilehash: 0ffcca4161fc836192fc9c9871a905f36ea76b32
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>チュートリアル: Azure Active Directory と SkyDesk Email の統合

このチュートリアルでは、SkyDesk Email と Azure Active Directory (Azure AD) を統合する方法について説明します。

SkyDesk Email と Azure AD の統合には、次の利点があります。

- SkyDesk Email にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SkyDesk Email にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SkyDesk Email の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SkyDesk Email でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SkyDesk Email の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-skydesk-email-from-the-gallery"></a>ギャラリーからの SkyDesk Email の追加
Azure AD への SkyDesk Email の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SkyDesk Email を追加する必要があります。

**ギャラリーから SkyDesk Email を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**SkyDesk Email**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_search.png)

5. 結果パネルで **[SkyDesk Email]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SkyDesk Email で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SkyDesk Email ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SkyDesk Email の関連ユーザーの間で、リンク関係が確立されている必要があります。

SkyDesk Email で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

SkyDesk Email で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Skydesk Email テスト ユーザーの作成](#creating-a-skydesk-email-test-user)** - Skydesk Email で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SkyDesk Email アプリケーションでシングル サインオンを構成します。

**Email で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SkyDesk Email** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_samlbase.png)

3. **[SkyDesk Email のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_url.png)

    **[サインオン URL]** ボックスに、`https://mail.skydesk.jp/portal/<companyname>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[SkyDesk Email クライアント サポート チーム](https://www.skydesk.sg/support/)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_400.png)

6. **[SkyDesk Email Configuration (SkyDesk Email 構成)]** セクションで、**[Configure SkyDesk Email (SkyDesk Email を構成する)]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_configure.png) 

7. **SkyDesk Email** で SSO を有効にするには、次の手順に従います。

    a. 管理者として SkyDesk Email アカウントにサインオンします。

    b. 上部のメニューで、**[設定]** をクリックし、**[組織]** を選択します。 
    
      ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
    c. 左側のパネルで **[ドメイン]** をクリックします。
    
      ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_53.png)

    d. **[ドメインの追加]** をクリックします。
    
      ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_54.png)

    e. 自分のドメイン名を入力し、ドメインを確認します。
    
      ![Configure Single Sign-On](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_55.png)

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 左側のパネルで **[SAML Authentication (SAML 認証)]** をクリックします。
    
      ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_52.png)

8. **[SAML 認証]** ダイアログ ページで、次の手順に従います。
   
      ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_56.png)
   
    >[!NOTE]
    >SAML ベースの認証を使用するには、**ドメインが検証済み**または**ポータル URL** が設定済みである必要があります。 一意の名前でポータル URL を設定できます。
    > 
    > 
   
    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
   
    b. **[ログアウト]** URL ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

    c. **[パスワード変更 URL]** は省略可能なので、空白のままにします。

    d. **[Get Key From File (ファイルからキーを取得)]** をクリックし、Azure portal からダウンロードした証明書を選択してから、**[開く]** をクリックして証明書をアップロードします。

    e. **[アルゴリズム]** として **[RSA]** を選択します。

    f. **[OK]** をクリックして変更を保存します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-skydeskemail-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-skydesk-email-test-user"></a>SkyDesk Email テスト ユーザーの作成

このセクションでは、SkyDesk Email で Britta Simon というユーザーを作成します。

1. SkyDesk Email 内の左側のパネルで **[User Access (ユーザー アクセス)]** をクリックし、自分のユーザー名を入力します。 

    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_58.png)

>[!NOTE] 
>ユーザーを一括で作成する必要がある場合は、[SkyDesk Email クライアント サポート チーム](https://www.skydesk.sg/support/)にお問い合わせください。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SkyDesk Email へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**SkyDesk Email に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[SkyDesk Email]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-skydeskemail-tutorial/tutorial_skydeskemail_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [SkyDesk Email] タイルをクリックすると、自動的に SkyDesk Email アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-skydeskemail-tutorial/tutorial_general_203.png

