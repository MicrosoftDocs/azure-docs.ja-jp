---
title: チュートリアル:Azure Active Directory と Kanbanize の統合 | Microsoft Docs
description: Azure Active Directory と Kanbanize の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22c136225e5a8526afd482e5ef8400198947422f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199682"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>チュートリアル:Azure Active Directory と Kanbanize の統合

このチュートリアルでは、Kanbanize と Azure Active Directory (Azure AD) を統合する方法について説明します。

Kanbanize と Azure AD の統合には、次の利点があります。

- Kanbanize にアクセスできる Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Kanbanize にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Kanbanize と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオンが有効な Kanbanize のサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Kanbanize の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-kanbanize-from-the-gallery"></a>ギャラリーからの Kanbanize の追加
Azure AD への Kanbanize の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Kanbanize を追加する必要があります。

**ギャラリーから Kanbanize を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**Kanbanize**」と入力し、結果パネルで **Kanbanize** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の Kanbanize](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Kanbanize で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Kanbanize ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Kanbanize の関連ユーザーの間で、リンク関係が確立されている必要があります。

Kanbanize で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Kanbanizeテスト ユーザーの作成](#create-a-kanbanize-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Kanbanize で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にして、Kanbanize アプリケーションでシングル サインオンを構成します。

**Kanbanize で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure portal の **Kanbanize** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. アプリケーションを **IDP** 開始モードで構成する場合は、**[Kanbanize のドメインと URL]** セクションで次の手順を実行します。

    ![[Kanbanize のドメインと URL] のシングル サインオン情報](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. **[識別子]** ボックスに、`https://<subdomain>.kanbanize.com/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.kanbanize.com/saml/acs` のパターンを使用して URL を入力します。

    c. **[詳細な URL 設定の表示]** をクリックします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。  **[リレー状態]** ボックスに、URL `/ctrl_login/saml_login` を入力します。

    e. **[SP]** 開始モードでアプリケーションを構成する場合は、**[サインオン URL]** テキストボックスに `https://<subdomain>.kanbanize.com` のパターンで URL を入力します。
     
    > [!NOTE] 
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[Kanbanize クライアント サポート チーム](mailto:support@ms.kanbanize.com)に問い合わせてください。 

5. Kanbanize アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、Kanbanize はこれがユーザーの電子メール アドレスにマップされることを想定します。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。
    
    ![Configure single sign-on](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. **[Kanbanize 構成]** セクションで、**[Kanbanize の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Kanbanize の構成](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. 別の Web ブラウザー ウィンドウで、セキュリティ管理者として Kanbanize にログインします。 

10. ページの右上の**設定**ロゴをクリックします。

    ![Kanbanize の設定](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. [Administration]\(管理\) パネル ページのメニューの左側にある **[Integration]\(統合\)** をクリックし、**[Single Sign-On]\(シングル サインオン\)** を有効にします。 

    ![Kanbanize の統合](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. [Integration]\(統合\) セクションで、**[CONFIGURE]\(構成\)** をっクリックして **[Single Sign-On Integration]\(シングル サインオンの統合\)** ページを開きます。

    ![Kanbanize の構成](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. **[Single Sign-On Configuration]\(シングル サインオンの統合\)** ページの **[Configurations]\(構成\)** で、次の手順を実行します。

    ![Kanbanize の統合](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. **[Idp Entity Id]\(Idp エンティティ ID\)** テキストボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    b. **[Idp Login Endpoint]\(IdP ログイン エンドポイント\)** テキストボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. **[Idp Login Endpoint]\(IdP ログイン エンドポイント\)** テキストボックスに、Azure Portal からコピーした **サインアウト URL** の値を貼り付けます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Attribute name for Email]\(電子メールの属性名\)** テキストボックスで、この値 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を入力します。

    e. **[Attribute name for First Name]\(名の属性名\)** テキストボックスで、この値 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` を入力します。

    f. **[Attribute name for Last Name]\(姓の属性名\)** テキストボックスで、この値 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` を入力します。 
    > [!Note]
    > これらの値は、Azure Portal の [ユーザー属性] セクションから対応する名前空間と名前の値を結合することで取得できます。

    g. Azure portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容を (開始マーカーとエンド マーカーを除いて) コピーして  **[Idp X.509 Certificate]\(IdP X.509 証明書\)** ボックスに貼り付けます。

    h. **[Enable login with both SSO and Kanbanize]\(SSO と Kanbanize の両方でログインを有効にする\)** をオンにします。
    
    i. **[設定の保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/kanbanize-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/kanbanize-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/kanbanize-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-a-kanbanize-test-user"></a>Kanbanize テスト ユーザーの作成

このセクションの目的は、Kanbanize で Britta Simon というユーザーを作成することです。 Kanbanize では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 存在しない Kanbanize ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、 [Kanbanize クライアント サポート チーム](mailto:support@ms.kanbanize.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Kanbanize へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようします。

![ユーザー ロールを割り当てる][200] 

**Kanbanize に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Kanbanize]** を選択します。

    ![アプリケーションの一覧の Kanbanize のリンク](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Kanbanize] タイルをクリックすると、自動的に Kanbanize アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

