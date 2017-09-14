---
title: "チュートリアル: Azure Active Directory と FreshDesk の統合 | Microsoft Docs"
description: "Azure Active Directory と FreshDesk の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 47609ab453cf3db3cb745f91ed23904ff90e7b41
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017

---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>チュートリアル: Azure Active Directory と FreshDesk の統合

このチュートリアルでは、FreshDesk と Azure Active Directory (Azure AD) を統合する方法について説明します。

FreshDesk と Azure AD の統合には、次の利点があります。

- FreshDesk にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで FreshDesk に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

FreshDesk と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- FreshDesk でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の試用環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の試用版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの FreshDesk の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-freshdesk-from-the-gallery"></a>ギャラリーからの FreshDesk の追加
Azure AD への FreshDesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FreshDesk を追加する必要があります。

**ギャラリーから FreshDesk を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. ダイアログの上部にある **[追加]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに、「**FreshDesk**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. 結果ウィンドウで **[FreshDesk]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、FreshDesk で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する FreshDesk ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと FreshDesk の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係は、Azure AD の **[ユーザー名]** の値を、FreshDesk の **[Username (ユーザー名)]** の値として割り当てることで確立されます。

FreshDesk で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[FreshDesk のテスト ユーザーの作成](#creating-a-freshdesk-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを FreshDesk で作成します。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、FreshDesk アプリケーションにシングル サインオンを構成します。

**FreshDesk で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **FreshDesk** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. **[FreshDesk のドメインと URL]** セクションで、**[サインイン URL]** として `https://<tenant-name>.freshdesk.com` または FreshDesk が提案したその他の値を入力してください。

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 この値は実際のサインオン URL で更新する必要があります。 この値を取得するには、[FreshDesk サポート チーム](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)に問い合わせてください。  

4. **[SAML 署名証明書]** セクションで、**[証明書]** をクリックし、コンピューターに証明書を保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. **[FreshDesk 構成]** セクションで、**[FreshDesk の構成]** をクリックして、[サインオンの構成] ウィンドウを開きます。 **[クイック リファレンス]** セクションから SAML シングル サインオン サービスの URL とサインアウト URL をコピーします。

    ![Configure Single Sign-On](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. 別の Web ブラウザー ウィンドウで、Freshdesk 企業サイトに管理者としてログインします。

8. 上部のメニューで **[Admin]**をクリックします。
   
   ![管理](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

9. **[全般設定]** タブで **[セキュリティ]** をクリックします。
   
   ![Security (セキュリティ)](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "Security")

10. **[セキュリティ]** セクションで、次の手順を実行します。
   
    ![Single Sign On](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "Single Sign On")
   
    a. **[シングル サインオン (SSO)]** で **[オン]** を選択します。

    b. **[SAML SSO]**を選択します。

    c. Azure Portal からコピーした **SAML シングル サインオン サービスの URL** を **[SAML Login URL (SAML ログイン URL)]** テキストボックスに入力します。

    d. Azure Portal からコピーした **サインアウト URL** を **[ログアウト URL]** テキストボックスに入力します。

    e. ダウンロードした Azure Portal の証明書から **[拇印]** の値をコピーして、**[セキュリティ証明書フィンガープリント]** ボックスに貼り付けます。  
 
    >[!TIP]
    >詳細については、「[How to retrieve a certificate's thumbprint value (証明書の拇印の値を取得する方法)](http://youtu.be/YKQF266SAxI)」をご覧ください。 
    
    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[保存]**をクリックします。


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="creating-a-freshdesk-test-user"></a>FreshDesk テスト ユーザーの作成

Azure AD ユーザーが FreshDesk にログインできるようにするには、そのユーザーを FreshDesk にプロビジョニングする必要があります。  
FreshDesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Freshdesk** テナントにログインします。
2. 上部のメニューで **[Admin]**をクリックします。
   
   ![管理](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3. **[全般設定]** タブで **[エージェント]** をクリックします。
   
   ![Agents](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "Agents")

4. **[新しいエージェント]**をクリックします。
   
    ![New Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "New Agent")

5. [エージェント情報] ダイアログで、次の手順を実行します。
   
   ![Agent Information](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "Agent Information")
   
   a. **[フル ネーム]** ボックスに、プロビジョニングする Azure AD のアカウントの名前を入力します。

   b. **[電子メール]** ボックスに、プロビジョニングする Azure AD アカウントの Azure AD 電子メール アドレスを入力します。

   c. **[タイトル]** ボックスに、プロビジョニングする Azure AD アカウントのタイトルを入力します。

   d. **[エージェント ロール]** を選択し、**[割り当て]** をクリックします。
       
   e. [ **Save**] をクリックします。     
   
    >[!NOTE]
    >Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 
    > 
    
    >[!NOTE]
    >Freshdesk から提供されている他の Freshdesk ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。 FreshDesk へ。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Box へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**FreshDesk に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[FreshDesk]**を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [FreshDesk] タイルをクリックすると、FreshDesk アプリケーションにサインオンするログイン ページが表示されます。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png


