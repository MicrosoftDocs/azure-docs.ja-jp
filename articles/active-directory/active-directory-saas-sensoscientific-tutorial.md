---
title: "チュートリアル: Azure Active Directory と SensoScientific Wireless Temperature Monitoring System の統合 | Microsoft Docs"
description: "Azure Active Directory と SensoScientific Wireless Temperature Monitoring System の間でシングル サインオンを構成する方法について説明します。"
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeedes
ms.openlocfilehash: fa6242cf7f9559ca394ffde2e5e734cb935b03dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>チュートリアル: Azure Active Directory と SensoScientific Wireless Temperature Monitoring System の統合

このチュートリアルでは、SensoScientific Wireless Temperature Monitoring System と Azure Active Directory (Azure AD) を統合する方法について説明します。

SensoScientific Wireless Temperature Monitoring System と Azure AD の統合には、次の利点があります。

- SensoScientific Wireless Temperature Monitoring System にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SensoScientific Wireless Temperature Monitoring System にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

SensoScientific Wireless Temperature Monitoring System と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SensoScientific Wireless Temperature Monitoring System でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの SensoScientific Wireless Temperature Monitoring System の追加
2. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>ギャラリーからの SensoScientific Wireless Temperature Monitoring System の追加
Azure AD への SensoScientific Wireless Temperature Monitoring System の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SensoScientific Wireless Temperature Monitoring System を追加する必要があります。

**ギャラリーから SensoScientific Wireless Temperature Monitoring System を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

2. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![アプリケーション][2]
    
3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![アプリケーション][3]

4. 検索ボックスに「**SensoScientific Wireless Temperature Monitoring System**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_search.png)

5. 結果ウィンドウで **[SensoScientific Wireless Temperature Monitoring System]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SensoScientific Wireless Temperature Monitoring System で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SensoScientific Wireless Temperature Monitoring System ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SensoScientific Wireless Temperature Monitoring System の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を SensoScientific Wireless Temperature Monitoring System の **[Username (ユーザー名)]** の値として割り当てます。

SensoScientific Wireless Temperature Monitoring System で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
3. **[SensoScientific Wireless Temperature Monitoring System テスト ユーザーの作成](#creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user)** - SensoScientific Wireless Temperature Monitoring System で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
4. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にして、SensoScientific Wireless Temperature Monitoring System アプリケーションにシングル サインオンを構成します。

**SensoScientific Wireless Temperature Monitoring System で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure ポータルの **SensoScientific Wireless Temperature Monitoring System** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![[シングル サインオンの構成]][4]

2. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオンの構成]](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_samlbase.png)

3. アプリは Azure と事前に統合済みであるため、**[SensoScientific Wireless Temperature Monitoring System のドメインと URL]** セクションで特に手順を実施する必要はありません。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_url.png)

4. **[SAML 署名証明書]** セクションで、**[証明書 (Base64)]** をクリックし、コンピューターに証明書ファイルを保存します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_certificate.png) 

5. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_400.png)

6. **[SensoScientific Wireless Temperature Monitoring System の構成]** セクションで、**[SensoScientific Wireless Temperature Monitoring System を構成する]** をクリックして、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインオン URL、SAML エンティティ ID**、および **SAML シングル サインオン サービス URL** をコピーします。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_configure.png) 

7. 管理者として SensoScientific Wireless Temperature Monitoring System アプリケーションにサインオンします。

8. 上部にあるナビゲーション メニューの **[Configuration (構成)]** をクリックし、**[Configure (構成)]** の **[Single Sign On (シングル サインオン)]** に移動して [Single Sign On Settings (シングル サインオン設定)] を開きます。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png) 

9. **[Single Sign On Settings (シングル サインオン設定)]** フォームで、次の手順を実行します。
 
    a. **[Issuer Name (発行者名)]** として [Azure AD] を選択します。
    
    b. [Issuer URL (発行者 URL)] ボックスに、Azure ポータルからコピーした **SAML エンティティ ID** を 貼り付けます。
    
    c. [Single Sign-On Service URL (シングル サインオン サービス URL )] ボックスに、Azure ポータルからコピーした **SAML シングル サインオン サービス URL** を貼り付けます。

    d. [Sign-Out URL (シングル サインアウト サービス URL )] ボックスに、Azure ポータルからコピーした**サインアウト URL** を貼り付けます。

    e. Azure ポータルからダウンロードした証明書を参照してアップロードします。
    
    f. [ **Save**] をクリックします。
  
> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 組み込みドキュメント機能の詳細については、[Azure AD の組み込みドキュメント](https://go.microsoft.com/fwlink/?linkid=845985)に関する記事をご覧ください。

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_01.png) 

2. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_02.png) 

3. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_03.png) 

4. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/active-directory-saas-sensoscientific-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d. **Create** をクリックしてください。
 
### <a name="creating-a-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>SensoScientific Wireless Temperature Monitoring System テスト ユーザーの作成

Azure AD ユーザーが SensoScientific Wireless Temperature Monitoring System にログインできるようにするには、ユーザーを SensoScientific Wireless Temperature Monitoring System にプロビジョニングする必要があります。 [SensoScientific Wireless Temperature Monitoring System サポート チーム](https://www.sensoscientific.com/contact-us/)と連携して、SensoScientific Wireless Temperature Monitoring System プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。 

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SensoScientific Wireless Temperature Monitoring System へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Britta Simon を SensoScientific Wireless Temperature Monitoring System に割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

2. アプリケーション リストから「**SensoScientific Wireless Temperature Monitoring SystemScaleX Enterprise**」を選択します。

    ![[シングル サインオンの構成]](./media/active-directory-saas-sensoscientific-tutorial/tutorial_sensoscientificwtms_app.png) 

3. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

4. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

5. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

6. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

7. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。 アクセス パネルで [SensoScientific Wireless Temperature Monitoring System] タイルをクリックすると、自動的に SensoScientific Wireless Temperature Monitoring System アプリケーションにサインオンします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://msdn.microsoft.com/library/dn308586)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](active-directory-saas-tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sensoscientific-tutorial/tutorial_general_203.png

