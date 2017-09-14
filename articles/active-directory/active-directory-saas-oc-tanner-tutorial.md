---
title: "チュートリアル: Azure Active Directory と O.C. の統合 Tanner - AppreciateHub の統合 | Microsoft Docs"
description: "Azure Active Directory と O.C. の間でシングル サインオンを構成する方法について説明します。 Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9af12372b30d9ee1575e46be3b4144fc3b73ec69
ms.contentlocale: ja-jp
ms.lasthandoff: 07/04/2017


---
# <a name="tutorial-azure-active-directory-integration-with-oc-tanner---appreciatehub"></a>チュートリアル: Azure Active Directory と O.C. の統合 Azure AD で制御できます。

このチュートリアルでは、O.C. を統合する方法について説明します。 Tanner - AppreciateHub と Azure Active Directory (Azure AD) を統合する方法を示すことです。

O.C.  Tanner - AppreciateHub と Azure AD の統合には、次の利点があります。

- O.C. Tanner - AppreciateHub にアクセスするユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に O.C.  Tanner にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と O.C.  Tanner - AppreciateHub の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- O.C.  Tanner - AppreciateHub でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの O.C.  Tanner - AppreciateHub の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>ギャラリーからの O.C.  Tanner - AppreciateHub の追加
Azure AD への O.C.  Tanner - AppreciateHub の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に O.C.  Tanner - AppreciateHub を追加する必要があります。

**ギャラリーから O.C. Tanner - AppreciateHub を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**O.C. Tanner - AppreciateHub** を選択します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_search.png)

5. 結果パネルで **[O.C.Tanner - AppreciateHub]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、O.C. を使用してAzure AD のシングル サインオンを構成し、テストします。 Tanner - AppreciateHub のシングル サインオンを構成し、テスト ユーザー "Britta Simon" を使用してテストする方法について説明することです。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する O.C.  Tanner - AppreciateHub ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと O.C.  Tanner - AppreciateHub の関連ユーザーの間で、リンク関係が確立されている必要があります。

O.C. Tanner - AppreciateHub で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

O.C.  Tanner - AppreciateHub で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[O.C. Tanner - AppreciateHub のテスト ユーザーの作成](#creating-a-oc-tanner---appreciatehub-test-user)** - O.C.  Tanner - AppreciateHub で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、O.C. でシングル サインオンを構成します。 Tanner - AppreciateHub  アプリケーションにサインオンします。

**O.C. Tanner - AppreciateHub で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Azure Portal の **O.C.Tanner - AppreciateHub** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_samlbase.png)

3. **[O.C.Tanner - AppreciateHub のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_url.png)

    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[応答 URL]** ボックスに、`https://<companyname>.appreciatehub.com/fed/sp/authnResponse20` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 実際の応答 URL でこの値を更新します。 [O.C.Tanner - AppreciateHub サポート チーム](mailto:sso@octanner.com)に問い合わせて、この値を入手してください。

    b. [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata)。
   
    c. **md:AssertionConsumerService** ノードを探します。 
   
    d. **[Location]** 属性の値をコピーします。 
   
    ![[アプリケーション設定の構成]][12]
   
    e. **[サインオン URL]** テキスト ボックスに、前の手順で取得した値を貼り付けます。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_400.png)

6. **O.C.Tanner - AppreciateHub** 側でシングル サインオンを構成するには、ダウンロードした **Metadata XML (メタデータ XML)** を [O.C.Tanner - AppreciateHub サポート チーム](mailto:sso@octanner.com)に送信する必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-oc-tanner---appreciatehub-test-user"></a>O.C.  Tanner - AppreciateHub テスト ユーザーの作成

このセクションの目的は、O.C.  Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。

**O.C. Tanner - AppreciateHub で Britta Simon というユーザーを作成するには、次の手順に従います。**

[O.C.Tanner - AppreciateHub サポート チーム](mailto:sso@octanner.com)に、Azure AD 内のユーザー名 Britta Simon と同じ値の nameID 属性を持つユーザーを作成することを依頼します。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に O.C. へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。 Tanner - AppreciateHub で Britta Simon というユーザーを作成することです。

![ユーザーの割り当て][200] 

**O.C. Tanner - AppreciateHub で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[O.C. Tanner - AppreciateHub]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-oc-tanner-tutorial/tutorial_octannerappreciatehub_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。  
アクセス パネルで O.C.  Tanner - AppreciateHub タイルをクリックすると、自動的に O.C.  Tanner - AppreciateHub  アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png

[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png

[100]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png


