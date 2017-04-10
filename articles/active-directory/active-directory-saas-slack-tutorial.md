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
ms.date: 01/25/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9c027decf5d982519bc9f41aa9588fa431ef3975
ms.openlocfilehash: 17ad8938cbee539e74f87210077a12c72e777edc
ms.lasthandoff: 02/24/2017


---
# <a name="tutorial-azure-ad-integration-with-slack"></a>チュートリアル: Azure AD と Slack の統合

このチュートリアルでは、Slack と Azure Active Directory (Azure AD) を統合する方法について説明します。

Azure AD と Slack を統合すると、次のことができます。

* Slack にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで自動的に Slack にシングル サインオン (SSO) できるように設定可能です。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Slack と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション
* Slack SSO が有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境は使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

* 必要な場合にのみ、運用環境を使用します。
* Azure AD の評価環境がない場合は、[1 か月の試用版を入手します](https://azure.microsoft.com/pricing/free-trial/)。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境での Azure AD SSO をテストします。 このシナリオは、2 つの主な構成要素で構成されます。

* ギャラリーからの Slack の追加
* Azure AD SSO の構成とテスト

## <a name="add-slack-from-the-gallery"></a>ギャラリーからの Slack の追加
Azure AD と Slack の統合を構成するには、次の手順でギャラリーから管理対象 SaaS アプリの一覧に Slack を追加する必要があります。

1. [Azure ポータル](https://portal.azure.com)を開きます。
2. 左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン][1]

3. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレードの [すべてのアプリケーション] ボタン][2]

4. **[すべてのアプリケーション]** ダイアログ ボックスの上部で **[追加]** をクリックします。

    ![[すべてのアプリケーション] ダイアログ ボックスの [追加] ボタン][3]

5. 検索ボックスに「**Slack**」と入力します。

    ![[アプリケーションの追加] の検索ボックス](./media/active-directory-saas-slack-tutorial/tutorial_slack_000.png)

6. 結果ウィンドウで **[Slack]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果ウィンドウでの [Slack] の選択](./media/active-directory-saas-slack-tutorial/tutorial_slack_0001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーを使用して、Slack で Azure AD SSO を構成し、テストします。

SSO が機能するには、Azure AD ユーザーと Slack の対応するユーザーの間で、リンク関係が確立されている必要があります。 このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を Slack の **[Username (ユーザー名)]** の値として割り当てます。

Slack で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. [Azure AD SSO の構成](#configuring-azure-ad-single-sign-on)。ユーザーがこの機能を使用できるようにします。
2. [Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)。Britta Simon というユーザーで Azure AD SSO をテストします。
3. [Slack のテスト ユーザーの作成](#creating-a-slack-test-user)。Azure AD のユーザーである Britta Simon に、Slack で対応するユーザーを作成してリンクさせます。
4. [Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)。Britta Simon というユーザーが Azure AD SSO を使用できるようにします。
5. [SSO のテスト](#testing-single-sign-on)。構成が機能することを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、次の手順で Azure Portal で Azure AD SSO を有効にし、Slack アプリケーションに SSO を構成します。

1. Azure Portal の **Slack** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Slack アプリケーション統合ページ][4]

2. **[シングル サインオン]** ダイアログ ボックスの **[モード]** の一覧で **[SAML ベースのサインオン]** を選択し、SSO を有効にします。

    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-slack-tutorial/tutorial_slack_01.png)

3. **[Slack のドメインと URL]** で、次の手順を実行します。

    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-slack-tutorial/tutorial_slack_02.png)

    a. **[サインオン URL]** ボックスに、*https://<company name>.slack.com* という命名規則を使用した URL を入力します。

    b. **[識別子]** ボックスに、「**https://slack.com**」と入力します。

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 ここでは、URL と識別子には一意の値を使用することをお勧めします。 後で、実際の URL と識別子で値を更新します。 値を取得するには、[Slack サポート チーム](https://slack.com/help/contact)に問い合わせてください。

4. Slack アプリケーションでは、Security Assertion Markup Language (SAML) のアサーションを特定の形式で表示する必要があります。 次のスクリーンショットに示すように、Slack アプリケーション統合ページの **[ユーザー属性]** セクションで、要求を構成し、属性の値を管理します。

    ![[ユーザーの属性] セクションでの要求の構成](./media/active-directory-saas-slack-tutorial/tutorial_slack_03.png)

5. **[シングル サインオン]** ダイアログ ボックスの **[ユーザー属性]** セクションで、**[ユーザー識別子]** として **[user.mail]** を選択します。 表の行ごとに、次の手順を実行します。

    | 属性名 | 属性名 |
    | --- | --- |    
    | User.Email | user.userprincipalname |
    | first_name | User.givenname |
    | last_name | User.surname |
    | User.Username | extractmailprefix([userprincipalname]) |

    a. **[属性の追加]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_04.png)

    b. **[属性の追加]** ダイアログ ボックスの **[名前]** ボックスに、表の**属性名**の列にある名を入力します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-slack-tutorial/tutorial_slack_05.png)

    c. **[値]** ボックスに、表の**属性値**の列の最初の値を入力します。

    d. **[OK]**をクリックします。

    e. 表の次の&3; 行についても "a" ～ "d" の手順を繰り返します。

6. **[SAML 署名証明書]** で、**[新しい証明書の作成]** をクリックします。

    ![証明書を作成する](./media/active-directory-saas-slack-tutorial/tutorial_slack_06.png)     

7. **[新しい証明書の作成]** ダイアログ ボックスで、**[カレンダー]** ボタンをクリックし、有効期限の日付を選択して、**[保存]** をクリックます。

    ![証明書の有効期限の日付の選択](./media/active-directory-saas-slack-tutorial/tutorial_general_300.png)

