---
title: チュートリアル:Azure Active Directory と Zscaler Private Access (ZPA) の統合 | Microsoft Docs
description: Azure Active Directory と Zscaler Private Access (ZPA) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b6471b6bd634c7fe3053fc7748eb925c049c17c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179568"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-zpa"></a>チュートリアル:Azure Active Directory と Zscaler Private Access (ZPA) の統合

このチュートリアルでは、Zscaler Private Access (ZPA) と Azure Active Directory (Azure AD) を統合する方法について説明します。

Zscaler Private Access (ZPA) と Azure AD の統合には、次の利点があります。

- Zscaler Private Access (ZPA) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Zscaler Private Access (ZPA) にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

Zscaler Private Access (ZPA) と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- Zscaler Private Access (ZPA) でのシングル サインオンが有効なサブスクリプション


> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。


このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。


## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの Zscaler Private Access (ZPA) の追加
1. Azure AD シングル サインオンの構成とテスト


## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>ギャラリーからの Zscaler Private Access (ZPA) の追加
Azure AD への Zscaler Private Access (ZPA) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zscaler Private Access (ZPA) を追加する必要があります。

**ギャラリーから Zscaler Private Access (ZPA) を追加するには、次の手順を実行します。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]
    
1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**Zscaler Private Access (ZPA)**」と入力します。

    ![Azure AD のテスト ユーザーの作成](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_001.png)

1. 結果ウィンドウで **[Zscaler Private Access (ZPA)]** を選択し、**[追加]** をクリックして、アプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Zscaler Private Access (ZPA) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Zscaler Private Access (ZPA) ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと Zscaler Private Access (ZPA) の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンクの関係を確立するには、Azure AD の **[ユーザー名]** の値を、Zscaler Private Access (ZPA) の **[Username]** の値として割り当てます。

Zscaler Private Access (ZPA) で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **Zscaler Private Access (ZPA) のテスト ユーザーの作成** - Zscaler Private Access (ZPA) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にし、Zscaler Private Access (ZPA) アプリケーションでシングル サインオンを構成します。

**Zscaler Private Access (ZPA) で Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. Microsoft Azure 管理ポータルの **Zscaler Private Access (ZPA)** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_general_300.png)
    
1. **[Zscaler Private Access (ZPA) のドメインと URL]** セクションで、次の手順を実行します。
    
    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_01.png)

    a. **[サインオン URL]** ボックスに、`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、「`https://samlsp.private.zscaler.com/auth/metadata`」と入力します。

    > [!NOTE] 
    > これは実際の値ではないので注意してください。 実際のサインオン URL と識別子でこれらの値を更新する必要があります。 ここでは、識別子に一意の URL を使用することをお勧めします。 これらの値を取得するには、[Zscaler Private Access (ZPA) サポート チーム](https://help.zscaler.com/zpa-submit-ticket)に連絡してください。

1. **[SAML 署名証明書**] セクションで、**[新しい証明書の作成]** をクリックします。

    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_general_400.png)     

1. **[新しい証明書の作成]** ダイアログで、カレンダー アイコンをクリックし、**期限日**を選択します。 **[保存]** をクリックします。

    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_general_500.png)

1. **[SAML 署名証明書]** セクションで、**[Make new certificate active (新しい証明書を有効にする)]** をクリックし、**[保存]** をクリックします。

    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_02.png)

1. ポップアップ表示される **[Rollover certificate (ロール オーバー証明書)]** ウィンドウで、**[OK]** をクリックします。

    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_general_600.png)

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_03.png) 

1. 別の Web ブラウザー ウィンドウで、Zscaler Private Access (ZPA) 企業サイトに管理者としてログインします。

1. **[Administrator]** に移動し、**[Idp Configuration]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_04.png)

1. **[Idp Configuration]** セクションで、**[Add New IDP Configuration]** をクリックします。

    ![アプリ側でのシングル サインオンの構成](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_05.png)

1. **[New IDP Configuration]** セクションで、次の手順を実行します。

    ![アプリ側でのシングル サインオンの構成](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_06.png)

    a. **[Select File]** をクリックし、ダウンロードしたメタデータ ファイルをアップロードします。

    b. **[保存]** ボタンをクリックします。
    


### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/zscalerprivateaccess-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。
    
    ![Azure AD のテスト ユーザーの作成](./media/zscalerprivateaccess-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。
 
    ![Azure AD のテスト ユーザーの作成](./media/zscalerprivateaccess-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。
 
    ![Azure AD のテスト ユーザーの作成](./media/zscalerprivateaccess-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。 



### <a name="creating-a-zscaler-private-access-zpa-test-user"></a>Zscaler Private Access (ZPA) テスト ユーザーの作成

このセクションでは、Zscaler Private Access (ZPA) で Britta Simon というユーザーを作成します。 [Zscaler Private Access (ZPA) のサポート チーム](https://help.zscaler.com/zpa-submit-ticket)に問い合わせて、Zscaler Private Access (ZPA) プラットフォームでユーザーを追加します。


### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Zscaler Private Access (ZPA) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**Zscaler Private Access (ZPA) に Britta Simon を割り当てるには、次の手順を実行します。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で、**[Zscaler Private Access (ZPA)]** を選択します。

    ![Configure single sign-on](./media/zscalerprivateaccess-tutorial/tutorial_zscalerprivateaccess_50.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    


### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Zscaler Private Access (ZPA) のタイルをクリックすると、自動的に Zscaler Private Access (ZPA) アプリケーションにサインオンします。


## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccess-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccess-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccess-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccess-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccess-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccess-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccess-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccess-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccess-tutorial/tutorial_general_203.png
