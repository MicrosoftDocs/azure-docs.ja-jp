---
title: チュートリアル:Azure Active Directory と Showpad の統合 | Microsoft Docs
description: Azure Active Directory と Showpad の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 48b6bee0-dbc5-4863-964d-75b25e517741
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec16aabeb1c8b956b4e525aca4e1c2eb7b133686
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188561"
---
# <a name="tutorial-azure-active-directory-integration-with-showpad"></a>チュートリアル:Azure Active Directory と Showpad の統合

このチュートリアルでは、Showpad と Azure Active Directory (Azure AD) を統合する方法について説明します。

Showpad と Azure AD の統合には、次の利点があります。

- Showpad にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Showpad にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Showpad と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Showpad でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Showpad の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-showpad-from-the-gallery"></a>ギャラリーからの Showpad の追加

Azure AD への Showpad の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Showpad を追加する必要があります。

**ギャラリーから Showpad を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Showpad**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/showpad-tutorial/tutorial_showpad_search.png)

1. 結果ウィンドウで **[Showpad]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/showpad-tutorial/tutorial_showpad_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Showpad で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Showpad ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Showpad の関連ユーザーの間で、リンク関係が確立されている必要があります。

Showpad で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Showpad で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Showpad テスト ユーザーの作成](#creating-a-showpad-test-user)** - Showpad で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Showpad アプリケーションでシングル サインオンを構成します。

**Showpad で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Showpad** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/showpad-tutorial/tutorial_showpad_samlbase.png)

1. **[Showpad のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/showpad-tutorial/tutorial_showpad_url.png)

    a. **[サインオン URL]** ボックスに、`https://<comapany-name>.showpad.biz/login` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<company-name>.showpad.biz` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 この値を取得するには、[Showpad サポート チーム](https://help.showpad.com)にお問い合わせください。 
 


1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/showpad-tutorial/tutorial_showpad_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/showpad-tutorial/tutorial_general_400.png)

1. Showpad テナントに管理者としてサインオンします。

1. 上部のメニューで **[Settings]** をクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/showpad-tutorial/tutorial_showpad_001.png) 

1. **[シングル サインオン]** に移動して **[有効化]** をクリックします。
   
    ![アプリ側でのシングル サインオンの構成](./media/showpad-tutorial/tutorial_showpad_002.png)

1. **[Add a SAML 2.0 Service ]** ダイアログで、次の手順を実行します。
   
    ![アプリ側でのシングル サインオンの構成](./media/showpad-tutorial/tutorial_showpad_003.png) 
   
    a. **[名前]** ボックスに、ID プロバイダーの名前 (自分の会社名など) を入力します。
   
    b. **[Metadata Source]** として、**[XML]** を選択します。
   
    c. Azure Portal からダウンロードしたメタデータ XML ファイルの内容をコピーし、**[メタデータ XML]** ボックスに貼り付けます。
   
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **[Auto-provision accounts for new users when they log in]** を選択します。
   
    e. **[送信]** をクリックします。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/showpad-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/showpad-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/showpad-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/showpad-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-a-showpad-test-user"></a>Showpad テスト ユーザーの作成

このセクションの目的は、Showpad で Britta Simon というユーザーを作成することです。 

Showpad では、ジャストインタイム プロビジョニングがサポートされています。 プロビジョニングは、「 **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)**」を参照してください。 

このセクションでは必要な操作はありません。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Showpad へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Showpad に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Showpad]** を選択します。

    ![Configure single sign-on](./media/showpad-tutorial/tutorial_showpad_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Showpad] タイルをクリックすると、自動的に Showpad アプリケーションにサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/showpad-tutorial/tutorial_general_01.png
[2]: ./media/showpad-tutorial/tutorial_general_02.png
[3]: ./media/showpad-tutorial/tutorial_general_03.png
[4]: ./media/showpad-tutorial/tutorial_general_04.png

[100]: ./media/showpad-tutorial/tutorial_general_100.png

[200]: ./media/showpad-tutorial/tutorial_general_200.png
[201]: ./media/showpad-tutorial/tutorial_general_201.png
[202]: ./media/showpad-tutorial/tutorial_general_202.png
[203]: ./media/showpad-tutorial/tutorial_general_203.png

