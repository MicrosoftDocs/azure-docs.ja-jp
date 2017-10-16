---
title: "チュートリアル: Azure Active Directory と OpenAthens の統合 | Microsoft Docs"
description: "Azure Active Directory と OpenAthens の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2017
ms.author: jeedes
ms.openlocfilehash: 2819e300d94a0bc3b0900419218561fc846effcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>チュートリアル: Azure Active Directory と OpenAthens の統合

このチュートリアルでは、OpenAthens と Azure Active Directory (Azure AD) を統合する方法について説明します。

OpenAthens と Azure AD の統合には、次の利点があります。

- OpenAthens にアクセスするユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に OpenAthens にサインオン (シングル サインオン) されるように指定できます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

OpenAthens と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- OpenAthens でのシングル サインオンが有効なサブスクリプション

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの OpenAthens の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-openathens-from-the-gallery"></a>ギャラリーからの OpenAthens の追加
Azure AD への OpenAthens の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OpenAthens を追加する必要があります。

**ギャラリーから OpenAthens を追加するには**

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** アイコンを選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]**、**[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ウィンドウ][2]
    
3. 新しいアプリケーションを追加するには、ダイアログ ボックスの上部にある **[新しいアプリケーション]** ボタンを選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**OpenAthens**」と入力し、結果パネルで **[OpenAthens]** を選択して、**[追加]** を選択します。

    ![結果一覧の OpenAthens](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" という名前のテスト ユーザーに基づいて、OpenAthens で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する OpenAthens ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと OpenAthens の関連ユーザーの間で、リンク関係が確立されている必要があります。

OpenAthens で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

OpenAthens で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. [Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on) - ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user) - Britta Simon で Azure AD のシングル サインオンをテストします。
3. [OpenAthens テスト ユーザーの作成](#create-a-openathens-test-user) - OpenAthens で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. [Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user) - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. [シングル サインオンのテスト](#test-single-sign-on) - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、OpenAthens アプリケーションでシングル サインオンを構成します。

**OpenAthens で Azure AD シングル サインオンを構成するには**

1. Azure Portal の **OpenAthens** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク][4]

2. シングル サインオンを有効にするには、**[シングル サインオン]** ダイアログ ボックスで、**[モード]** として **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_samlbase.png)

3. **[OpenAthens のドメインと URL]** セクションの **[識別子]**ボックスに、値「`https://login.openathens.net/saml/2/metadata-sp`」を入力します。

    ![[OpenAthens のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_url.png)

4. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** を選択し、コンピューターにメタデータ ファイルを保存します。

    ![AMSL 署名証明書のダウンロードのリンク](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_certificate.png) 

5. **[保存]** を選択します。

    ![[シングル サインオン] の [保存] ボタン](./media/active-directory-saas-openathens-tutorial/tutorial_general_400.png)

6. 別の Web ブラウザー ウィンドウで、OpenAthens 企業サイトに管理者としてログインします。

7. **[管理]** タブの一覧から **[接続]** を選択します。 

    ![Configure single sign-on](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application1.png)

8. **[SAML 1.1/2.0]** を選択し、**[構成]** を選択します。

    ![Configure single sign-on](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application2.png)
    
9. 構成を追加するには、**[参照]** を選択して Azure Portal からダウンロードしたメタデータ .xml ファイルをアップロードし、**[追加]** を選択します。

    ![Configure single sign-on](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application3.png)

10. **[詳細]** タブで、次の手順を実行します。

    ![Configure single sign-on](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_application4.png)

    a. **[Display name mapping]\(表示名マッピング\)** で、**[Use attribute]\(属性の使用\)** を選択します。

    b. **[Display name attribute]\(表示名属性\)** ボックスに、値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」を入力します。
    
    c. **[Unique user mapping]\(一意のユーザー マッピング\)** で、**[Use attribute]\(属性の使用\)** を選択します。

    d. **[Display name attribute]\(一意のユーザ属性\)** ボックスに、値「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」を入力します。

    e. **[状態]** で、3 つのチェック ボックスすべてをオンにします。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[Create local accounts]\(ローカル アカウントの作成\)** で、**[automatically]\(自動\)** を選択します。

    g. **[変更の保存]** を選択します。

> [!TIP]
> アプリのセットアップ中、[Azure ポータル](https://portal.azure.com)内で上記の手順の簡易版を確認できるようになりました。 **[Active Directory]**  >  **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブを選択し、一番下の **[構成]** セクションから組み込みドキュメントにアクセスします。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント](https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で "Britta Simon" というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには**

1. Azure Portal の左側のウィンドウで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](./media/active-directory-saas-openathens-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** を選びます。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-openathens-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** を選択します。

    ![[追加] ボタン](./media/active-directory-saas-openathens-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-openathens-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、Britta Simon のメール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**を選択します。
  
### <a name="create-an-openathens-test-user"></a>OpenAthens テスト ユーザーの作成

OpenAthens では、Just-In-Time プロビジョニングがサポートされています。ユーザーは、認証が成功した後に自動的に作成されます。 このセクションでは、何も行う必要はありません。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OpenAthens へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**OpenAthens に Britta Simon を割り当てるには**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

2. **アプリケーション**の一覧で **[OpenAthens]** を選択します。

    ![アプリケーションの一覧の OpenAthens のリンク](./media/active-directory-saas-openathens-tutorial/tutorial_openathens_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンを選びます。 **[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ボックスの一覧で **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ボックスの一覧で **[選択]** を選択します。

7. **[割り当ての追加]** ウィンドウで、**[割り当て]** を選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで **[OpenAthens]** タイルを選択すると、自動的に OpenAthens アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* SaaS アプリと Azure Active Directory を統合する方法のチュートリアルの一覧については、[Azure AD で使用するための SaaS アプリ統合のチュートリアル](active-directory-saas-tutorial-list.md)に関するページをご覧ください。
* Azure Active Directory でのアプリケーション アクセスとシングル サインオンの詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

<!--Image references-->

[1]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-openathens-tutorial/tutorial_general_203.png

