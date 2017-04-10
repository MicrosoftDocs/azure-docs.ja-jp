---
title: "チュートリアル: Azure Active Directory と Clarizen の統合 | Microsoft Docs"
description: "Azure Active Directory と Clarizen の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 3908c354a8551d7b16c759a48ef3e5316bfa11fa
ms.openlocfilehash: 574c6877bddac8be7d6d541bfabbdc10f6be3101
ms.lasthandoff: 02/21/2017


---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>チュートリアル: Azure Active Directory と Clarizen の統合

このチュートリアルでは、Azure Active Directory (Azure AD) を Clarizen と統合する方法について説明します。 この統合には次のようなメリットがあります。

- Clarizen にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Clarizen にサインイン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

このチュートリアルのシナリオは、主に次の&2; つの作業で構成されています。

1. ギャラリーからの Clarizen の追加。
2. Azure AD シングル サインオンの構成とテスト。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
Clarizen と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオンが有効な Clarizen サブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- テスト環境で Azure AD のシングル サインオンをテストします。 必要な場合を除き、運用環境は使用しないでください。
- Azure AD のテスト環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="add-clarizen-from-the-gallery"></a>ギャラリーからの Clarizen の追加
Azure AD への Clarizen の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Clarizen を追加します。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** のアイコンをクリックします。

    ![Azure Active Directory のアイコン][1]

2. **[エンタープライズ アプリケーション]** をクリックします。 次に、**[すべてのアプリケーション]** をクリックします。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] のクリック][2]

3. ダイアログ ボックスの上部にある **[追加]** ボタンをクリックします。

    ![[追加] ボタン][3]

4. 検索ボックスに、「**Clarizen**」と入力します。

    ![検索ボックスに「Clarizen」と入力](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_000.png)

5. 結果ウィンドウで **[Clarizen]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果ウィンドウでの Clarizen の選択](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_0001.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
次のセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Clarizen で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Clarizen ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Clarizen の関連ユーザーの間で、リンク関係が確立されている必要があります。 このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Clarizen の **[Username (ユーザー名)]** の値として割り当てます。

Clarizen で Azure AD のシングル サインオンを構成してテストするには、次の要素を完了します。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Clarizen テスト ユーザーの作成](#create-a-clarizen-test-user)** - Clarizen で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成
Azure Portal で Azure AD のシングル サインオンを有効にし、Clarizen アプリケーションでシングル サインオンを構成します。

1. Azure Portal の **Clarizen** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオン] のクリック][4]

2. **[シングル サインオン]** ダイアログ ボックスで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![[SAML ベースのサインオン] の選択](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_01.png)

3. **[Clarizen のドメインと URL]** セクションで、次の手順を実行します。

    ![識別子と応答 URL のボックス](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_02.png)

    a. **[識別子]** ボックスに、値として「**Clarizen**」と入力します。

    b. **[応答 URL]** ボックスに、**https://<company name>.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx** というパターンを使用して URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL を使用する必要があります。 ここでは、識別子に一意の文字列値を使用することをお勧めします。 実際の値を取得するには、[Clarizen サポート チーム](https://success.clarizen.com/hc/en-us/requests/new)に連絡してください。

4. **[SAML 署名証明書]** セクションで、**[新しい証明書の作成]** をクリックします。

    ![[新しい証明書の作成] のクリック](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_03.png)     

5. **[新しい証明書の作成]** ダイアログ ボックスで、カレンダー アイコンをクリックし、期限日を選択します。 その後、 **[保存]**をクリックします。

    ![期限日の選択と保存](./media/active-directory-saas-clarizen-tutorial/tutorial_general_300.png)

6. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![新しい証明書を有効にするチェック ボックスの選択](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_04.png)

7. **[Rollover certificate (ロールオーバー証明書)]** ダイアログ ボックスで、**[OK]** をクリックします。

    ![[OK] をクリックして証明書の有効化を確定](./media/active-directory-saas-clarizen-tutorial/tutorial_general_400.png)

8. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[Certificate (Base64) (証明書 (Base64))] をクリックしてダウンロードを開始](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_05.png)

9. **[Clarizen 構成]** セクションで、**[Clarizen の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[Clarizen の構成] のクリック](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_06.png)

    ![ファイルと URL が含まれた [サインオンの構成] ウィンドウ](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_07.png)

