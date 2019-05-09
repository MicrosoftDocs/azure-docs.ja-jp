---
title: チュートリアル:Azure Active Directory と SAP Business ByDesign の統合 | Microsoft Docs
description: Azure Active Directory と SAP Business ByDesign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9846fe8078403d064f226c121383fc191397d321
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201875"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>チュートリアル:Azure Active Directory と SAP Business ByDesign の統合

このチュートリアルでは、SAP Business ByDesign と Azure Active Directory (Azure AD) を統合する方法について説明します。

SAP Business ByDesign と Azure AD の統合には、次の利点があります。

- SAP Business ByDesign にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に SAP Business ByDesign にサインオン (シングル サインオン) できるようにします。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure AD と SAP Business ByDesign の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- SAP Business ByDesign でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。 このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーから SAP Business ByDesign を追加する
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>ギャラリーから SAP Business ByDesign を追加する
Azure AD への SAP Business ByDesign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Business ByDesign を追加する必要があります。

**ギャラリーから SAP Business ByDesign を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。 

    ![Azure Active Directory のボタン][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[エンタープライズ アプリケーション] ブレード][2]
    
1. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン][3]

1. 検索ボックスに「**SAP Business ByDesign**」と入力し、結果パネルで **[SAP Business ByDesign]** を選び、**[追加]** をクリックして、アプリケーションを追加します。

    ![結果一覧の SAP Business ByDesign](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、SAP Business ByDesign で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する SAP Business ByDesign ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと SAP Business ByDesign の関連ユーザーの間で、リンク関係が確立されている必要があります。

SAP Business ByDesign で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

SAP Business ByDesign で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[SAP Business ByDesign のテスト ユーザーの作成](#create-an-sap-business-bydesign-test-user)** - SAP Business ByDesign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure Portal で Azure AD のシングル サインオンを有効にして、SAP Business ByDesign アプリケーションでシングル サインオンを構成します。

**SAP Business ByDesign で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Azure Portal の **SAP Business ByDesign** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![シングル サインオン構成のリンク][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。
 
    ![[シングル サインオン] ダイアログ ボックス](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

1. **[SAP Business ByDesign のドメインと URL]** セクションで、次の手順に従います。

    ![[SAP Business ByDesign のドメインと URL] のシングル サインオン情報](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. **[サインオン URL]** ボックスに、`https://<servername>.sapbydesign.com` のパターンを使用して URL を入力します。

    b. **[識別子]** ボックスに、`https://<servername>.sapbydesign.com` の形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 これらの値を取得するには、[SAP Business ByDesign クライアント サポート チーム](https://www.sap.com/products/cloud-analytics.support.html)に連絡してください。

1. **[ユーザー属性]** セクションで、次の手順に従います。

    ![SAP Business ByDesign 属性セクション](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. **[ユーザー識別子]** リストで、**[ExtractMailPrefix()]** 関数を選択します。
    
    b. **[メール]** 一覧から、実装で使用するユーザー属性を選択します。 たとえば、一意のユーザー識別子として EmployeeID を使用し、その属性値を ExtensionAttribute2 に保存している場合、[user.extensionattribute2] を選択します。     

1. **[SAML 署名証明書]** セクションで、**[Metadata XML (メタデータ XML)]** をクリックし、コンピューターにメタデータ ファイルを保存します。

    ![証明書のダウンロードのリンク](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

1. **[保存]** ボタンをクリックします。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/sapbusinessbydesign-tutorial/tutorial_general_400.png)

1. **[SAP Business ByDesign 構成]** セクションで、**[SAP Business ByDesign の構成]** をクリックし、**[サインオンの構成]** ウィンドウを開きます。 **[クイック リファレンス]** セクションから **SAML シングル サインオン サービスの URL** をコピーします。

    ![SAP Business ByDesign 構成](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

1. アプリケーション用に構成された SSO を取得するには、次の手順を実行します。
   
    a. SAP Business ByDesign ポータルに管理者権限でサインオンします。
   
    b. **[Application and User Management Common Task (アプリケーションとユーザー管理の共通タスク)]** に移動し、**[ID プロバイダー]** タブをクリックします。
   
    c. **[New Identity Provider]** をクリックし、Azure Portal からダウンロードしたメタデータの XML ファイルを選択します。 メタデータをインポートすることによって、必要な署名証明書と暗号化証明書が自動的にアップロードされます。
   
    ![Configure single sign-on](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Assertion Consumer Service URL** を SAML 要求に追加するには、**[Include Assertion Consumer Service URL (Assertion Consumer Service URL を含める)]** を選択します。
   
    e. **[Activate Single Sign-On (シングル サインオンを有効にする)]** をクリックします。
   
    f. 変更を保存します。
   
    g. **[My System (自分のシステム)]** タブをクリックします。
   
    ![Configure single sign-on](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Azure Portal からコピーした **SAML シングル サインオン サービス URL** を **[Azure AD Single Sign On URL]** ボックスに貼り付けます。
   
    ![Configure single sign-on](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. ユーザー ID とパスワードでログオンするか、SSO でログオンするかを従業員が手動で選択できるかどうかを、**[Manual Identity Provider Selection (ID プロバイダーの手動選択)]** を選択して指定します。
   
    j. **[SSO URL]** セクションには、従業員がシステムへのログオンに使用する URL を指定します。 
    [URL Sent to Employee (従業員に送信する URL)] ボックスの一覧で、次のオプションを選択できます。
   
    **[Non-SSO URL (非 SSO URL)]**
   
    従業員に送信されるのは、システムの通常の URL のみです。 従業員のログオンに SSO は使用できず、パスワードまたは証明書を使用する必要があります。
   
    **[SSO URL (SSO の URL)]** 
   
    従業員に送信されるのは、SSO の URL のみです。 従業員は SSO を使用してログオンすることができます。 認証要求は IdP を介してリダイレクトされます。
   
    **[Automatic Selection (自動選択)]**
   
    SSO が有効ではない場合、システムの通常の URL が従業員に送信されます。 SSO が有効である場合は、従業員がパスワードを持っているかどうかがシステムによってチェックされます。 パスワードを持っていた場合は、SSO の URL と非 SSO の URL の両方が従業員に送信されます。 一方、従業員がパスワードを持っていない場合は、SSO の URL だけが従業員に送信されます。
   
    k. 変更を保存します。

> [!TIP]
> アプリのセットアップ中、[Azure Portal](https://portal.azure.com) 内で上記の手順の簡易版を確認できるようになりました。  **[Active Directory] の [エンタープライズ アプリケーション]** セクションからこのアプリを追加した後、**[シングル サインオン]** タブをクリックし、一番下の **[構成]** セクションから組み込みドキュメントにアクセスするだけです。 埋め込みドキュメント機能の詳細については、[Azure AD の埋め込みドキュメント]( https://go.microsoft.com/fwlink/?linkid=845985)に関するページを参照してください。
> 

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

   ![Azure AD のテスト ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. Azure Portal の左側のウィンドウで、**Azure Active Directory** のボタンをクリックします。

    ![Azure Active Directory のボタン](./media/sapbusinessbydesign-tutorial/create_aaduser_01.png)

1. ユーザーの一覧を表示するには、**[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックします。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](./media/sapbusinessbydesign-tutorial/create_aaduser_02.png)

1. **[ユーザー]** ダイアログ ボックスを開くには、**[すべてのユーザー]** ダイアログ ボックスの上部にある **[追加]** をクリックしてきます。

    ![[追加] ボタン](./media/sapbusinessbydesign-tutorial/create_aaduser_03.png)

1. **[ユーザー]** ダイアログ ボックスで、次の手順に従います。

    ![[ユーザー] ダイアログ ボックス](./media/sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに、ユーザーである Britta Simon の電子メール アドレスを入力します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、**[パスワード]** ボックスに表示された値を書き留めます。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。
 
### <a name="create-an-sap-business-bydesign-test-user"></a>SAP Business ByDesign のテスト ユーザーの作成

このセクションでは、SAP Business ByDesign で Britta Simon というユーザーを作成します。 SAP Business ByDesign プラットフォームにユーザーを追加する方法についてご不明な点がある場合は、[SAP Business ByDesign クライアント サポート チーム](https://www.sap.com/products/cloud-analytics.support.html)にお問い合わせください。 

> [!NOTE]
> NameID 値は必ず、SAP Business ByDesign プラットフォームのユーザー名フィールドと一致させてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SAP Business ByDesign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザー ロールを割り当てる][200] 

**SAP Business ByDesign に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure Portal でアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201] 

1. アプリケーションの一覧で **[SAP Business ByDesign]** を選択します。

    ![アプリケーションの一覧の SAP Business ByDesign のリンク](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![[ユーザーとグループ] リンク][202]

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。
    
### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SAP Business ByDesign] タイルをクリックすると、自動的に SAP Business ByDesign アプリケーションにサインオンします。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/sapbusinessbydesign-tutorial/tutorial_general_203.png

