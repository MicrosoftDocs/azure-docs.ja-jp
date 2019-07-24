---
title: チュートリアル:Azure Active Directory と Workteam の統合 | Microsoft Docs
description: Azure Active Directory と Workteam の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7cd986544dfb1472f5cc8a013fec951dca42a59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57898654"
---
# <a name="tutorial-azure-active-directory-integration-with-workteam"></a>チュートリアル:Azure Active Directory と Workteam の統合

このチュートリアルでは、Workteam と Azure Active Directory (Azure AD) を統合する方法について説明します。

Workteam と Azure AD の統合には、次の利点があります。

- Workteam にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Workteam にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Workteam と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workteam でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workteam の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-workteam-from-the-gallery"></a>ギャラリーからの Workteam の追加
Azure AD への Workteam の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Workteam を追加する必要があります。

**ギャラリーから Workteam を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Workteam**」と入力し、結果パネルで **[Workteam]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Workteam](./media/workteam-tutorial/tutorial_workteam_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workteam で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workteam ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workteam の関連ユーザーの間で、リンク関係が確立されている必要があります。

Workteam で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Workteam テスト ユーザーの作成](#create-a-workteam-test-user)** - Workteam で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Workteam アプリケーションでシングル サインオンを構成します。

**Workteam で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Workteam** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/workteam-tutorial/tutorial_workteam_samlbase.png)

3. アプリケーションは Azure と事前に統合済みであるため、**[Workteam のドメインと URL]** セクションで実行が必要な手順はありません。

    ![[Workteam のドメインと URL] のシングル サインオン情報](./media/workteam-tutorial/tutorial_workteam_url.png)

4. アプリケーションを **SP** 開始モードで構成する場合は、**[詳細な URL 設定の表示]** チェックボックスをオンにして次の手順を実行します。

    ![[Workteam のドメインと URL] のシングル サインオン情報](./media/workteam-tutorial/tutorial_workteam_url1.png)

    **[サインオン URL]** ボックスに、「`https://app.workte.am`」と入力します。
     
5. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/workteam-tutorial/tutorial_workteam_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/workteam-tutorial/tutorial_general_400.png)
    
7. **[Workteam 構成]** セクションで、**[Workteam の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![Workteam 構成](./media/workteam-tutorial/tutorial_workteam_configure.png) 

8. 別の Web ブラウザー ウィンドウで、Workteam にセキュリティ管理者としてログインします。

9. 右上隅にある**プロファイル ロゴ**をクリックし、**[Organization settings]\(組織の設定\)** をクリックします。 

    ![Workteam の設定](./media/workteam-tutorial/tutorial_workteam_settings.png)

10. **[AUTHENTICATION]\(認証\)** セクションで、**設定ロゴ**をクリックします。

     ![Workteam - Azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

11. **[SAML Settings]** ページで、次の手順を実行します。

     ![Workteam - SAML](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. **[SAML IdP]** で **[AD Azure]** を選択します。

    b. **[SAML Single Sign-On Service URL]\(SAML シングル サインオン サービス URL\)** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[SAML Entity ID]\(SAML エンティティ ID\)** ボックスに、Azure portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    d. Azure portal からダウンロードした **Base-64 でエンコードされた証明書**をメモ帳で開き、その内容をコピーして **[SAML Signing Certificate (Base64)]\(SAML 署名証明書 (Base64)\)** ボックスに貼り付けます。

    e. Click **OK**.

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/workteam-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/workteam-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/workteam-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/workteam-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-workteam-test-user"></a>Workteam テスト ユーザーの作成

Azure AD ユーザーが Workteam にログインできるようにするには、ユーザーを Workteam にプロビジョニングする必要があります。 Workteam では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として Workteam にログインします。

2. **[Organization settings]\(組織の設定\)** ページで、上部中央の **[USERS]\(ユーザー\)** をクリックし、**[NEW USER]\(新しいユーザー\)** をクリックします。

    ![Workteam - ユーザー](./media/workteam-tutorial/tutorial_workteam_user.png)

3. **[New employee]\(新しい従業員\)** ページで、次の手順を実行します。

    ![Workteam - 新しい従業員](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. **[Name]\(名\)** ボックスに、ユーザーの名を入力します (例: **Brittasimon**)。

    b. **[Email]\(メール\)** ボックスに、ユーザーのメール アドレス (例: **Brittasimon\@contoso.com**) を入力します。

    c. Click **OK**.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workteam へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Workteam に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Workteam]** を選択します。

    ![アプリケーションの一覧の Workteam のリンク](./media/workteam-tutorial/tutorial_workteam_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Workteam] タイルをクリックすると、自動的に Workteam アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/workteam-tutorial/tutorial_general_01.png
[2]: ./media/workteam-tutorial/tutorial_general_02.png
[3]: ./media/workteam-tutorial/tutorial_general_03.png
[4]: ./media/workteam-tutorial/tutorial_general_04.png

[100]: ./media/workteam-tutorial/tutorial_general_100.png

[200]: ./media/workteam-tutorial/tutorial_general_200.png
[201]: ./media/workteam-tutorial/tutorial_general_201.png
[202]: ./media/workteam-tutorial/tutorial_general_202.png
[203]: ./media/workteam-tutorial/tutorial_general_203.png

