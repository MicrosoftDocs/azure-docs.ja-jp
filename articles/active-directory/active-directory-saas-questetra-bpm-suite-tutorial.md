---
title: "チュートリアル: Azure Active Directory と Questetra BPM Suite の統合 | Microsoft Docs"
description: "Azure Active Directory と Questetra BPM Suite の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2017
ms.author: jeedes
ms.openlocfilehash: 89d1ebc090a7b667885f5dcbab14312efd767f0b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>チュートリアル: Azure Active Directory と Questetra BPM Suite の統合

このチュートリアルでは、Questetra BPM Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。

Questetra BPM Suite と Azure AD の統合には、次の利点があります。

- Questetra BPM Suite にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Questetra BPM Suite にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Questetra BPM Suite と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Questetra BPM Suite でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Questetra BPM Suite の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>ギャラリーからの Questetra BPM Suite の追加
Azure AD への Questetra BPM Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Questetra BPM Suite を追加する必要があります。

**ギャラリーから Questetra BPM Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Questetra BPM Suite**」と入力し、結果パネルで **[Questetra BPM Suite]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![ギャラリーから追加する](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Questetra BPM Suite で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Questetra BPM Suite ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Questetra BPM Suite の関連ユーザーの間で、リンク関係が確立されている必要があります。

Questetra BPM Suite で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

Questetra BPM Suite で Azure AD のシングル サインオンを構成し、テストするには、次の要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Questetra BPM Suite のテスト ユーザーの作成](#create-a-questetra-bpm-suite-test-user)** - Questetra BPM Suite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Questetra BPM Suite アプリケーションでシングル サインオンを構成します。

**Questetra BPM Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Questetra BPM Suite** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![SAML ベースのサインオン](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_samlbase.png)

3. **[Questetra BPM Suite のドメインと URL]** セクションで、次の手順に従います。

    ![[Questetra BPM Suite のドメインと URL] セクション](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.questetra.net/saml/SSO/alias/bpm` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.questetra.net/` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値は、**Questetra BPM Suite** 企業サイトの **[SP Information]\(SP 情報\)** セクションから取得できます。これについては、チュートリアルの後半で説明します。または、[Questetra BPM Suite クライアント サポート チーム](https://www.questetra.com/contact/)にお問い合わせください。 
 
4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[SAML 署名証明書] セクション](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[保存] ボタン](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_400.png)

6. **[Questetra BPM Suite 構成]** セクションで、**[Adaptive Suite の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![[Questetra BPM Suite 構成] セクション](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_configure.png) 

7. 別の Web ブラウザーのウィンドウで、管理者として **Questetra BPM Suite** 企業サイトにログインします。

8. 上部のメニューで **[システム設定]**をクリックします。 
   
    ![Azure AD のシングル サインオン][10]

9. **[シングルサインオン (SAML)]** ページを開くには、**[SSO (SAML)]** をクリックします。 
   
    ![Azure AD のシングル サインオン][11]

10. **Questetra BPM Suite** 企業サイトの **[SP Information]\(SP 情報\)** セクションで、次の手順に従います。

    a. **[ACS URL]** をコピーし、Azure Portal の **[Questetra BPM Suite のドメインと URL]** セクションにある **[サインオン URL]** ボックスに貼り付けます。
    
    b. **[Entity ID]\(エンティティ ID\)** をコピーし、Azure Portal の **[Questetra BPM Suite のドメインと URL]** セクションにある **[識別子]** ボックスに貼り付けます。

11. **Questetra BPM Suite** 企業サイトで、次の手順に従います。 
   
    ![[シングル サインオンの構成]][15]
   
    a.[サインオン URL] ボックスに、ユーザーが Tidemark アプリケーションへのサインオンに使用する URL を入力します。 **[シングル サインオンを有効にする]**を選択します。
   
    b. **[Entity ID]\(エンティティ ID\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
    
    c. **[Sign-in page URL]\(サインイン ページ URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
    
    d. **[Sign-out page URL]\(サインアウト ページ URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
    
    e. **[NameID format]\(NameID の形式\)** ボックスに、「`urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`」と入力します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 Azure Portal からダウンロードした **Base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[Validation certificate]\(検証証明書\)** ボックスに貼り付けます。 

    g. [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-questetra-bpm-suite-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="create-a-questetra-bpm-suite-test-user"></a>Questetra BPM Suite テスト ユーザーの作成

このセクションの目的は、Questetra BPM Suite で Britta Simon というユーザーを作成することです。

**Questetra BPM Suite で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. Questetra BPM Suite 企業サイトに管理者としてサインオンします。
2. **[システム設定]、[ユーザー一覧]、[新規ユーザー]** の順に移動します。 
3. [新規ユーザー] ダイアログで、次の手順を実行します。 
   
    ![テスト ユーザーの作成][300] 
   
    a. **[Name]\(名前\)** ボックスに、ユーザーの**名前**を「**britta.simon@contoso.com**」と入力します。
   
    b. **[Email]\(電子メール\)** ボックスに、ユーザーの**電子メール**を「**britta.simon@contoso.com**」と入力します。
   
    c. **[Password]\(パスワード\)** ボックスに、そのユーザーの**パスワード**を入力します。
    
    d. **[新しいユーザーの追加]**をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Questetra BPM Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Questetra BPM Suite に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Questetra BPM Suite]**を選択します。

    ![アプリの一覧の Questetra BPM Suite](./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_questetra-bpm-suite_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Questetra BPM Suite のタイルをクリックすると、自動的に Questetra BPM Suite アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_08.png

[100]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/tutorial_general_203.png
[300]: ./media/active-directory-saas-questetra-bpm-suite-tutorial/questera_bpm_suite_11.png 

