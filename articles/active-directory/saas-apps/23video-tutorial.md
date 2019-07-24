---
title: チュートリアル:Azure Active Directory と 23 Video の統合 | Microsoft Docs
description: Azure Active Directory と 23 Video の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 5e73dd1d-3995-4a73-b9cf-1b2318d49cb3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0cfaaf0d4ae692581d63c7745660ffeacfb11f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175746"
---
# <a name="tutorial-azure-active-directory-integration-with-23-video"></a>チュートリアル:Azure Active Directory と 23 Video の統合

このチュートリアルでは、23 Video と Azure Active Directory (Azure AD) を統合する方法について説明します。

23 Video と Azure AD の統合には、次の利点があります。

- 23 Video にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に 23 Video にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

23 Video と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- 23 Video でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの 23 Video の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-23-video-from-the-gallery"></a>ギャラリーからの 23 Video の追加
Azure AD への 23 Video の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に 23 Video を追加する必要があります。

**ギャラリーから 23 Video を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

4. 検索ボックスに、「 **23 Video**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/23video-tutorial/tutorial_23video_search.png)

5. 結果ウィンドウで **[23 Video]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/23video-tutorial/tutorial_23video_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、23 Video で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する 23 Video ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと 23 Video の関連ユーザーの間で、リンク関係が確立されている必要があります。

23 Video で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

23 Video で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[23 Video テスト ユーザーの作成](#creating-a-23-video-test-user)** - 23 Video で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、23 Video アプリケーションでシングル サインオンを構成します。

**23 Video で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **23 Video** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/23video-tutorial/tutorial_23video_samlbase.png)

3. **[23 Video のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/23video-tutorial/tutorial_23video_url.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.23video.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://www.23video.com/saml/trust/<uniqueid>` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[23 Video クライアント サポート チーム](mailto:support@23company.com)に問い合わせてください。 
 
4. **[SAML 署名証明書]** セクションで、**[Certificate (Base64) (証明書 (Base64)) ]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![Configure single sign-on](./media/23video-tutorial/tutorial_23video_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/23video-tutorial/tutorial_general_400.png)

6. **[23 Video 構成]** セクションで、**[23 Video の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/23video-tutorial/tutorial_23video_configure.png) 

7. **23 Video** 側にシングル サインオンを構成するには、ダウンロードされた**証明書 (Base64)**、**サインアウト URL、SAML エンティティ ID、および SAML シングル サインオン サービス URL** を [23 Video サポート チーム](mailto:support@23company.com)に送信する必要があります。 


> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/23video-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/23video-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/23video-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/23video-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-23-video-test-user"></a>23 Video テスト ユーザーの作成

このセクションの目的は、23 Video で Britta Simon というユーザーを作成することです。

**23 Video で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. 23 Video 企業サイトに管理者としてサインオンします。

2. **[設定]** に移動します。
 
3. **[Users (ユーザー)]** セクションで **[Configure (構成)]** をクリックします。
   
    ![ユーザーの割り当て][400]

4. **[Add a new user (新しいユーザーの追加)]** をクリックします。 
   
    ![ユーザーの割り当て][401]

5. **[Invite someone to join this site (別のユーザーをこのサイトに招待)]** セクションで、次の手順を実行します。
   
    ![ユーザーの割り当て][402]

    a. **[E-mail addresses (メール アドレス)]** ボックスに、Azure AD の Britta Simon の電子メール アドレスを入力します。  
 
    b. **[Add the user (ユーザーの追加)]** をクリックします。   

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に 23 Video へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**23 Video に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーションの一覧で **[23 Video]** を選択します。

    ![Configure single sign-on](./media/23video-tutorial/tutorial_23video_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [23 Video] タイルをクリックすると、自動的に 23 Video アプリケーションにサインオンします。 

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/23video-tutorial/tutorial_general_01.png
[2]: ./media/23video-tutorial/tutorial_general_02.png
[3]: ./media/23video-tutorial/tutorial_general_03.png
[4]: ./media/23video-tutorial/tutorial_general_04.png

[100]: ./media/23video-tutorial/tutorial_general_100.png

[200]: ./media/23video-tutorial/tutorial_general_200.png
[201]: ./media/23video-tutorial/tutorial_general_201.png
[202]: ./media/23video-tutorial/tutorial_general_202.png
[203]: ./media/23video-tutorial/tutorial_general_203.png

[400]: ./media/23video-tutorial/tutorial_23video_10.png
[401]: ./media/23video-tutorial/tutorial_23video_11.png
[402]: ./media/23video-tutorial/tutorial_23video_12.png
