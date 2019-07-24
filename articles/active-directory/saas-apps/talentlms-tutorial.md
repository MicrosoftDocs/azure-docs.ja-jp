---
title: チュートリアル:Azure Active Directory と TalentLMS の統合 | Microsoft Docs
description: Azure Active Directory と TalentLMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8fa78ec2b5623dfd010a8fe5709916a47e221a9e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57902197"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>チュートリアル:Azure Active Directory と TalentLMS の統合

このチュートリアルでは、TalentLMS と Azure Active Directory (Azure AD) を統合する方法について説明します。

TalentLMS と Azure AD の統合には、次の利点があります。

- TalentLMS にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に TalentLMS にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

TalentLMS と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- TalentLMS でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、1 か月の試用版を[無料試用版の提供](https://azure.microsoft.com/pricing/free-trial/)のページで入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの TalentLMS の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-talentlms-from-the-gallery"></a>ギャラリーからの TalentLMS の追加
Azure AD への TalentLMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TalentLMS を追加する必要があります。

**ギャラリーから TalentLMS を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**TalentLMS**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/talentlms-tutorial/tutorial_talentlms_search.png)

1. 結果ウィンドウで **TalentLMS** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/talentlms-tutorial/tutorial_talentlms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、TalentLMS で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する TalentLMS ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと TalentLMS の関連ユーザーの間で、リンク関係が確立されている必要があります。

TalentLMS で、Azure AD の **[ユーザー名]** の値を **[Username]\(ユーザー名\)** の値として割り当ててリンク関係を確立します。

TalentLMS で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[TalentLMS のテスト ユーザーの作成](#creating-a-talentlms-test-user)** - TalentLMS で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、TalentLMS アプリケーションでシングル サインオンを構成します。

**TalentLMS で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **TalentLMS** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/talentlms-tutorial/tutorial_talentlms_samlbase.png)

1. **[TalentLMS のドメインと URL]** セクションで、次の手順を実行します。

    ![Configure single sign-on](./media/talentlms-tutorial/tutorial_talentlms_url.png)

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.TalentLMSapp.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`http://<tenant-name>.talentlms.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[TalentLMS クライアント サポート チーム](https://www.talentlms.com/contact)に連絡してください。 
 
1. **[SAML 署名証明書]** セクションで、証明書の **[拇印]** の値をコピーします。

    ![Configure single sign-on](./media/talentlms-tutorial/tutorial_talentlms_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/talentlms-tutorial/tutorial_general_400.png)

1. **[TalentLMS 構成]** セクションで、**[TalentLMS の構成]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/talentlms-tutorial/tutorial_talentlms_configure.png)  

1. 別の Web ブラウザー ウィンドウで、TalentLMS 企業サイトに管理者としてログインします。

1. **[Account & Settings]** セクションで、**[Users]** タブをクリックします。
   
    ![Account & Settings](./media/talentlms-tutorial/IC777296.png "Account & Settings")

1. **[Single Sign-On (SSO)]** をクリックします。

1. [Single Sign-On] セクションで、次の手順に従います。
   
    ![シングル サインオン](./media/talentlms-tutorial/IC777297.png "Single Sign-On")   

    a. **[SSO integration type]** ボックスの一覧から **[SAML 2.0]** を選択します。

    b. **[Identity provider (IDP)]\(ID プロバイダー \(IDP\)\)** ボックスに、Azure Portal からコピーした **SAML エンティティ ID** の値を貼り付けます。
 
    c. Azure Portal の**拇印**の値を、**[Certificate fingerprint]\(証明書のフィンガープリント\)** ボックスに貼り付けます。    

    d.  **[Remote sign-in URL]\(リモート サインイン URL\)** ボックスに、Azure Portal からコピーした **SAML シングル サインオン サービス URL** の値を貼り付けます。
 
    e. **[Remote sign-out URL]\(リモート サインアウト URL\)** ボックスに、Azure Portal からコピーした**サインアウト URL** の値を貼り付けます。

    f. 次の入力を行います。 

    * **[TargetedID]\(ターゲット ID\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。
     
    * **[First name]\(名\)** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。
    
    * **[Last name]\(姓\)** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。
    
    * **[Email]\(電子メール\)** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。
    
1. **[Save]** をクリックします。
 
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/talentlms-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/talentlms-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/talentlms-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/talentlms-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-talentlms-test-user"></a>TalentLMS テスト ユーザーの作成

Azure AD ユーザーが TalentLMS にログインできるようにするには、そのユーザーを TalentLMS にプロビジョニングする必要があります。 TalentLMS の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **TalentLMS** テナントにログインします。

1. **[Users]** をクリックし、**[Add User]** をクリックします。

1. **[Add user]** ダイアログ ページで、次の手順に従います。
   
    ![ユーザーの追加](./media/talentlms-tutorial/IC777299.png "Add User")  

    a. **[First name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    b. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。
 
    c. **[Email Address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレス (例: **brittasimon\@contoso.com**) を入力します。

    d. **[ユーザーの追加]** をクリックします。

>[!NOTE]
>TalentLMS から提供されている他の TalentLMS ユーザー アカウント作成ツールまたは API を使用して、AAD ユーザー アカウントをプロビジョニングできます。
 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に TalentLMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**TalentLMS に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[TalentLMS]** を選択します。

    ![Configure single sign-on](./media/talentlms-tutorial/tutorial_talentlms_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストすることです。

アクセス パネルで TalentLMS のタイルをクリックすると、自動的に TalentLMS アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/talentlms-tutorial/tutorial_general_01.png
[2]: ./media/talentlms-tutorial/tutorial_general_02.png
[3]: ./media/talentlms-tutorial/tutorial_general_03.png
[4]: ./media/talentlms-tutorial/tutorial_general_04.png

[100]: ./media/talentlms-tutorial/tutorial_general_100.png

[200]: ./media/talentlms-tutorial/tutorial_general_200.png
[201]: ./media/talentlms-tutorial/tutorial_general_201.png
[202]: ./media/talentlms-tutorial/tutorial_general_202.png
[203]: ./media/talentlms-tutorial/tutorial_general_203.png

