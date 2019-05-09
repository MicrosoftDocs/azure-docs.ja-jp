---
title: チュートリアル:Azure Active Directory と Icertis Contract Management Platform の統合 | Microsoft Docs
description: Azure Active Directory と Icertis Contract Management Platform の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 6627e6dd-f559-4cd4-a509-f6d9a4961b49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5d83ab0c14da772551a8f33704e0e776be5ebb6
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171385"
---
# <a name="tutorial-azure-active-directory-integration-with-icertis-contract-management-platform"></a>チュートリアル:Azure Active Directory と Icertis Contract Management Platform の統合

このチュートリアルでは、Icertis Contract Management Platform と Azure Active Directory (Azure AD) を統合する方法について説明します。

Icertis Contract Management Platform と Azure AD の統合には、次の利点があります。

- Icertis Contract Management Platform にアクセスできるユーザーを Azure AD で制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Icertis Contract Management Platform にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Icertis Contract Management Platform と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Icertis Contract Management Platform でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Icertis Contract Management Platform の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-icertis-contract-management-platform-from-the-gallery"></a>ギャラリーからの Icertis Contract Management Platform の追加
Azure AD への Icertis Contract Management Platform の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Icertis Contract Management Platform を追加する必要があります。

**ギャラリーから Icertis Contract Management Platform を追加するには、次の手順を実行します。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「 **Icertis Contract Management Platform**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/icertisicm-tutorial/tutorial_icertisicm_search.png)

1. 結果ウィンドウで **Icertis Contract Management Platform** を選択し、**[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/icertisicm-tutorial/tutorial_icertisicm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Icertis Contract Management Platform で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Icertis Contract Management Platform ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Icertis Contract Management Platform 内の対応するユーザーの間で、リンク関係が確立されている必要があります。

Icertis Contract Management Platform で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Icertis Contract Management Platform で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Icertis Contract Management Platform テスト ユーザーの作成](#creating-an-icertis-contract-management-platform-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを Icertis Contract Management Platform で作成します。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、Icertis Contract Management Platform アプリケーションでシングル サインオンを構成します。

**Icertis Contract Management Platform との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Azure Portal の **Icertis Contract Management Platform** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/icertisicm-tutorial/tutorial_icertisicm_samlbase.png)

1. **[Icertis Contract Management Platform のドメインと URL]** セクションで、以下の手順を実行します。

    ![Configure single sign-on](./media/icertisicm-tutorial/tutorial_icertisicm_url.png)

    a. **[サインオン URL]** ボックスに、`https://<company name>.icertis.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<company name>.icertis.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 値を取得するには、[Icertis Contract Management Platform クライアント サポート チーム](https://www.icertis.com/company/contact/)に問い合わせてください。 

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/icertisicm-tutorial/tutorial_icertisicm_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![Configure single sign-on](./media/icertisicm-tutorial/tutorial_general_400.png)

1. **[Icertis Contract Management Platform 構成]** セクションで **[Icertis Contract Management Platform の構成]** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから、**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** をコピーします。

    ![Configure single sign-on](./media/icertisicm-tutorial/tutorial_icertisicm_configure.png) 

1. **Icertis Contract Management Platform** 側にシングル サインオンを構成するには、ダウンロードした**メタデータ XML** および**サインアウト URL、SAML エンティティ ID、SAML シングル サインオン サービス URL** を、[Icertis Contract Management Platform サポート チーム](https://www.icertis.com/company/contact/)に送る必要があります。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Azure Portal** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/icertisicm-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/icertisicm-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして、**[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/icertisicm-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/icertisicm-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="creating-an-icertis-contract-management-platform-test-user"></a>Icertis Contract Management Platform テスト ユーザーの作成

このセクションでは、Icertis Contract Management Platform で Britta Simon というユーザーを作成します。 [Icertis Contract Management Platform サポート チーム](https://www.icertis.com/company/contact/)と協力して、Icertis Contract Management Platform プラットフォームにユーザーを追加してください。

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Icertis Contract Management Platform へのアクセス権を付与することによって、Britta Simon が Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Icertis Contract Management Platform に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[Icertis Contract Management Platform]** を選択します。

    ![Configure single sign-on](./media/icertisicm-tutorial/tutorial_icertisicm_app.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションの目的は、アクセス パネルを使用して Azure AD の SSO 構成をテストすることです。

アクセス パネルで [Icertis Contract Management Platform] タイルをクリックすると、自動的に Icertis Contract Management Platform アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/icertisicm-tutorial/tutorial_general_01.png
[2]: ./media/icertisicm-tutorial/tutorial_general_02.png
[3]: ./media/icertisicm-tutorial/tutorial_general_03.png
[4]: ./media/icertisicm-tutorial/tutorial_general_04.png

[100]: ./media/icertisicm-tutorial/tutorial_general_100.png

[200]: ./media/icertisicm-tutorial/tutorial_general_200.png
[201]: ./media/icertisicm-tutorial/tutorial_general_201.png
[202]: ./media/icertisicm-tutorial/tutorial_general_202.png
[203]: ./media/icertisicm-tutorial/tutorial_general_203.png

