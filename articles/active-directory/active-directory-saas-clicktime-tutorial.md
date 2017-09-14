---
title: "チュートリアル: Azure Active Directory と ClickTime の統合 | Microsoft Docs"
description: "Azure Active Directory と ClickTime の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 0e0123a40d52dfd7a2e29c29cb2239e979089ca9
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>チュートリアル: Azure Active Directory と ClickTime の統合

このチュートリアルでは、ClickTime と Azure Active Directory (Azure AD) を統合する方法について説明します。

ClickTime と Azure AD の統合には、次の利点があります。

- ClickTime にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで ClickTime に自動的にサインオン (シングル サインオン) できるように、設定が可能です。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ClickTime と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- ClickTime でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの ClickTime の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-clicktime-from-the-gallery"></a>ギャラリーからの ClickTime の追加
Azure AD への ClickTime の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ClickTime を追加する必要があります。

**ギャラリーから ClickTime を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

4. 検索ボックスに「**ClickTime**」と入力し、結果パネルで **ClickTime** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、ClickTime で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する ClickTime ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと ClickTime の関連ユーザーの間で、リンク関係が確立されている必要があります。

ClickTime で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

ClickTime で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[ClickTime のテスト ユーザーの作成](#create-a-clicktime-test-user)** - ClickTime で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、ClickTime アプリケーションでシングル サインオンを構成します。

**ClickTime で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **ClickTime** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_samlbase.png)

3. **[ClickTime のドメインと URL]** セクションで、次の手順を実行します。

    ![[ClickTime のドメインと URL] のシングル サインオン情報](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_url.png)

    a. **[識別子]** ボックスに、`https://app.clicktime.com/sp/` のように URL を入力します。
    
    b. **[応答 URL]** ボックスに、次の形式で URL を入力します。 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/active-directory-saas-clicktime-tutorial/tutorial_general_400.png)

6. **[ClickTime 構成]** セクションで、**[ClickTime の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![ClickTime の構成](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_configure.png) 

7. 別の Web ブラウザー ウィンドウで、ClickTime 企業サイトに管理者としてログインします。

8. 上部にあるツールバーで **[Preferences]**、**[Security Settings]** の順にクリックします。

9. **[Single Sign-On Preferences]** 構成セクションで、次の手順を実行します。
   
    ![Security Settings](./media/active-directory-saas-clicktime-tutorial/tic777280.png "Security Settings")
   
    a.  **[Azure AD]** を指定し、シングル サインオン (SSO) を使用したサインインの **[Allow (許可)]** を選択します。
   
    b. **[ID プロバイダー エンドポイント]** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** を貼り付けます。
   
    c.  Azure Portal からダウンロードされた **Base-64 でエンコードされた証明書**を**メモ帳**で開き、その内容をコピーしてから、それを **[X.509 証明書]** ボックスに貼り付けます。
   
    d.  [ **Save**] をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/active-directory-saas-clicktime-tutorial/create_aaduser_01.png) 

2. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。
    
    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/active-directory-saas-clicktime-tutorial/create_aaduser_02.png) 

3. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。
 
    ![[追加] ボタン](./media/active-directory-saas-clicktime-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。
 
    ![[ユーザー] ダイアログ ボックス](./media/active-directory-saas-clicktime-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **[作成]**をクリックします。
 
### <a name="create-a-clicktime-test-user"></a>ClickTime のテスト ユーザーの作成

Azure AD ユーザーが ClickTime にログインできるようにするには、そのユーザーを ClickTime にプロビジョニングする必要があります。  
ClickTime の場合、プロビジョニングは手動で行います。

> [!NOTE]
> ClickTime から提供されている他の ClickTime ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**
1. **ClickTime** テナントにログインします。
2. 上部のツールバーで、**[Company]** をクリックし、**[People]** をクリックします。
   
    ![ユーザー](./media/active-directory-saas-clicktime-tutorial/tic777282.png "People")
3. **[Add Person]**をクリックします。
   
    ![Add Person](./media/active-directory-saas-clicktime-tutorial/tic777283.png "Add Person")
4. [New Person] セクションで、次の手順を実行します。
   
    ![ユーザー](./media/active-directory-saas-clicktime-tutorial/tic777284.png "People")
   
    a.  **[フル ネーム]** ボックスに、ユーザーの氏名 (**Britta Simon** など) を入力します。 
  
    b.  **[メール]** ボックスに、ユーザーのメール アドレス (**brittasimon@contoso.com** など) を入力します。
       
    > [!NOTE]
    > 必要に応じて、新しいユーザー オブジェクトの追加プロパティを設定できます。
   
    c.  [ **Save**] をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に ClickTime へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**ClickTime に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[ClickTime]**を選択します。

    ![アプリケーションの一覧の ClickTime のリンク](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ClickTime] タイルをクリックすると、ClickTime アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png


