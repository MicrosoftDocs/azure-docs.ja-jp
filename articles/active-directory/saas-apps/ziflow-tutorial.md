---
title: チュートリアル:Azure Active Directory と Ziflow の統合 | Microsoft Docs
description: Azure Active Directory と Ziflow の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175e678365016bafd3d18f590a5434c32ac9fadd
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176477"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>チュートリアル:Azure Active Directory と Ziflow の統合

このチュートリアルでは、Ziflow と Azure Active Directory (Azure AD) を統合する方法について説明します。

Ziflow と Azure AD の統合には、次の利点があります。

- Ziflow にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Ziflow にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Ziflow と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Ziflow シングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Ziflow の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-ziflow-from-the-gallery"></a>ギャラリーからの Ziflow の追加
Azure AD への Ziflow の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Ziflow を追加する必要があります。

**ギャラリーから Ziflow を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Ziflow**」と入力し、結果ウィンドウで **[Ziflow]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Ziflow](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Ziflow で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Ziflow ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Ziflow の関連ユーザーの間で、リンク関係が確立されている必要があります。

Ziflow で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Ziflow テスト ユーザーの作成](#create-a-ziflow-test-user)** - Ziflow で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Ziflow アプリケーションでシングル サインオンを構成します。

**Ziflow で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Ziflow** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. **[Ziflow Domain and URLs (Ziflow ドメインと URL)]** セクションで、次の手順を実行します。

    ![[Ziflow Domain and URLs (Ziflow ドメインと URL)] のシングル サインオン情報](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. **[サインオン URL]** ボックスに、`https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`urn:auth0:ziflow-production:<UniqueID>` の形式で URL を入力します。

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 識別子とサインオン URL 内の固有の ID を実際の値に置き換えます。実際の値については後で説明します。

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/ziflow-tutorial/tutorial_general_400.png)

6. **[Ziflow 構成]** セクションで、**[Ziflow の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![Ziflow の構成](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Ziflow にログインします。

8. 右上隅のアバターをクリックして、**[アカウントの管理]** をクリックします。

    ![Ziflow の構成の管理](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. 左上の **シングル サインオン** をクリックします。

    ![Ziflow の構成 - サイン](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. **Single sign-on**(シングル サインオン) ページで、次の手順を実行します。

    ![Ziflow の構成 - シングル](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. **[SAML2.0]** として **[種類]** を選択します。

    b. **[サインイン URL]** ボックスに、Azure portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. Azure Portal からダウンロードした base 64 でエンコードされた証明書を **X509 署名証明書**にアップロードします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Sign Out URL (サインアウト URL)]** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    e. **[Configuration Settings for your Identifier Provider (ID プロバイダーの構成設定)]** セクションで、強調表示されている一意の ID 値をコピーし、Azure Portal の **[Ziflow Domain and URLs (Ziflow ドメインと URL)]** セクションで、ID とサインオン URL に追加します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/ziflow-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/ziflow-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/ziflow-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/ziflow-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
  
### <a name="create-a-ziflow-test-user"></a>Ziflow のテスト ユーザーの作成

Azure AD ユーザーが Ziflow にログインできるようにするには、そのユーザーを Ziflow にプロビジョニングする必要があります。 Ziflow では、プロビジョニングは手動で行います。

ユーザー アカウントをプロビジョニングするには、次の手順を実行します。

1. セキュリティ管理者として Ziflow にログインします。

2. 上部の **[ユーザー]** に移動します。

    ![Ziflow の構成 - ユーザー](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. **[追加]**、**[ユーザーの追加]** の順にクリックします。

    ![Ziflow の構成 - ユーザーの追加](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. **[ユーザーの追加]** ポップアップで、次の手順を実行します。

    ![Ziflow の構成 - ユーザーの追加](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: brittasimon@contoso.com)。

    b. **[名]** ボックスに、ユーザーの名を入力します (例: Britta)。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (例: Simon)。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 Ziflow のロールを選択します。

    e. **[1 ユーザーの追加]** をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者が電子メールを受信し、リンクに従ってアカウントを確認すると、そのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Ziflow へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Ziflow に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Ziflow]** を選択します。

    ![アプリケーションの一覧の [Ziflow] リンク](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Ziflow] タイルをクリックすると、Ziflow アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

