---
title: "チュートリアル: Azure Active Directory と Versal の統合 | Microsoft Docs"
description: "Azure Active Directory と Versal の間でシングル サインオンを構成する方法について確認します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5b2e53c0-61a3-4954-ae46-8c28c6368bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 546610d05d0ed261f671df651cd6cc74837063f7
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="tutorial-azure-active-directory-integration-with-versal"></a>チュートリアル: Azure Active Directory と Versal の統合

このチュートリアルでは、Versal と Azure Active Directory (Azure AD) を統合する方法について説明します。

Versal と Azure AD の統合には、次の利点があります。

- Versal にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Versal にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Versal と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Versal でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Versal の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-versal-from-the-gallery"></a>ギャラリーからの Versal の追加
Azure AD への Versal の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Versal を追加する必要があります。

**ギャラリーから Versal を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Versal**」と入力し、結果ウィンドウで **[Versal]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Versal](./media/active-directory-saas-versal-tutorial/tutorial_versal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Versal で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Versal ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Versal の関連ユーザーの間で、リンク関係が確立されている必要があります。

Versal で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Versal で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Versal テスト ユーザーの作成](#create-a-versal-test-user)** - Versal で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Versal アプリケーションでシングル サインオンを構成します。

**Versal で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **Versal** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-versal-tutorial/tutorial_versal_samlbase.png)

3. **[Versal のドメインと URL]** セクションで、次の手順を実行します。

    ![[Versal のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-versal-tutorial/tutorial_versal_url.png)

    a. **[識別子]** テキストボックスに、値として「`VERSAL`」を入力します。

    b. **[応答 URL]** ボックスに、`https://versal.com/sso/saml/orgs/<organization_id>` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Versal サポート チーム](https://support.versal.com/hc/)に問い合わせてください。
    
4. アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、**Versal** はこれがユーザーの電子メール アドレスにマップされることを想定します。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。
    
    ![[ユーザー識別子] ドロップダウン メニュー](./media/active-directory-saas-versal-tutorial/tutorial_versal_attribute.png)

5. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-versal-tutorial/tutorial_versal_certificate.png) 

6. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-versal-tutorial/tutorial_general_400.png)
    
7. **Versal** 側でシングル サインオンを構成するには、ダウンロードした**メタデータ XML** と **SAML 署名証明書**を [Versal サポート チーム](https://support.versal.com/hc/)に送信する必要があります。 サポート チームは、SAML SSO 接続が両方の側で正しく設定されるように Versal 組織をセットアップします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-versal-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-versal-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-versal-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-versal-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。
  
### <a name="create-a-versal-test-user"></a>Versal テスト ユーザーの作成

このセクションでは、Versal で Britta Simon というユーザーを作成します。 「[Creating a SAML test user](https://support.versal.com/hc/en-us/articles/115011672887-Creating-a-SAML-test-user)」(SAML テスト ユーザーの作成) に関するサポート ガイドに従って、組織内にユーザー Britta Simon を作成します。 シングル サインオンを使用する前に、Versal 内にユーザーを作成して有効化しておく必要があります。 

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Versal へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Versal に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Versal]**を選択します。

    ![アプリケーションの一覧の Versal リンク](./media/active-directory-saas-versal-tutorial/tutorial_versal_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、Web サイトに埋め込まれた Versal コースを使用して Azure AD のシングル サインオン構成をテストします。
Azure AD シングル サインオンのサポートによる Versal コースの埋め込み方法の手順については、「[Embedding Organizational Courses](https://support.versal.com/hc/en-us/articles/203271866-Embedding-organizational-courses)」(組織コースの埋め込み) の「**SAML Single Sign-On**」(SAML シングル サインオン) サポート ガイドを参照してください。 

テスト コースの埋め込みをテストするには、コースを作成し、組織で共有し、公開する必要があります。 詳細については、「[Creating a course](https://support.versal.com/hc/en-us/articles/203722528-Create-a-course)(コースの作成)、「[Publishing a course](https://support.versal.com/hc/en-us/articles/203753398-Publishing-a-course)」(コースの公開)、および「[Course and learner management](https://support.versal.com/hc/en-us/articles/206029467-Course-and-learner-management)」(コースと学習者の管理) を参照してください。  
                     

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-versal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-versal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-versal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-versal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-versal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-versal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-versal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-versal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-versal-tutorial/tutorial_general_203.png


