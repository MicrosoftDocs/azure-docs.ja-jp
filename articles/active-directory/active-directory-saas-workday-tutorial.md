---
title: "チュートリアル: Azure Active Directory と Workday の統合 | Microsoft Docs"
description: "Azure Active Directory と Workday の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 164d5c644f120fa86e2b690649241892764b64b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>チュートリアル: Azure Active Directory と Workday の統合

このチュートリアルでは、Workday と Azure Active Directory (Azure AD) を統合する方法について説明します。

Workday と Azure AD の統合には、次の利点があります。

- Workday にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Workday にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Workday と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Workday でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Workday の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-workday-from-the-gallery"></a>ギャラリーからの Workday の追加
Azure AD への Workday の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workday を追加する必要があります。

**ギャラリーから Workday を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「 **Workday**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. 結果ウィンドウで **Workday** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Workday で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Workday ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Workday の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Workday の **[Username]\(ユーザー名\)** の値として割り当てます。

Workday で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Workday テスト ユーザーの作成](#creating-a-workday-test-user)** - Workday で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Workday アプリケーションでシングル サインオンを構成します。

**Workday で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Workday** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. **[Workday のドメインと URL]** セクションで、次の手順に従います。

    ![[シングル サインオンの構成]](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. **[サインオン URL]** テキストボックスに、「`https://impl.workday.com/<tenant>/login-saml2.htmld`」と入力します。

    b. **[応答 URL]** ボックスに、`https://impl.workday.com/<tenant>/login-saml.htmld` のパターンを使用して URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 応答 URL には必ずサブドメインを入れます (例: www、wd2、wd3、wd3-impl、wd5、wd5-impl)。 " *http://www.myworkday.com* " のようなものは動作しますが、" *http://myworkday.com* " は動作しません。 これらの値を取得するには、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせてください。 
 

4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. **[Workday 構成]** セクションで、**[Workday の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![シングル サインオンを構成する](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. 別の Web ブラウザー ウィンドウで、Workday 企業サイトに管理者としてログインします。

8. **[メニュー]\>[ワークベンチ]** に移動します。
   
    ![ワークベンチ](./media/active-directory-saas-workday-tutorial/IC782923.png "Workbench")

9. **[アカウント管理]**に移動します。
   
    ![Account Administration](./media/active-directory-saas-workday-tutorial/IC782924.png "Account Administration")

10. **[テナントのセットアップの編集 – セキュリティ]**に移動します。
   
    ![テナントのセキュリティの編集](./media/active-directory-saas-workday-tutorial/IC782925.png "Edit Tenant Security")

11. **[リダイレクト URL]** セクションで、次の手順を実行します。
   
    ![リダイレクト URL](./media/active-directory-saas-workday-tutorial/IC7829581.png "Redirection URLs")
   
    a. **[行の追加]** をクリックします。
   
    b. **[Login Redirect URL]\(ログイン リダイレクト URL\)** ボックスと **[Mobile Redirect URL]\(モバイル リダイレクト URL\)** ボックスに、Azure Portal の **[Workday のドメインと URL]** セクションで入力した**サインオン URL** を入力します。
   
    c. Azure Portal の **[サインオンの構成]** ウィンドウで **[サインアウト URL]** をコピーし、**[Logout Redirect URL]\(ログアウト リダイレクト URL\)** ボックスに貼り付けます。
   
    d.  **[環境]** テキスト ボックスに、環境の名前を入力します。  

    >[!NOTE]
    > [環境] 属性の値が、テナント URL の値に関連付けられます。  
    >- Workday テナント URL のドメイン名が impl で始まる場合 (例: *https://impl.workday.com/\<テナント\>/login-saml2.htmld*)、**[Environment]\(環境\)** 属性を [Implementation]\(実装\) に設定する必要があります。  
    >- ドメイン名が impl 以外で始まる場合は、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に問い合わせて、対応する **[Environment]\(環境\)** の値を取得してください。

12. **[SAML 設定]** セクションで、次の手順を実行します。
   
    ![SAML のセットアップ](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML Setup")
   
    a.  **[Enable SAML Authentication]**を選択します。
   
    b.  **[行の追加]** をクリックします。

13. [SAML ID プロバイダー] セクションで、次の手順に従います。
   
    ![SAML ID プロバイダー](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML Identity Providers")
   
    a. [Identity Provider Name]\(ID プロバイダー名\) テキスト ボックスに、プロバイダー名を入力します (例: *SPInitiatedSSO*)。
   
    b. Azure Portal の **[サインオンの構成]** ウィンドウで **[SAML エンティティ ID]** の値をコピーし、**[Issuer]\(発行者\)** ボックスに貼り付けます。
   
    c. **[Enable Workday Initiated Logout]\(Workday 始動ログアウトを有効にする\)** をオンにします。
   
    d. Azure Portal の **[サインオンの構成]** ウィンドウで **[サインアウト URL]** の値をコピーし、**[Logout Request URL]\(ログアウト要求 URL\)** ボックスに貼り付けます。

    e. **[ID プロバイダーの公開鍵証明書]** をクリックし、**[作成]** をクリックします。 

    ![作成](./media/active-directory-saas-workday-tutorial/IC782928.png "作成")

    f. **[x509 公開鍵の作成]** をクリックします。 

    ![作成](./media/active-directory-saas-workday-tutorial/IC782929.png "作成")


14. **[x509 公開鍵の表示]** セクションで、次の手順を実行します。 
   
    ![x509 公開鍵の表示](./media/active-directory-saas-workday-tutorial/IC782930.png "x509 公開鍵の表示") 
   
    a. **[Name]\(名前\)** テキスト ボックスに、証明書の名前を入力します (例: *PPE\_SP*)。
   
    b. **[有効期間の開始日]** テキスト ボックスに、証明書の有効期間の開始日を示す属性の値を入力します。
   
    c.  **[有効期間の終了日]** テキスト ボックスに、証明書の有効期間の終了日を示す属性の値を入力します。
   
    > [!NOTE]
    > 有効期間の開始日と終了日は、ダウンロードした証明書をダブルクリックして確認できます。  日付は **[詳細]** タブに表示されます。
    > 
    >
   
    d.  Base 64 でエンコードされた証明書をメモ帳で開き、その内容をコピーします。
   
    e.  **[証明書]** テキスト ボックスに、クリップボードの内容を貼り付けます。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。  **[OK]**をクリックします。

15. 次の手順に従います。 
   
    ![SSO 構成](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO configuration")
   
    a.  **[x509 秘密鍵のペア]** を有効にします。
   
    b.  **[サービス プロバイダー ID]** テキスト ボックスに「**http://www.workday.com**」と入力します。
   
    c.  **[SP によって開始された SAML 認証を有効にする]** を選択します。
   
    d.  Azure Portal の **[サインオンの構成]** ウィンドウで **[SAML シングル サインオン サービスの URL]** の値をコピーし、**[IdP SSO Service URL]\(IdP SSO サービス URL\)** ボックスに貼り付けます。
   
    e. **[SP によって開始された認証要求を圧縮しない]** を選択します。
   
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[認証要求署名方法]** として **[SHA256]** を選択します。 
   
    ![認証要求署名方法](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "Authentication Request Signature Method") 
   
    g. **[OK]**をクリックします。 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")
<CE>

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
>

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-workday-test-user"></a>Workday テスト ユーザーの作成

Workday にテスト ユーザーをプロビジョニングするには、[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)に連絡する必要があります。
[Workday クライアント サポート チーム](https://www.workday.com/en-us/partners-services/services/support.html)にユーザーを作成してもらいます。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Workday へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を Workday に割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Workday]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

シングル サインオンの設定をテストする場合は、アクセス パネルを開きます。 アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)
* [[ユーザー プロビジョニングの構成]](active-directory-saas-workday-inbound-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png

