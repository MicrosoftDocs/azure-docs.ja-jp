---
title: "チュートリアル: Azure Active Directory と Perception United States (非 UltiPro) の統合 | Microsoft Docs"
description: "Azure Active Directory と Perception United States (非 UltiPro) の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 8e2f9f979f8b94e0c043d4db6e93bd7a53c3dd27
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>チュートリアル: Azure Active Directory と Perception United States (非 UltiPro) の統合

このチュートリアルでは、Perception United States (非 UltiPro) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Perception United States (非 UltiPro) と Azure AD の統合には、次の利点があります。

- Perception United States (非 UltiPro) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Perception United States (非 UltiPro) にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Perception United States (非 UltiPro) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Perception United States (非 UltiPro) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Perception United States (非 UltiPro) を追加する
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>ギャラリーから Perception United States (非 UltiPro) を追加する
Azure AD への Perception United States (非 UltiPro) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Perception United States (非 UltiPro) を追加する必要があります。

**ギャラリーから Perception United States (非 UltiPro) を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Perception United States (非 UltiPro)**」と入力し、結果パネルで [**Perception United States (非 UltiPro)**] を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Perception United States (非 UltiPro)](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Perception United States (非 UltiPro) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Perception United States (非 UltiPro) ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Perception United States (非 UltiPro) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Perception United States (非 UltiPro) で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Perception United States (非 UltiPro) で Azure AD のシングル サインオンを構成してテストするには、次の手順を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Perception United States (非 UltiPro) のテスト ユーザーの作成](#create-a-perception-united-states-non-ultipro-test-user)** - Perception United States (非 UltiPro) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Perception United States (非 UltiPro) アプリケーションでシングル サインオンを構成します。

**Perception United States (非 UltiPro) で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Perception United States (非 UltiPro)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. **[Perception United States (非 UltiPro) のドメインと URL]** セクションで、次の手順に従います。

    ![[Perception United States (非 UltiPro) のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://perception.kanjoya.com/sp`

    b. **[応答 URL]** ボックスに、`https://perception.kanjoya.com/sso?idp=<entity_id>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > この値は実際のものではありません。 実際の応答 URL に値を置き換えます。実際の値については後で説明します。
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_400.png)

6. **[Perception United States (非 UltiPro) 構成]** セクションで、**[Perception United States (非 UltiPro) の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス] セクション**から **SAML エンティティ ID** をコピーします。

    a. **Perception United States (非 UltiPro)** アプリケーションでは、先ほどコピーした **SAML エンティティ ID** 値を URI でエンコードする必要があります。 URI でエンコードされた値を取得するには、**http://www.url-encode-decode.com/** リンクを使用してください。

    b. URI でエンコードされた値を取得した後に、次の説明に従って、その値を**応答 URL** と結合します。

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. **[Perception United States (非 UltiPro) のドメインと URL]** セクションの **[応答 URL]** ボックスに上記の値を貼り付けます。

    ![Perception United States (非 UltiPro) 構成](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. 別の Web ブラウザー ウィンドウで、管理者として Perception United States (非 UltiPro) 企業サイトにサインオンします。

8. メイン ツール バーで、**[Account Settings]** をクリックします。

    ![Perception United States (非 UltiPro) ユーザー](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. **[Account Settings]** ページで、次の手順を実行します。

    ![Perception United States (非 UltiPro) ユーザー](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. **[Company Name]** ボックスに、**会社**の名前を入力します。
    
    b. **[Account Name]** ボックスに、**アカウント**の名前を入力します。

    c. **[Default Reply-To Email]** ボックスに、有効な**電子メール**を入力します。

    d. **[SSO Identity Provider]** として **[SAML 2.0]** を選択します。

10. **[SSO 構成]** ページで、次の手順を実行します。

    ![Perception United States (非 UltiPro) SSOConfig](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. **[SAML NameID Type]** として **[EMAIL]** を選択します。

    b. **[SSO Configuration Name]** ボックスに**構成**の名前を入力します。
    
    c. **[Identity Provider Name]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。 

    d. **[SAML Domain]** ボックスに「**@contoso.com**」のようにドメインを入力します。

    e. **[Upload Again]** をクリックして、**メタデータ XML** ファイルをアップロードします。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Update]**をクリックします。


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Perception United States (非 UltiPro) のテスト ユーザーの作成

このセクションでは、Perception United States (非 UltiPro) で Britta Simon というユーザーを作成します。 [Perception United States (非 UltiPro) サポート チーム](http://www.ultimatesoftware.com/Contact/ContactUs)と連携し、Perception United States (非 UltiPro) プラットフォームにユーザーを追加してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Perception United States (非 UltiPro) へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Perception United States (非 UltiPro) に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Perception United States (非 UltiPro)]** を選択します。

    ![アプリケーションの一覧の Perception United States (非 UltiPro) のリンク](./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Perception United States (非 UltiPro) のタイルをクリックすると、自動的に Perception United States (非 UltiPro) アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-perceptionunitedstates-tutorial/tutorial_general_203.png