10. 別の Web ブラウザー ウィンドウで、Clarizen 企業サイトに管理者としてサインインします。

11. 自分のユーザー名をクリックし、**[Settings (設定)]** をクリックします。

    ![ユーザー名の下にある [Settings (設定)] のクリック](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_001.png "設定")

12. **[Global Settings (グローバル設定)]** タブをクリックします。 次に、**[Federated Authentication (フェデレーション認証)]** の横の **[edit (編集)]** をクリックします。

    ![[Global Settings (グローバル設定)] タブ](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_002.png "グローバル設定")

13. **[Federated Authentication (フェデレーション認証)]** ダイアログ ボックスで、次の手順を実行します。

    ![[Federated Authentication (フェデレーション認証)] ダイアログ ボックス](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_003.png "フェデレーション認証")

    a. **[Enable Federated Authentication (フェデレーション認証を有効にする)]** をオンにします。

    b. **[アップロード]** をクリックして、ダウンロードした証明書をアップロードします。

    c. **[Sign-in URL (サインイン URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    d. **[Sign-out URL (サインアウト URL)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[Sign-Out URL (サインアウト URL)]** の値を入力します。

    e. **[POST を使用]**を選択します。

    f. **[保存]**をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
Azure Portal で Britta Simon というテスト ユーザーを作成します。

![Azure AD のテスト ユーザーの名前と電子メール アドレス][100]

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のアイコンをクリックします。

    ![Azure Active Directory のアイコン](./media/active-directory-saas-clarizen-tutorial/create_aaduser_01.png)

2. **[ユーザーとグループ]** をクリックし、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![[ユーザーとグループ] と [すべてのユーザー] のクリック](./media/active-directory-saas-clarizen-tutorial/create_aaduser_02.png)

3. ダイアログ ボックスの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログ ボックスを開きます。

    ![[追加] ボタン](./media/active-directory-saas-clarizen-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![名前、電子メール アドレス、パスワードが入力済みの [ユーザー] ダイアログ ボックス](./media/active-directory-saas-clarizen-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。

### <a name="create-a-clarizen-test-user"></a>Clarizen テスト ユーザーの作成
Clarizen への Azure AD ユーザーのサインインを有効にするには、ユーザー アカウントをプロビジョニングする必要があります。 Clarizen の場合、プロビジョニングは手動で行います。

1. Clarizen 企業サイトに管理者としてサインインします。

2. **[ユーザー]**をクリックします。

    ![[People (ユーザー)] のクリック](./media/active-directory-saas-clarizen-tutorial/create_aaduser_001.png "ユーザー")

3. **[ユーザーの招待]**をクリックします。

    ![[Invite User (ユーザーの招待)] ボタン](./media/active-directory-saas-clarizen-tutorial/create_aaduser_002.png "ユーザーの招待")

4. **[Invite People (ユーザーの招待)]** ダイアログ ボックスで、次の手順を実行します。

    ![[Invite People (ユーザーの招待)] ダイアログ ボックス](./media/active-directory-saas-clarizen-tutorial/create_aaduser_003.png "ユーザーの招待")

    a. **[Email (電子メール)]** ボックスに、Britta Simon アカウントの電子メール アドレスを入力します。

    b. **[招待]**をクリックします。

    > [!NOTE]
    > Azure Active Directory アカウント所有者がメールを受信し、リンクに従ってアカウントを確認するとそのアカウントがアクティブになります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て
Britta Simon に Clarizen へのアクセスを許可することで、Britta Simon アカウントが Azure シングル サインオンを使用できるようにします。

![割り当て済みのテスト ユーザー][200]

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]** の順にクリックします。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] のクリック][201]

2. アプリケーションの一覧で **[Clarizen]** を選択します。

    ![一覧での Clarizen の選択](./media/active-directory-saas-clarizen-tutorial/tutorial_clarizen_50.png)

3. 左側のウィンドウで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] のクリック][202]

4. **[Add] \(追加)** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[追加] ボタンと [割り当ての追加] ダイアログ ボックス][203]

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンをクリックします。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト
アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Clarizen] タイルをクリックすると、Clarizen アプリケーションに自動的にサインインします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clarizen-tutorial/tutorial_general_203.png

