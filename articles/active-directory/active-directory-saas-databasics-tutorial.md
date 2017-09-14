---
title: "チュートリアル: Azure Active Directory と DATABASICS の統合 | Microsoft Docs"
description: "Azure Active Directory と DATABASICS の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a37ded45-84c8-4e88-8d9b-c5b9443eb0d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 26943282701302b61252e17850b71381afb1629b
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="tutorial-azure-active-directory-integration-with-databasics"></a>チュートリアル: Azure Active Directory と DATABASICS の統合

このチュートリアルでは、DATABASICS と Azure Active Directory (Azure AD) を統合する方法について説明します。

DATABASICS と Azure AD の統合には、次の利点があります。

- DATABASICS にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に DATABASICS にサインオン (シングル サインオン) できるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

DATABASICS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- DATABASICS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの DATABASICS の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-databasics-from-the-gallery"></a>ギャラリーからの DATABASICS の追加
Azure AD への DATABASICS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に DATABASICS を追加する必要があります。

**ギャラリーから DATABASICS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**DATABASICS**」と入力し、結果パネルで **[DATABASICS]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![結果リストの DATABASICS](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、DATABASICS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する DATABASICS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと DATABASICS の関連ユーザーの間で、リンク関係が確立されている必要があります。

DATABASICS で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

DATABASICS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[DATABASICS テスト ユーザーの作成](#create-a-databasics-test-user)** - DATABASICS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、DATABASICS アプリケーションでシングル サインオンを構成します。

**DATABASICS で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **DATABASICS** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_samlbase.png)

3. **[DATABASICS のドメインと URL]** セクションで、次の手順を実行します。

    ![[DATABASICS のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_url.png)

    a. **[識別子]** テキストボックスに、値として「`DATA-BASICS_SP`」を入力します。
    
    b. **[サインオン URL]** ボックスに、`https://<sitenumber>.data-basics.net/<clientname>/saml_sso.jsp` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[DATABASICS クライアント サポート チーム](https://www.data-basics.com/support/)に連絡してください。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-databasics-tutorial/tutorial_general_400.png)

6. **DATABASICS** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** を [DATABASICS サポート チーム](https://www.data-basics.com/support/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-databasics-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-databasics-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-databasics-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-databasics-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。
 
### <a name="create-a-databasics-test-user"></a>DATABASICS テスト ユーザーの作成

このセクションでは、DATABASICS で Britta Simon というユーザーを作成します。 [DATABASICS サポート チーム](https://www.data-basics.com/support/)と連携し、DATABASICS プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に DATABASICS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**DATABASICS に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[DATABASICS]** を選択します。

    ![アプリケーションの一覧の DATABASICS のリンク](./media/active-directory-saas-databasics-tutorial/tutorial_databasics_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで DATABASICS のタイルをクリックすると、自動的に DATABASICS アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-databasics-tutorial/tutorial_general_203.png


