---
title: "チュートリアル: Azure Active Directory と OfficeSpace Software の統合 | Microsoft Docs"
description: "Azure Active Directory と OfficeSpace Software の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 43d2ecfe851d8f6c43cd4ce7fc4bd872818f4137
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>チュートリアル: Azure Active Directory と OfficeSpace Software の統合

このチュートリアルでは、OfficeSpace Software と Azure Active Directory (Azure AD) を統合する方法について説明します。

OfficeSpace Software と Azure AD の統合には、次の利点があります。

- OfficeSpace Software にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に OfficeSpace Software にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

OfficeSpace Software と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン対応の OfficeSpace Software サブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの OfficeSpace Software の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-officespace-software-from-the-gallery"></a>ギャラリーからの OfficeSpace Software の追加
Azure AD への OfficeSpace Software の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に OfficeSpace Software を追加する必要があります。

**ギャラリーから OfficeSpace Software を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**OfficeSpace Software**」と入力し、結果ウィンドウで **OfficeSpace Software** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の OfficeSpace Software](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、OfficeSpace Software で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する OfficeSpace Software ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと OfficeSpace Software の関連ユーザーの間で、リンク関係が確立されている必要があります。

OfficeSpace Software で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

OfficeSpace Software で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[OfficeSpace Software のテスト ユーザーの作成](#create-a-officespace-software-test-user)** - OfficeSpace Software で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、OfficeSpace Software アプリケーションでシングル サインオンを構成します。

**OfficeSpace Software で Azure AD のシングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **OfficeSpace Software** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_samlbase.png)

3. **[OfficeSpace Software のドメインと URL]** セクションで、次の手順を実行します。

    ![[OfficeSpace Software のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company name>.officespacesoftware.com/users/sign_in/saml` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`<company name>.officespacesoftware.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これの値の取得については、[OfficeSpace Software クライアント サポート チーム](mailto:support@officespacesoftware.com)にお問い合わせください。 

4. OfficeSpace Software アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成してください。 この属性の値は、アプリケーション統合ページの **[User Attributer]** セクションで管理できます。 次のスクリーンショットはその例です。
    
    ![属性の構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_attribute.png)

5. **[シングル サインオン]** ダイアログの **[ユーザー属性]** セクションで、**[ユーザー識別子]** として **[user.mai]l** を選択し、以下の表に示す行ごとに、次の手順を実行します。
    
    | 属性名 | 属性値 |
    | --- | --- |    
    | 電子メール | User.mail |
    | name | user.displayname |
    | first_name | User.givenname |
    | last_name | User.surname |

    a. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。

    ![構成の追加 ](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_04.png)

    ![属性の構成](./media/active-directory-saas-officespace-tutorial/tutorial_attribute_05.png)
    
    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。
    
    c. **[値]** 一覧から、その行に対して表示される値を入力します。
    
    d. **[OK]** をクリックします。
 
6. **[SAML 署名証明書]** セクションで、証明書の **[拇印]** の値をコピーします。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_certificate.png) 

7. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-officespace-tutorial/tutorial_general_400.png)

8. **[OfficeSpace Software Configuration (OfficeSpace Software 構成)]** セクションで **[Configure OfficeSpace Software (OfficeSpace Software の構成)]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **クイック リファレンス セクション**から、**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![OfficeSpace Software の構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_configure.png) 

9. 別の Web ブラウザーのウィンドウで、管理者として OfficeSpace Software テナントにログインします。

10. **[設定]** に移動して、**[コネクタ]**をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_002.png)

11. **[SAML 認証]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_003.png)

12. **[SAML Authentication]** セクションで、次の手順に従います。

    ![アプリ側でのシングル サインオンの構成](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_004.png)

    a. **[Logout provider url]\(ログアウト プロバイダー URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    b. **[Client idp target url]\(クライアント idp ターゲット URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。

    c. Azure Portal からコピーした **[拇印]** の値を**[Client IDP certificate fingerprint]\(クライアント IDP 証明書フィンガープリント\)** ボックスに貼り付けます。 

    d. **[設定の保存]**をクリックします。


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-officespace-tutorial/create_aaduser_01.png)

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-officespace-tutorial/create_aaduser_02.png)

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/active-directory-saas-officespace-tutorial/create_aaduser_03.png)

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-officespace-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。
 
### <a name="create-a-officespace-software-test-user"></a>OfficeSpace Software のテスト ユーザーの作成

このセクションの目的は、OfficeSpace Software で Britta Simon というユーザーを作成することです。 OfficeSpace Software では、ジャスト イン タイム プロビジョニングがサポートされています。この設定は、既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 ユーザーが存在しない場合は、OfficeSpace Software へのアクセスを試みたときに、新しいユーザーが自動的に作成されます。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合は、[OfficeSpace Software サポート チーム](mailto:support@officespacesoftware.com)にお問い合わせください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に OfficeSpace Software へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**OfficeSpace Software に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で、**[OfficeSpace Software]** を選択します。

    ![アプリケーションの一覧の OfficeSpace Software リンク](./media/active-directory-saas-officespace-tutorial/tutorial_officespace_app.png)  

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [OfficeSpace Software] タイルをクリックすると、自動的に OfficeSpace Software アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-officespace-tutorial/tutorial_general_203.png

