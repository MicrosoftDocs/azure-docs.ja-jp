---
title: "チュートリアル: Azure Active Directory と Salesforce の統合 | Microsoft Docs"
description: "Azure Active Directory と Salesforce の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: 639e40ca7e406a1726033e9f5c5363c289087589
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>チュートリアル: Azure Active Directory と Salesforce の統合

このチュートリアルでは、Salesforce と Azure Active Directory (Azure AD) を統合する方法について説明します。

Salesforce と Azure AD の統合には、次の利点があります。

- Salesforce にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Salesforce にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と Salesforce の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Salesforce でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Salesforce の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-salesforce-from-the-gallery"></a>ギャラリーからの Salesforce の追加
Azure AD への Salesforce の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Salesforce を追加する必要があります。

**ギャラリーから Salesforce を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Salesforce**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_search.png)

5. 結果ウィンドウで **[Salesforce]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Salesforce で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Salesforce ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Salesforce の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、Salesforce の **[ユーザー名]** の値として割り当てることで確立されます。

Salesforce で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Salesforce テスト ユーザーの作成](#creating-a-salesforce-test-user)** - Salesforce で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Salesforce アプリケーションでシングル サインオンを構成します。

**Salesforce で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Salesforce** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. **[Salesforce のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_url.png)

    **[サインオン URL]** ボックスに、次のパターンを使用して値を入力します。 
   * エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`
   * 開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`

    > [!NOTE] 
    > これらは実際の値ではありません。 これらの値は実際のサインオン URL に変更します。 これらの値を取得するには、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/tutorial_general_400.png)

6. **[Salesforce 構成]** セクションで、**[Salesforce を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。 

    ![シングル サインオンの構成](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_configure.png) 
<CS>
7.  ブラウザーで新しいタブを開き、Salesforce の管理者アカウントにログインします。

8.  **[管理者]** ナビゲーション ウィンドウで **[セキュリティ コントロール]** をクリックし、関連するセクションを展開します。 次に、**[シングル サインオンの設定]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png)

9.  **[シングル サインオンの設定]** ページで、**[編集]** ボタンをクリックします。
    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png)

      > [!NOTE]
      > Salesforce アカウント用のシングル サインオン設定を有効にできない場合は、必要に応じて、[Salesforce クライアント サポート チーム](https://help.salesforce.com/support)にお問い合わせください。 

10. **[SAML 有効]** を選択し、**[保存]** をクリックします。

      ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png)
11. SAML のシングル サインオン設定を構成するには、 **[新規]**をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png)

12. **[SAML シングル サインオン設定の編集]** ページで、次の構成を行います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png)

    a. **[名前]** フィールドに、この構成の表示名を入力します。 **[名前]** の値を指定すると、**[API 名]** ボックスに値が自動的に入力されます。

    b. Salesforce の **[発行者]** フィールドに **[SMAL エンティティ ID]** 値を貼り付けます。

    c. **[エンティティ ID]**ボックスに、次のパターンを使用して Salesforce のドメイン名を入力します。
      
      * エンタープライズ アカウント: `https://<subdomain>.my.salesforce.com`
      * 開発者アカウント: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. **[参照]** または **[ファイルの選択]** をクリックして **[アップロードするファイルの選択]** ダイアログを開きます。Salesforce の証明書を選択し、**[開く]** をクリックして証明書をアップロードします。

    e. **[SAML ID の種類]** で、**[アサーションに salesforce.com ユーザーの名前を含める]** を選択します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[SAML ID の場所]** で、**[ID を Subject ステートメントの NameIdentifier 要素にする]** チェックボックスをオンにします。

    g. Salesforce の **[Identity provider login URL]\(ID プロバイダー ログイン URL\)** フィールドに **[シングル サインオン サービス URL]** を貼り付けます。
    
    h. **[サービス プロバイダーが開始した要求のバインド]** で **[HTTP リダイレクト]** を選択します。
    
    i. 最後に **[保存]** をクリックし、SAML シングル サインオンの設定を適用します。

13. Salesforce の左側のナビゲーション ウィンドウで、**[ドメインの管理]** をクリックして関連するセクションを展開し、**[マイ ドメイン]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png)

14. **[認証の構成]** セクションまで下へスクロールし、**[編集]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png)

15. **[認証サービス]** セクションで SAML SSO 構成の表示名を選択し、**[保存]** をクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > 複数の認証サービスを選択した場合、ユーザーが Salesforce 環境へのシングル サインオンを開始すると、サインインに使用する認証サービスを選択するよう要求されます。 このメッセージが表示されないようにするには、**その他すべての認証サービスをオフのままに**しておいてください。
<CE>    
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforce-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforce-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforce-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-salesforce-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. ページの下部にある **[Create]**」を参照してください。
 
### <a name="creating-a-salesforce-test-user"></a>Salesforce テスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを Salesforce に作成します。 Salesforce では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。
このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Salesforce に存在しない場合は、Salesforce にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Salesforce へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Salesforce に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Salesforce]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-salesforce-tutorial/tutorial_salesforce_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

シングル サインオンの設定をテストするには、アクセス パネル ([https://myapps.microsoft.com](https://myapps.microsoft.com/)) を開き、テスト アカウントにサインインして **[Salesforce]** をクリックします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [ユーザー プロビジョニングの構成](active-directory-saas-salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-salesforce-tutorial/tutorial_general_203.png


