---
title: "チュートリアル: Azure Active Directory と PingBoard の統合 | Microsoft Docs"
description: "Azure Active Directory と PingBoard の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.author: jeedes
ms.openlocfilehash: 67534da42ac27989a8b08cec4d6f9f9c31774264
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>チュートリアル: Azure Active Directory と PingBoard の統合

このチュートリアルでは、PingBoard と Azure Active Directory (Azure AD) を統合する方法について説明します。

PingBoard と Azure AD の統合には、次の利点があります。

- PingBoard にアクセスする Azure AD ユーザーを制御できます
- ユーザーが Azure AD アカウントで PingBoard に自動的にサインオン (シングル サインオン) できるように設定できます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

PingBoard と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- PingBoard でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの PingBoard の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-pingboard-from-the-gallery"></a>ギャラリーからの PingBoard の追加
Azure AD への PingBoard の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PingBoard を追加する必要があります。

**ギャラリーから PingBoard を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![エンタープライズ アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**PingBoard**」と入力し、結果ウィンドウで **PingBoard** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果一覧の PingBoard](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、PingBoard で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PingBoard ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと PingBoard の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、PingBoard の **[Username]** の値として割り当てることで確立されます。

PingBoard で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[PingBoard のテスト ユーザーの作成](#create-a-pingboard-test-user)** - PingBoard で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、PingBoard アプリケーションにシングル サインオンを構成します。

**PingBoard で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **PingBoard** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2.  **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. **[PingBoard のドメインと URL]** セクションで、**IDP 開始モード**でアプリケーションを構成する場合は、次の手順を実行します。

    ![[PingBoard のドメインと URL] のシングル サインオン情報 IDP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_url.png)

    a. **[識別子]** ボックスに、値として「`http://app.pingboard.com/sp`」と入力します。

    b. **[応答 URL]** ボックスに、`https://<entity-id>.pingboard.com/auth/saml/consume` のパターンを使用して URL を入力します。

4. **SP** 開始モードでアプリケーションを構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにします。

    ![[PingBoard のドメインと URL] のシングル サインオン情報 SP](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     **[サインオン URL]** ボックスに、`https://<sub-domain>.pingboard.com/sign_in` という形式で URL を入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[PingBoard クライアント サポート チーム](https://support.pingboard.com/)に問い合わせてください。

5. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![PingBoard メタデータ xml](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-pingboard-tutorial/tutorial_general_400.png)

7. PingBoard 側で SSO を構成するには、新しいブラウザー ウィンドウを開き、PingBoard アカウントにログインします。 シングル サインオンを設定するには、PingBoard 管理者である必要があります。

8. 上部のメニューで、**[アプリ]、[統合]** の順に選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-pingboard-tutorial/Pingboard_integration.png)

9.  **[Integrations]** ページで、**[Azure Active Directory]** タイルを探してクリックします。

    ![PingBoard シングル サインオン統合](./media/active-directory-saas-pingboard-tutorial/Pingboard_aad.png)

10. 続いて表示されるモーダルで、**[Configure]** をクリックします。

    ![PingBoard 構成ボタン](./media/active-directory-saas-pingboard-tutorial/Pingboard_configure.png)

11. 次のページに、"Azure SSO 統合が有効になっている" ことを示す通知が表示されます。 ダウンロードしたメタデータ XML ファイルをメモ帳で開き、コンテンツを **[IDP Metadata]** に貼り付けます。

    ![PingBoard SSO 構成画面](./media/active-directory-saas-pingboard-tutorial/Pingboard_sso_configure.png)

12. ファイルが検証されます。すべて正しい場合は、シングル サインオンが有効になります。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-pingboard-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-pingboard-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![[追加] ボタン](./media/active-directory-saas-pingboard-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-pingboard-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="create-a-pingboard-test-user"></a>PingBoard テスト ユーザーを作成する

Azure AD ユーザーが PingBoard にログインできるようにするには、そのユーザーを PingBoard にプロビジョニングする必要があります。 PingBoard の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. PingBoard 企業サイトに管理者としてログインします。

2. **[Directory]** ページで **[Add Employee]** をクリックします。

    ![従業員の追加](./media/active-directory-saas-pingboard-tutorial/create_testuser_add.png)

3. **[従業員の追加]** ダイアログ ページで、次の手順に従います。

    ![[ユーザーの招待]](./media/active-directory-saas-pingboard-tutorial/create_testuser_name.png)

    a. **[フル ネーム]** ボックスに、ユーザーの氏名 (**Britta Simon** など) を入力します。

    b. **[メール]** ボックスに、ユーザーのメール アドレス (**brittasimon@contoso.com** など) を入力します。

    c. **[Job Title]** ボックスに、Britta Simon の役職を入力します。

    d. **[Location]** ドロップダウン リストで、Britta Simon の場所を選択します。
    
    e. **[追加]**をクリックします。   

4. ユーザーの追加を確認するための確認画面が表示されます。
    
    ![確認](./media/active-directory-saas-pingboard-tutorial/create_testuser_confirm.png)
        
    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PingBoard へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**PingBoard に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[PingBoard]** を選択します。

    ![アプリケーションの一覧の PingBoard のリンク](./media/active-directory-saas-pingboard-tutorial/tutorial_pingboard_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

アクセス パネルで [PingBoard] タイルをクリックすると、自動的に PingBoard アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-pingboard-tutorial/tutorial_general_203.png
