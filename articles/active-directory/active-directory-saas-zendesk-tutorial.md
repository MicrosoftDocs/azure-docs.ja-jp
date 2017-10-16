---
title: "チュートリアル: Azure Active Directory と Zendesk の統合 | Microsoft Docs"
description: "Azure Active Directory と Zendesk の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: 51c06d838c5ed6286dfb99ea25faaaf33bad5f3c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>チュートリアル: Azure Active Directory と Zendesk の統合

このチュートリアルでは、Zendesk と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zendesk と Azure AD の統合には、次の利点があります。

- Zendesk にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に Zendesk にサインオン (シングル サインオン) することができます
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Zendesk と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Zendesk でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zendesk の追加
2. Azure AD シングル サインオンの構成とテスト


## <a name="adding-zendesk-from-the-gallery"></a>ギャラリーからの Zendesk の追加
Azure AD への Zendesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zendesk を追加する必要があります。

**ギャラリーから Zendesk を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[新しいアプリケーション]** ボタンをクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**Zendesk**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_search.png)

5. 結果ウィンドウで **[Zendesk]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zendesk で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zendesk ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zendesk の関連ユーザーの間で、リンク リレーションシップが確立されている必要があります。

このリンク リレーションシップを確立するには、Azure AD の **[ユーザー名]** の値を Zendesk の **[[Username]\(ユーザー名\)]** の値として割り当てます。

Zendesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[Zendesk テスト ユーザーの作成](#creating-a-zendesk-test-user)** - Zendesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にし、Zendesk アプリケーションでシングル サインオンを構成します。

**Zendesk で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **Zendesk** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. **[Zendesk のドメインと URL]** セクションで、次の手順を実行します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.zendesk.com` のパターンを使用して値を入力します。

    b. **[識別子]** ボックスに、`https://<subdomain>.zendesk.com` の形式で値を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子 URL でこれらの値を更新してください。 この値を取得するには、[Zendesk サポート チーム](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise)にお問い合わせください。 

4. Zendesk は、特定の形式で構成された SAML アサーションを受け入れます。 必須の SAML 属性はありませんが、必要に応じて以下の手順に従って **[ユーザー属性]** セクションから属性を追加できます。 

     ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. **[その他のすべてのユーザー属性を表示および編集する]** チェック ボックスをオンにします。
     
    ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_attributes2.png)
   
    b. **[属性の追加]** をクリックして **[属性の追加]** ダイアログを開きます。
    
    ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_attribute_05.png)

    c. **[名前]** ボックスに、属性名 (**emailaddress** など) を入力します。
    
    d. **[値]** 一覧から、属性値 (**user.mail** など) を選択します。
    
    e. **[OK]** をクリックします。
 
    > [!NOTE] 
    > 既定では、Azure AD に含まれていない属性を追加するには拡張属性を使用します。 「[SAMLに設定できるユーザー属性](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-)」をクリックすると、**Zendesk** が受け入れる SAML 属性の完全な一覧が表示されます。 

5. **[SAML 署名証明書]** セクションで、証明書の **THUMBPRINT** の値をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_certificate.png) 

6. **[Zendesk Configuration]\(Zendesk 構成\)** セクションで、**[Zendesk を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから**サインアウト URL と SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_configure.png) 

7. 別の Web ブラウザー ウィンドウで、Zendesk 企業サイトに管理者としてログインします。

8. **[Admin]**をクリックします。

9. 左側のナビゲーション ウィンドウで、**[設定]**、**[セキュリティ]** の順にクリックします。

10. **[Security]\(セキュリティ\)** ページで、次の手順に従います。 
   
     ![Security (セキュリティ)](./media/active-directory-saas-zendesk-tutorial/ic773089.png "Security")

    ![シングル サインオン](./media/active-directory-saas-zendesk-tutorial/ic773090.png "シングル サインオン")

     a. **[Admin & Agents]\(管理者とエージェント\)** タブをクリックします。

     b. **[シングルサインオン (SSO) と SAML]** を選択し、**[SAML]** を選択します。

     c. **[SAML SSO URL]\(SAML SSO URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。 

     d. **[Remote Logout URL]\(リモート ログアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。
        
     e. **[Certificate Fingerprint] \(証明書のフィンガープリント)** テキスト ボックスに、Azure Portal からコピーした証明書の **THUMBPRINT** 値を貼り付けます。
     
     f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[保存]**をクリックします。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zendesk-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zendesk-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zendesk-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-zendesk-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。 

### <a name="creating-a-zendesk-test-user"></a>Zendesk テスト ユーザーの作成

Azure AD ユーザーが **Zendesk** にログインできるようにするには、ユーザーを **Zendesk** にプロビジョニングする必要があります。  
アプリで割り当てられたロールに応じて、想定される動作があります。

 1. **エンドユーザー** アカウントは、サインインするときに自動的にプロビジョニングされます。
 2. **エージェント**と**管理者**のアカウントは、サインインの前に **Zendesk** で手動でプロビジョニングする必要があります。
 
**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Zendesk** テナントにログインします。

2. **[顧客リスト]** タブを選択します。

3. **[ユーザー]** タブを選択し、**[追加]** をクリックします。
   
    ![ユーザーの追加](./media/active-directory-saas-zendesk-tutorial/ic773632.png "ユーザーの追加")
4. プロビジョニングする既存の Azure AD アカウントの電子メール アドレスを入力し、 **[追加]**をクリックします。
   
    ![New user](./media/active-directory-saas-zendesk-tutorial/ic773633.png "New user")

> [!NOTE]
> Zendesk から提供されている他の Zendesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zendesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Zendesk に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[Zendesk]** を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-zendesk-tutorial/tutorial_zendesk_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Zendesk] タイルをクリックすると、自動的に Zendesk アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zendesk-tutorial/tutorial_general_203.png