8. **[SAML 署名証明書]** で、**[Make new certificate active (新しい証明書を有効にする)]** チェック ボックスをオンにし、**[保存]** をクリックします。

    ![SAML 署名証明書の有効化](./media/active-directory-saas-slack-tutorial/tutorial_slack_07.png)

9. **[ロールオーバー証明書]** ポップアップ ウィンドウで、**[OK]** をクリックします。

    ![[ロールオーバー証明書] ポップアップ ウィンドウ](./media/active-directory-saas-slack-tutorial/tutorial_general_400.png)

10. **[SAML 署名証明書]** で、**[証明書 (Base64)]** をクリックし、ローカル ドライブに証明書ファイルを保存します。

    ![ローカル ドライブへの証明書の保存](./media/active-directory-saas-slack-tutorial/tutorial_slack_08.png)

11. **[Slack Configuration (Slack 構成)]** で、**[Configure Slack (Slack を構成する)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。

    ![[Configure Slack (Slack を構成する)] をクリックして [サインオンの構成] ウィンドウを開く](./media/active-directory-saas-slack-tutorial/tutorial_slack_09.png)

    ![[サインオンの構成] ウィンドウ](./media/active-directory-saas-slack-tutorial/tutorial_slack_10.png)

12. 新しく Web ブラウザー ウィンドウを開き、Slack 企業サイトに管理者としてサインインします。

13. **[Microsoft Azure AD]**、**[Team Settings (チーム設定)]** の順に移動します。

    ![Slack 企業サイトの [Microsoft Azure AD] の [Team Settings チーム設定] ボタン](./media/active-directory-saas-slack-tutorial/tutorial_slack_001.png)

14. **[Team Settings (チーム設定)]** で、**[Authentication (認証)]** タブをクリックし、**[Change Settings (設定の変更)]** をクリックします。

    ![[Team Settings (チーム設定)] ページの [Change Settings (設定の変更)] ボタン](./media/active-directory-saas-slack-tutorial/tutorial_slack_002.png)

15. **[SAML Authentication Settings (SAML 認証設定)]** ダイアログ ボックスで、次の手順を実行します。

    ![[SAML Authentication Settings (SAML 認証設定)] ダイアログ ボックス](./media/active-directory-saas-slack-tutorial/tutorial_slack_003.png)

    a. **[SAML 2.0 Endpoint (HTTP) (SAML 2.0 エンドポイント (HTTP))]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Single Sign-on Service URL (SAML シングル サインオン サービス URL)]** の値を入力します。

    b. **[Identity Provider Issuer (ID プロバイダー発行者)]** ボックスに、Azure AD アプリケーション構成ウィンドウの **[SAML Entity ID (SAML エンティティ ID)]** の値を入力します。

    c. ダウンロードした証明書ファイルをメモ帳で開き、その内容をコピーして、**[Public Certificate (パブリック証明書)]** ボックスに貼り付けます。

    d. 必要に応じて、Slack チームに上記の&3; つの設定を構成します。 設定の詳細については、「[Guide to single sign-on with Slack (Slack でのシングル サインオンのガイド)](https://get.slack.help/hc/en-us/articles/220403548-Guide-to-single-sign-on-with-Slack)」を参照してください。

    e. **[Save Configuration]**をクリックします。

    <!-- **[Allow users to change their email address]** (ユーザーにメール アドレスの変更を許可する) を選択解除します。

    e. **[Allow users to choose their own username]** を選択します。

    f. **[Authentication for your team must be used by]** で、**[It’s optional]** を選択します。 -->


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションでは、Azure Portal で次の手順に従って Britta Simon というテスト ユーザーを作成します。

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-slack-tutorial/create_aaduser_01.png)

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![Azure AD の [すべてのユーザー] ボタン](./media/active-directory-saas-slack-tutorial/create_aaduser_02.png)

3. **[すべてのユーザー]** ダイアログ ボックスの上部で **[追加]** をクリックします。

    ![[ユーザーの追加] ダイアログ ボックスの [追加] ボタン](./media/active-directory-saas-slack-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の情報を入力します。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-slack-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **[作成]**をクリックします。

### <a name="create-a-slack-test-user"></a>Slack のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Slack に作成します。 Slack では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ Slack に存在しない場合は、Slack にアクセスしようとしたときに新しいユーザーが作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[Slack サポート チーム](https://slack.com/help/contact)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Slack へのアクセスを許可することで、ユーザーである Britta Simon が Azure SSO を使用できるようにします。

![Azure SSO のユーザーの割り当て][200]

Slack にユーザーである Britta Simon を割り当てるには、次の手順を実行します。

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

2. **アプリケーション**の一覧で **[Slack]** を選択します。

    ![Azure Portal のアプリケーションの一覧](./media/active-directory-saas-slack-tutorial/tutorial_slack_50.png)

3. 左側のウィンドウで **[ユーザーとグループ]** をクリックします。

    ![左側のウィンドウの [ユーザーとグループ] ボタン][202]

4. **[追加]** ボタンをクリックし、**[割り当ての追加]** ブレードで **[ユーザーとグループ]** を選択します。

    ![[追加] ボタンと [割り当ての追加] ブレード][203]

5. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** 一覧から、**[Britta Simon]** を選択します。

6. **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ブレードで、**[割り当て]** ボタンをクリックします。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

構成をテストするには、アクセス パネルに移動し、**[Slack]** タイルをクリックします。 ユーザーは、自動的に Slack アプリケーションにサインインされます。


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

