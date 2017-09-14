---
title: "チュートリアル: Azure Active Directory と Envoy の統合 | Microsoft Docs"
description: "Azure Active Directory とEnvoy の間にシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 71f7afcc-1033-4098-9b7e-4f9f2b26f734
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 49211b35ab3e28e0df914061e7fa623907935638
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-envoy"></a>チュートリアル: Azure Active Directory と Envoy の統合

このチュートリアルでは、Envoy と Azure Active Directory (Azure AD) を統合する方法について説明します。

Envoy と Azure AD の統合には、次の利点があります。

- Envoy にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Envoy にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Envoy と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Envoy でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Envoy の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-envoy-from-the-gallery"></a>ギャラリーからの Envoy の追加
Azure AD への Envoy の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Envoy を追加する必要があります。

**ギャラリーから Envoy を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Envoy**」と入力し、結果ウィンドウで **[Envoy]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの Envoy](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Envoy で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Envoy ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Envoy の関連ユーザーの間で、リンク関係が確立されている必要があります。

Envoy で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Envoy で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Envoy のテスト ユーザーの作成](#create-an-envoy-test-user)** - Envoy で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Envoy アプリケーションでシングル サインオンを構成します。

**Envoy で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Envoy** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_samlbase.png)

3. **[Envoy のドメインと URL]** セクションで、次の手順を実行します。

    ![[Envoy のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_url.png)

    **[サインオン URL]** ボックスに、`https://<tenant-name>.Envoy.com` のパターンを使用して URL を入力します。
    
    > [!NOTE] 
    > これは実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[Envoy クライアント サポート チーム](https://envoy.com/contact/)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、証明書の **THUMBPRINT (拇印)** の値をコピーします。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-envoy-tutorial/tutorial_general_400.png)

6. **[Envoy Configuration]\(Envoy 構成\)** セクションで、**[Configure Envoy]\(Envoy を構成する\)** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![Envoy の構成](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_configure.png)

7. 別の Web ブラウザー ウィンドウで、Envoy 企業サイトに管理者としてログインします。

8. 上部のツールバーで **[Settings]**をクリックします。

    ![Envoy](./media/active-directory-saas-envoy-tutorial/ic776782.png "Envoy")

9. **[会社]**をクリックします。

    ![Company](./media/active-directory-saas-envoy-tutorial/ic776783.png "Company")

10. **[SAML]**をクリックします。

    ![SAML](./media/active-directory-saas-envoy-tutorial/ic776784.png "SAML")

11. **[SAML 認証]** 構成セクションで、次の手順を実行します。

    ![SAML authentication](./media/active-directory-saas-envoy-tutorial/ic776785.png "SAML authentication")
    
    >[!NOTE]
    >[HQ 場所 ID] の値は、アプリケーションによって自動的に生成されます。
    
    a. **[Fingerprint]\(フィンガープリント\)** ボックスに、Azure Portal からコピーした証明書の **Thumbprint (拇印)** 値を貼り付けます。
    
    b. Azure Portal からコピーした **SAML シングル サインオン サービスの URL** を **[IDENTITY PROVIDER HTTP SAML URL]\(ID プロバイダーのHTTP SAML URL\)** ボックスに貼り付けます。
    
    c. **[変更を保存]**をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-envoy-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-envoy-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-envoy-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-envoy-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。
 
### <a name="create-an-envoy-test-user"></a>Envoy テスト ユーザーの作成

Envoy へのユーザー プロビジョニングの構成にあたって必要な操作はありません。 割り当てられたユーザーがアクセス パネルを使用して Envoy にログインしようとすると、そのユーザーが存在するかどうかが Envoy によって確認されます。 使用可能なユーザー アカウントがない場合、ユーザー アカウントは自動的に作成されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Envoy へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Britta Simon を Envoy に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Envoy]**を選択します。

    ![アプリケーションの一覧の Envoy のリンク](./media/active-directory-saas-envoy-tutorial/tutorial_envoy_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Envoy] タイルをクリックすると、Envoy アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-envoy-tutorial/tutorial_general_203.png


