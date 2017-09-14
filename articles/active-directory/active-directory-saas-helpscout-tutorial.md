---
title: "チュートリアル: Azure Active Directory と Help Scout の統合 | Microsoft Docs"
description: "Azure Active Directory と Help Scout の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 84cee39c28a0f7e6b9878441e504131795673020
ms.contentlocale: ja-jp
ms.lasthandoff: 08/28/2017

---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>チュートリアル: Azure Active Directory と Help Scout の統合

このチュートリアルでは、Help Scout と Azure Active Directory (Azure AD) を統合する方法について説明します。

Help Scout と Azure AD の統合には、次の利点があります。

- Azure AD で、Help Scout にアクセスするユーザーを制御できます。
- ユーザーがシングル サインオンと自分の Azure AD アカウントを使って自動的に Help Scout にサインインできるようになります。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Help Scout と Azure AD の統合を設定するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオンが有効になっている Help Scout サブスクリプション 

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストする際の推奨事項:

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の無料試用版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 

このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから Help Scout を追加する。
2. Azure AD シングル サインオンを設定してテストする。

## <a name="add-help-scout-from-the-gallery"></a>ギャラリーから Help Scout を追加する。
Azure AD と Help Scout の統合を設定するには、ギャラリーから管理対象 SaaS アプリの一覧に Help Scout を追加する必要があります。

ギャラリーから Help Scout を追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) の左側のメニューで、**[Azure Active Directory]** を選択します。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ページ][2]
    
3. 新しいアプリケーションを追加するには、**[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Help Scout**」と入力します。 検索結果で **[Help Scout]** を選択し、**[追加]** を選択します。

    ![結果リストの Help Scout](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_addfromgallery.png)

## <a name="set-up-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの設定とテスト

このセクションでは、"*Britta Simon*" というテスト ユーザーに基づいて、Help Scout で Azure AD のシングル サインオンを設定してテストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Help Scout ユーザーが Azure AD で認識されている必要があります。 Azure AD ユーザーと Help Scout の関連ユーザーの間で、リンク関係が確立されている必要があります。

リンク関係を確立するには、Help Scout で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当てます。

Help Scout で Azure AD のシングル サインオンを構成してテストするには、次のタスクを完了する必要があります。

1. [Azure AD シングル サインオンを設定する](#set-up-azure-ad-single-sign-on)。 この機能を使用するユーザーを設定します。
2. [Azure AD のテスト ユーザーを作成する](#create-an-azure-ad-test-user)。 ユーザー Britta Simon を使用して Azure AD シングル サインオンをテストします。
3. [Help Scout テスト ユーザーを作成する](#create-a-help-scout-test-user)。 Help Scout で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. [Azure AD のテスト ユーザーを割り当てる](#assign-the-azure-ad-test-user)。 Azure AD シングル サインオンを使用するよう Britta Simon を設定します。
5. [シングル サインオンをテストする](#test-single-sign-on)。 構成が機能することを確認します。

### <a name="set-up-azure-ad-single-sign-on"></a>Azure AD シングル サインオンを設定する

このセクションでは、Azure Portal で Azure AD シングル サインオンを設定します。 次に、Help Scout アプリケーションでシングル サインオンを設定します。

Help Scout で Azure AD シングル サインオンを設定するには、次の手順に従います。

1. Azure Portal の **Help Scout** アプリケーション統合ページで、**[シングル サインオン]** を選択します。
 
    ![シングル サインオンの設定リンク][4]

2. **[シングル サインオン]** ページで、**[モード]** として **[SAML ベースのサインオン]** を選択します。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_samlbase.png)

3. アプリケーションを IDP 開始モードで構成する場合は、**[Help Scout のドメインと URL]** セクションで次の手順を完了します。

    1. **[識別子]** ボックスに、`urn:auth0:helpscout:<instancename>` の形式で URL を入力します。

    2. **[応答 URL]** ボックスに、`https://helpscout.auth0.com/login/callback?connection=<instancename>` の形式で URL を入力します。

    ![[Help Scout のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url.png)

4. SP 開始モードでアプリケーションを設定する場合は、**[詳細な URL 設定の表示]** チェック ボックスをオンにして、次の手順を実行します。

    * **[サインオン URL]** ボックスに、`https://secure.helpscout.net/members/login/` の形式で URL を入力します。

    ![[Help Scout のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_url1.png)
 
    > [!NOTE] 
    > これらの URL の値は、単なる例です。 実際の識別子と応答 URL でこれらの値を更新します。 これらの値を取得するには、[Help Scout サポート チーム](mailto:help@helpscout.com)に問い合わせてください。 

5. **[SAML 署名証明書]** で、**[メタデータ XML]** を選択し、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_certificate.png) 

6. [ **保存**] を選択します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-helpscout-tutorial/tutorial_general_400.png)
    
7. Help Scout 側にシングル サインオンを設定するには、ダウンロードしたメタデータ XML ファイルを [Help Scout サポート チーム](mailto:help@helpscout.com)に送信します。 Help Scout サポート チームは、SAML シングル サインオンの接続が双方で正しく設定されるように、この設定を適用します。

> [!TIP]
> アプリの設定中に、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できます。 **[Active Directory]** > **[エンタープライズ アプリケーション]** を選択してこのアプリを追加した後、**[シングル サインオン]** タブを選択します。ページ下部の **[構成]** セクションで、組み込みのドキュメントにアクセスできます。 詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure Portal で Britta Simon というテスト ユーザーを作成します。

![Azure AD のテスト ユーザーの作成][100]

Azure AD でテスト ユーザーを作成するには、次の手順に従います。

1. Azure Portal の左側のメニューで、**[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](./media/active-directory-saas-helpscout-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** を選択し、**[すべてのユーザー]** を選択します。

    ![[ユーザーとグループ]、[すべてのユーザー] の順に選択](./media/active-directory-saas-helpscout-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ページの上部にある **[追加]** をクリックします。

    ![[追加] ボタン](./media/active-directory-saas-helpscout-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順を完了します。

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    2. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    3. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    4. **[作成]**を選択します。

        ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-helpscout-tutorial/create_aaduser_04.png)

 
### <a name="create-a-help-scout-test-user"></a>Help Scout テスト ユーザーの作成

このセクションの目的は、Help Scout で Britta Simon というユーザーを作成することです。 Help Scout では、Just-In-Time (JIT) プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、タスクやアクションを行う必要はありません。 ユーザーがまだ Help Scout に存在しない場合は、Help Scout にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Help Scout へのユーザー アカウント アクセスを許可することで、このユーザーが Azure AD シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

Help Scout に Britta Simon を割り当てるには、次の手順に従います。

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。 **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Help Scout]** を選択します。

    ![アプリケーションの一覧の Help Scout のリンク](./media/active-directory-saas-helpscout-tutorial/tutorial_helpscout_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]**を選択します。 **[割り当ての追加]** ページで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ページで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ページで **[選択]** を選択します。

7. **[割り当ての追加]** ページで **[割り当て]** を選択します。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Help Scout] タイルを選択すると、自動的に Help Scout アプリケーションにサインインします。

アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-helpscout-tutorial/tutorial_general_203.png


