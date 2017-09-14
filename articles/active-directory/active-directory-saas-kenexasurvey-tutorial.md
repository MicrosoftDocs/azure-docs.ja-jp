---
title: "チュートリアル: Azure Active Directory と IBM Kenexa Survey Enterprise の統合| Microsoft Docs"
description: "Azure Active Directory と IBM Kenexa Survey Enterprise の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/30/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5c276c23288292a1c54dd9d57177d5072b90c9e3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>チュートリアル: Azure Active Directory と IBM Kenexa Survey Enterprise の統合

このチュートリアルでは、IBM Kenexa Survey Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。

IBM Kenexa Survey Enterprise と Azure AD の統合には、次の利点があります。

- IBM Kenexa Survey Enterprise にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで、シングル サインオン (SSO) を使用して自動的に IBM Kenexa Survey Enterprise にサインインできるようにできます。
- 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

IBM Kenexa Survey Enterprise と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- IBM Kenexa Survey Enterprise での SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD SSO をテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

* ギャラリーからの IBM Kenexa Survey Enterprise の追加
* Azure AD SSO の構成とテスト

## <a name="add-ibm-kenexa-survey-enterprise-from-the-gallery"></a>ギャラリーからの IBM Kenexa Survey Enterprise の追加
Azure AD への IBM Kenexa Survey Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に IBM Kenexa Survey Enterprise を追加します。

ギャラリーから IBM Kenexa Survey Enterprise を追加するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. アプリケーションを追加するには、**[新しいアプリケーション]**ボタンをクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに、「 **IBM Kenexa Survey Enterprise**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_search.png)

5. 結果の一覧で、**[IBM Kenexa Survey Enterprise]** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の IBM Kenexa Survey Enterprise](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、IBM Kenexa Survey Enterprise で Azure AD の SSO を構成し、テストします。

SSO を機能させるには、IBM Kenexa Survey Enterprise ユーザーに対応する Azure ユーザーを Azure AD が特定する必要があります。 言い換えると、Azure AD は、Azure AD ユーザーと IBM Kenexa Survey Enterprise の関連ユーザーの間のリンク関係を確立する必要があります。

リンク関係を確立するには、IBM Kenexa Survey Enterprise の **[ユーザー名]** の値を Azure AD の **[Username]** の値として割り当てます。

IBM Kenexa Survey Enterprise で Azure AD の SSO を構成してテストするには、次の 2 つのセクションの構成要素を完了します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、次の手順で Azure Portal で Azure AD SSO を有効にし、IBM Kenexa Survey Enterprise application アプリケーションに SSO を構成します。

1. Azure Portal の **IBM Kenexa Survey Enterprise** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![IBM Kenexa Survey Enterprise のシングル サインオンの構成リンク][4]

2. **[シングル サインオン]** ダイアログ ボックスの **[モード]** ボックスで **[SAML ベースのサインオン]** を選択し、SSO を有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_samlbase.png)

3. **[IBM Kenexa Survey Enterprise Domain and URLs] \(IBM Kenexa Survey Enterprise のドメインと URL)** セクションで、次の手順を実行します。

    ![[IBM Kenexa Survey Enterprise のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_url.png)

    a. **[識別子]** ボックスに、URL を次のパターンで入力します。`https://surveys.kenexa.com/<companycode>`

    b. **[応答 URL]** ボックスに、`https://surveys.kenexa.com/<companycode>/tools/sso.asp` の形式で URL を入力します。

    > [!NOTE] 
    > 上記の値は、実際の値ではありません。 実際の識別子と応答 URL でこれらの値を更新します。 実際の値を取得するには、 [IBM Kenexa Survey Enterprise サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)に問い合わせてください。

4. **[SAML 署名証明書]** で、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書 (Base64) ダウンロード リンク](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_certificate.png) 

    IBM Kenexa Survey Enterprise アプリケーションでは、特定の形式の Security Assertions Markup Language (SAML) アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 応答内のユーザー識別子要求の値は、Kenexa システムで構成された SSO ID に一致する必要があります。 組織内の適切なユーザー ID を SSO Internet Datagram Protocol (IDP) としてマッピングするには、[IBM Kenexa Survey Enterprise サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)と連携してください。 

    既定では、Azure AD はユーザーの識別子を、ユーザー プリンシパル名 (UPN) の値として設定します。 この値は、以下のスクリーン ショットに示すように **[属性]** タブから変更できます。 マッピングを正しく完了した後にのみ統合は機能します。
    
    ![[ユーザー属性] ダイアログ ボックス](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_attribute.png)   

5. [ **Save**] をクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_400.png)

6. **[サインオンの構成]** ウィンドウを開くには、**[IBM Kenexa Survey Enterprise Configuration] \(IBM Kenexa Survey Enterprise の構成)** で **[Configure IBM Kenexa Survey Enterprise] \(IBM Kenexa Survey Enterprise を構成する)** をクリックします。 
 
    ![IBM Kenexa Survey Enterprise の構成リンク](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_configure.png)

7. **サインアウト URL**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL** の値を **[クイック リファレンス]** セクションからコピーします。

8. **[サインオンの構成]** ウィンドウの**[クイック リファレンス]** で、 **サインアウト URL**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL** の値をコピーします。

9. **IBM Kenexa Survey Enterprise** 側に SSO を構成するには、ダウンロードした**証明書 (Base64)**、**サインアウト URL**、**SAML エンティティ ID**、**SAML シングル サインオン サービス URL** の値を [IBM Kenexa Survey Enterprise サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)に送信します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認することができます。 **[Active Directory]** > **[エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント](https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、Azure Portal で次の手順に従って Britta Simon というテスト ユーザーを作成します。

![Azure AD のテスト ユーザーの作成][100]

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_02.png) 

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。
 
    ![[追加] ボタン](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-kenexasurvey-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="create-an-ibm-kenexa-survey-enterprise-test-user"></a>IBM Kenexa Survey Enterprise テスト ユーザーの作成

このセクションでは、IBM Kenexa Survey Enterprise で Britta Simon というユーザーを作成します。 

IBM Kenexa Survey Enterprise システムでユーザーを作成し、それに SSO ID をマッピングするには、[IBM Kenexa Survey Enterprise サポート チーム](https://www.ibm.com/support/home/?lnk=fcw)と連携してください。 また、この SSO ID 値を Azure AD のユーザー ID の値にマップする必要があります。 **[属性]** タブでこの既定の設定を変更できます。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ユーザーの Britta Simon に IBM Kenexa Survey Enterprise へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようします。

![ユーザー ロールを割り当てる][200] 

ユーザーの Britta Simon を IBM Kenexa Survey Enterprise に割り当てるには、次の手順に従います。

1. Azure Portal で**アプリケーション** ビューを開き、**ディレクトリ** ビューに移動します。次に、**[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]** をクリックします。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] のリンク][201] 

2. **アプリケーション**の一覧で、**[IBM Kenexa Survey Enterprise]**を選択します。

    ![アプリケーションの一覧の IBM Kenexa Survey Enterprise リンク](./media/active-directory-saas-kenexasurvey-tutorial/tutorial_kenexasurvey_app.png) 

3. 左側のウィンドウで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** ボタンをクリックし、**[割り当ての追加]** ウィンドウで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** 一覧から、**[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログ ボックスで、**[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで、**[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネルで **[IBM Kenexa Survey Enterprise]** タイルをクリックすると、IBM Kenexa Survey Enterprise アプリケーションに自動的にサインインします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-kenexasurvey-tutorial/tutorial_general_203.png

 

