---
title: "チュートリアル: Azure Active Directory と Citrix ShareFile の統合 | Microsoft Docs"
description: "Azure Active Directory と Citrix ShareFile の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: b85680104fe4f33638c559b2a12483a2312a4476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>チュートリアル: Azure Active Directory と Citrix ShareFile の統合

このチュートリアルでは、Citrix ShareFile と Azure Active Directory (Azure AD) を統合する方法について説明します。

Citrix ShareFile と Azure AD の統合には、次の利点があります。

- Citrix ShareFile にアクセスできるユーザーを Azure AD で制御できます。
- ユーザーが Azure AD アカウントで自動的に Citrix ShareFile にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Citrix ShareFile の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Citrix ShareFile でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Citrix ShareFile の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-citrix-sharefile-from-the-gallery"></a>ギャラリーからの Citrix ShareFile の追加
Azure AD への Citrix ShareFile の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Citrix ShareFile を追加する必要があります。

**ギャラリーから Citrix ShareFile を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Citrix ShareFile**」と入力し、結果ウィンドウで **[Citrix ShareFile]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Citrix ShareFile](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Citrix ShareFile で Azure AD シングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Citrix ShareFile ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Citrix ShareFile の関連ユーザーの間で、リンク関係が確立されている必要があります。

Citrix ShareFile で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Citrix ShareFile で Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Citrix ShareFile テスト ユーザーの作成](#create-a-citrix-sharefile-test-user)** - Citrix ShareFile で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD シングル サインオンを有効にし、Citrix ShareFile アプリケーションでシングル サインオンを構成します。

**Citrix ShareFile で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Citrix ShareFile** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_samlbase.png)

3. **[Citrix ShareFile のドメインと URL]** セクションで、次の手順を実行します。

    ![[Citrix ShareFile のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_url.png)
    
    **[サインオン URL]** ボックスに、`https://<tenant-name>.sharefile.com/saml/login` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[Citrix ShareFile クライアント サポート チーム](https://www.citrix.co.in/products/sharefile/support.html)に問い合わせてください。 

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-sharefile-tutorial/tutorial_general_400.png)

6. **[Citrix ShareFile Configuration (Citrix ShareFile 構成)]** セクションで、**[Configure Citrix ShareFile (Citrix ShareFile の構成)]** をクリックして **[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Citrix ShareFile の構成](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_configure.png) 

7. 別の Web ブラウザー ウィンドウで、 **Citrix ShareFile** 企業サイトに管理者としてログインします。

8. 上部のツールバーの **[Admin]**をクリックします。

9. 左側のナビゲーション ウィンドウで、 **[Configure Single Sign-On]**を選択します。
   
    ![Account Administration](./media/active-directory-saas-sharefile-tutorial/ic773627.png "Account Administration")

10. **[Single Sign-On/ SAML 2.0 Configuration]** ダイアログ ページの **[基本設定]** で、次の手順を実行します。
   
    ![シングル サインオン](./media/active-directory-saas-sharefile-tutorial/ic773628.png "シングル サインオン")
   
    a. **[Enable SAML]**をクリックします。
    
    b. **[Your IDP Issuer/ Entity ID]\(IDP 発行者/エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c. **[X.509 Certificate]\(X.509証明書\)** フィールドの横の **[変更]** をクリックし、Azure Portal からダウンロードした証明書をアップロードします。
    
    d. **[ログイン URL]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
    
    e. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

11. Citrix ShareFile 管理ポータルで **[Save]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-sharefile-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-sharefile-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-sharefile-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-sharefile-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-citrix-sharefile-test-user"></a>Citrix ShareFile テスト ユーザーの作成

Azure AD ユーザーが Citrix ShareFile にログインできるようにするには、ユーザーを Citrix ShareFile にプロビジョニングする必要があります。 Citrix ShareFile の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Citrix ShareFile** テナントにログインします。

2. **[ユーザーの管理]\>[Manage Users Home (ユーザーの管理: ホーム)]\>[+ Create Employee (従業員の作成)]** の順にクリックします。
   
   ![Create Employee](./media/active-directory-saas-sharefile-tutorial/IC781050.png "Create Employee")

3. **[Basic Information]** セクションで、次の手順を実行します。
   
   ![Basic Information](./media/active-directory-saas-sharefile-tutorial/IC799951.png "Basic Information")
   
   a. **[Email Address]\(電子メール アドレス\)** ボックスに、Britta Simon アカウントの電子メール アドレスを **brittasimon@contoso.com** と入力します。
   
   b. **[名]** ボックスに、ユーザーの**名**を、「**Britta**」と入力します。
   
   c. **[姓]** ボックスに、ユーザーの**姓**を、「**Simon**」と入力します。

4. **[ユーザーの追加]**をクリックします。
  
   >[!NOTE]
   >Azure AD のアカウント所有者が電子メールを受信し、リンクをたどって自分のアカウントを確認すると、アカウントがアクティブになります。Azure AD ユーザー アカウントのプロビジョニングには、他の Citrix ShareFile ユーザー アカウント作成ツールまたは Citrix ShareFile が提供する API を使用できます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Citrix ShareFile へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Citrix ShareFile に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Citrix ShareFile]** を選択します。

    ![アプリケーション一覧の Citrix ShareFile リンク](./media/active-directory-saas-sharefile-tutorial/tutorial_sharefile_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Citrix ShareFile] タイルをクリックすると、自動的に Citrix ShareFile アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sharefile-tutorial/tutorial_general_203.png

