---
title: "チュートリアル: Azure Active Directory と Slack の統合 | Microsoft Docs"
description: "Azure Active Directory とSlack の間にシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 5aca630b2077d3f7d4ce9273ee668ed6a5f9843d
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-slack"></a>チュートリアル: Azure Active Directory と Slack の統合

このチュートリアルでは、Slack と Azure Active Directory (Azure AD) を統合する方法について説明します。

Slack と Azure AD の統合には、次の利点があります。

- Slack にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Slack にサインオンする機能 (シングル サインオン) を有効にできます。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Slack と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Slack でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Slack の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-slack-from-the-gallery"></a>ギャラリーからの Slack の追加
Azure AD への Slack の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Slack を追加する必要があります。

**ギャラリーから Slack を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**Slack**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/tutorial_slack_search.png)

5. 結果ウィンドウで **[Slack]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/tutorial_slack_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Slack で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Slack ユーザーが Azure AD で認識されている必要があります。 つまり、Azure AD ユーザーと Slack の関連ユーザーの間でリンク関係が確立されている必要があるということです。

Slack で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Slack で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Slack のテスト ユーザーの作成](#creating-a-slack-test-user)** - Slack で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Slack アプリケーションにシングル サインオンを構成します。

**Slack で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Slack** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_samlbase.png)

3. **[Slack のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_url.png)

    a. **[サインオン URL]** ボックスに、`https://<companyname>.slack.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、URL として「`https://slack.com`」を入力します。

    > [!NOTE] 
    > これは実際の値ではありません。 この値は実際のサインオン URL で更新する必要があります。 この値を取得するには、[Slack サポート チーム](https://slack.com/help/contact)にお問い合わせください。
     
4. Slack アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、**[ユーザー識別子]** として **[user.mai]l** を選択し、以下の表に示す行ごとに、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |
    | first_name | User.givenname |
    | last_name | User.surname |
    | User.Email | User.mail |  
    | User.Username | user.userprincipalname |

    a. **[属性]** をクリックして **[属性の編集]** ダイアログ ボックスを開き、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_attribute1.png)

    a. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    b. **[値]** 一覧から、その行に対して表示される属性値を選択します。
    
    c. **[OK]**

6. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure Single Sign-On](./media/active-directory-saas-slack-tutorial/tutorial_slack_certificate.png)

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

8. **[Slack Configuration]** (Slack 構成) セクションで、**[Configure Slack]** (Slack を構成する) をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML エンティティ ID と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_configure.png) 

9.  別の Web ブラウザー ウィンドウで、Slack 企業サイトに管理者としてログインします。

10.  **[Microsoft Azure AD]**、**[Team Settings]** の順に選択します。

     ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

11.  **[Team Settings]** セクションで、**[Authentication]** タブをクリックし、**[Change Settings]** をクリックします。

     ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

12. **[SAML Authentication Settings]** ダイアログで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a.  **[SAML 2.0 エンドポイント (HTTP)]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    b.  **[ID プロバイダーの発行者]** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。

    c.  ダウンロードした証明書をメモ帳で開き、その内容をクリップボードにコピーし、**[Public Certificate]** ボックスに貼り付けます。

    d. 必要に応じて、Slack チームに上記の 3 つの設定を構成します。 設定の詳細については、**Slack の SSO 構成ガイド**をこちらから参照してください。 `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  **[Save Configuration]**をクリックします。
     
    <!-- Deselect **Allow users to change their email address**.

    e.  Select **Allow users to choose their own username**.

    f.  As **Authentication for your team must be used by**, select **It’s optional**. -->

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="creating-a-slack-test-user"></a>Slack テスト ユーザーの作成

このセクションの目的は、Slack で Britta Simon というユーザーを作成することです。 Slack では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 存在しない Slack ユーザーにアクセスしようとすると、新しいユーザーが自動的に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Slack のサポート チーム](https://slack.com/help/contact)にお問い合わせください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Slack へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Slack に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Slack]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Slack] タイルをクリックすると、Slack アプリケーションに自動的にサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-slack-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-slack-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-slack-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-slack-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-slack-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-slack-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-slack-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-slack-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-slack-tutorial/tutorial_general_203.png


