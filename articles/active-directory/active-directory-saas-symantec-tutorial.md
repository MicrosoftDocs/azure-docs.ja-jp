---
title: "チュートリアル: Azure Active Directory と Symantec Web Security Service (WSS) の統合 | Microsoft Docs"
description: "Azure Active Directory と Symantec Web Security Service (WSS) の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 61576d3a915d209e7355e04432e586dcf66e7c5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>チュートリアル: Azure Active Directory と Symantec Web Security Service (WSS) の統合

このチュートリアルでは、Symantec Web Security Service (WSS) アカウントを Azure Active Directory (Azure AD) アカウントに統合して、WSS で Azure AD にプロビジョニングされたエンド ユーザーを SAML 認証を使用して認証し、ユーザー レベルまたはグループ レベルのポリシー ルールを適用できるようにする方法について説明します。

Symantec Web Security Service (WSS) と Azure AD の統合には、次の利点があります。

- WSS アカウントによって使用されるすべてのエンド ユーザーとグループを Azure AD ポータルから管理できます。 

- エンド ユーザーは、Azure AD 資格情報を使用して WSS で自己認証を行うことができます。

- WSS アカウントに定義されたユーザー レベルおよびグループ レベルのポリシー ルールを適用できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Symantec Web Security Service (WSS) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Symantec Web Security Service (WSS) アカウント

> [!NOTE]
> このチュートリアルの手順をテストする場合、現在運用環境で使用されている WSS アカウントを使用することはお勧めできません。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、このテストでは、現在運用環境で使用されている WSS アカウントを使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、Azure AD アカウントで定義されたエンド ユーザー資格情報を使用して WSS にシングル サインオンできるように Azure AD を構成します。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Symantec Web Security Service (WSS) アプリの追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>ギャラリーからの Symantec Web Security Service (WSS) (WSS) の追加
Azure AD への Symantec Web Security Service (WSS) の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Symantec Web Security Service (WSS) を追加する必要があります。

**ギャラリーから Symantec Web Security Service (WSS) を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Symantec Web Security Service (WSS)**」と入力し、結果ウィンドウで「**Symantec Web Security Service (WSS)**」を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Symantec Web Security Service (WSS)](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Symantec Web Security Service (WSS) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Symantec Web Security Service (WSS) ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Symantec Web Security Service (WSS) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Symantec Web Security Service (WSS) で、Azure AD の **[ユーザー名]** の値を **[ユーザー名]** の値に割り当てて、リンク関係を確立します。

Symantec Web Security Service (WSS) での Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Symantec Web Security Service (WSS) テスト ユーザーの作成](#create-a-symantec-web-security-service-wss-test-user)** - Symantec Web Security Service (WSS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Symantec Web Security Service (WSS) アプリケーションへのシングル サインオンを構成します。

**Symantec Web Security Service (WSS) で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Symantec Web Security Service (WSS)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

3. **[Symantec Web Security Service (WSS) Domain and URLs]\(Symantec Web Security Service (WSS) のドメインと URL\)** セクションで、次の手順を実行します。

    ![Symantec Web Security Service (WSS) ドメインおよび URL シングル サインオン情報](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. **[識別子]** ボックスに次の URL を入力します。`https://saml.threatpulse.net:8443/saml/saml_realm`

    b. **[応答 URL]** ボックスに、URL として「`https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`」と入力します。

    > [!NOTE]
    > **ID** と**応答 URL** の値が何らかの理由で有効でない場合には、[Symantec Web Security Service (WSS) クライアント サポート チーム](https://www.symantec.com/contact-us)に問い合わせてください。

4. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-symantec-tutorial/tutorial_general_400.png)
    
6. Symantec Web Security Service (WSS) 側にシングル サインオンを構成するには、WSS のオンライン ドキュメントを参照してください。 ダウンロードした**メタデータ XML** ファイルは、WSS ポータルにインポートする必要があります。 WSS ポータルの構成でサポートが必要な場合には、[Symantec Web Security Service (WSS) サポート チーム](https://www.symantec.com/contact-us)に問い合わせてください。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-symantec-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-symantec-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-symantec-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-symantec-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>Symantec Web Security Service (WSS) テスト ユーザーの作成

このセクションでは、Symantec Web Security Service (WSS) で Britta Simon というユーザーを作成します。 対応するエンド ユーザー名を WSS ポータルで手動で作成するか、または、Azure AD にプロビジョニングされたユーザーまたはグループが数分後 (最大 15 分) に WSS ポータルに同期されるのを待ちます。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 また、Web サイトを参照するために使用する、エンド ユーザー マシンのパブリック IP アドレスを Symantec Web Security Service (WSS) ポータルにプロビジョニングする必要もあります。

> [!NOTE]
> [ここをクリック](http://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1)して、マシンのパブリック IP アドレスを取得してください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Symantec Web Security Service (WSS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**Symantec Web Security Service (WSS) に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[Symantec Web Security Service (WSS)]** を選択します。

    ![アプリケーションの一覧の Symantec Web Security Service (WSS) リンク](./media/active-directory-saas-symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

これまでに、WSS アカウントで Azure AD の SAML 認証が使用されるように構成しましたので、このセクションでは、そのシングル サインオン機能をテストします。

WSS にトラフィックをプロキシするように Web ブラウザーを構成したら、ブラウザーを開いてサイトを参照しようとすると、Azure サインオン ページにリダイレクトされます。 Azure AD にプロビジョニングされたテスト エンド ユーザー (つまり、BrittaSimon) の資格情報と、関連するパスワードを入力します。 認証されると、選択した Web サイトを参照できるようになります。 BrittaSimon が特定のサイトを参照できないようにするポリシー ルールを WSS 側に作成した場合、ユーザー BrittaSimon としてそのサイトを参照しようとすると、WSS ブロック ページが表示されます。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-symantec-tutorial/tutorial_general_203.png

