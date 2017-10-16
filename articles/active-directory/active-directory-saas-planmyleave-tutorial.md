---
title: "チュートリアル: Azure Active Directory と PlanMyLeave の統合 | Microsoft Docs"
description: "Azure Active Directory と PlanMyLeave の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b0d31cbe-7ae2-488b-9cf3-4927391fa744
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: jeedes
ms.openlocfilehash: f803378424d2c951e0ff85d80df72645af342208
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-planmyleave"></a>チュートリアル: Azure Active Directory と PlanMyLeave の統合

このチュートリアルでは、PlanMyLeave と Azure Active Directory (Azure AD) を統合する方法について説明します。

PlanMyLeave と Azure AD の統合には、次の利点があります。

- PlanMyLeave にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に PlanMyLeave にサインオン (シングル サインオン) するよう指定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

PlanMyLeave と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- PlanMyLeave でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの PlanMyLeave の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-planmyleave-from-the-gallery"></a>ギャラリーからの PlanMyLeave の追加
Azure AD への PlanMyLeave の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に PlanMyLeave を追加する必要があります。

**ギャラリーから PlanMyLeave を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**PlanMyLeave**」と入力し、結果ウィンドウで **[PlanMyLeave]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの PlanMyLeave](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、PlanMyLeave で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する PlanMyLeave ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと PlanMyLeave の関連ユーザーの間で、リンク関係が確立されている必要があります。

PlanMyLeave で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てて、リンク関係を確立します。

PlanMyLeave で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[PlanMyLeave テスト ユーザーの作成](#create-a-planmyleave-test-user)** - PlanMyLeave で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、PlanMyLeave アプリケーションでシングル サインオンを構成します。

**PlanMyLeave で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **PlanMyLeave** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_samlbase.png)

3. **[PlanMyLeave のドメインと URL]** セクションで、次の手順を実行します。

    ![[PlanMyLeave のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company-name>.planmyleave.com/Login.aspx` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<company-name>.planmyleave.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[PlanMyLeave クライアント サポート チーム](mailto:support@planmyleave.com)にお問い合わせください。 
 
4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-planmyleave-tutorial/tutorial_general_400.png)

6. **[PlanMyLeave Configuration (PlanMyLeave 構成)]** セクションで、**[Configure PlanMyLeave (PlanMyLeave を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![PlanMyLeave 構成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_configure.png) 
7. 別の Web ブラウザーのウィンドウで、管理者として PlanMyLeave テナントにログインします。

8. **[System Setup (システム セットアップ)]** に移動します。 次に、**[セキュリティ管理]** セクションで、**[Company SAML settings (会社の SAML 設定)]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_002.png) 

9. **[SAML 設定]** セクションで、エディターのアイコンをクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_003.png)

10. **[Update SAML Settings (SAML 設定の更新)]** セクションで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_004.png)

    a.  **[Login URL]\(ログイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    b.  ダウンロードしたメタデータを開き、**X509Certificate** 値をコピーして、**[証明書]** ボックスに貼り付けます。

    c. **[Is Enable (有効)]** を **[はい]** に設定します。

    d. [ **Save**] をクリックします。 

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-planmyleave-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-planmyleave-test-user"></a>PlanMyLeave テスト ユーザーの作成

このセクションの目的は、PlanMyLeave で Britta Simon というユーザーを作成することです。 PlanMyLeave では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない PlanMyLeave ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[PlanMyLeave のサポート チーム](mailto:support@planmyleave.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に PlanMyLeave へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザー ロールを割り当てる][200] 

**PlanMyLeave に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[PlanMyLeave]** を選択します。

    ![アプリケーションの一覧の PlanMyLeave のリンク](./media/active-directory-saas-planmyleave-tutorial/tutorial_planmyleave_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [PlanMyLeave] タイルをクリックすると、自動的に PlanMyLeave アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-planmyleave-tutorial/tutorial_general_203.png

