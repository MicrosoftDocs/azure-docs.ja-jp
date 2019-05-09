---
title: チュートリアル:Azure Active Directory と LinkedIn Elevate の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Elevate の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ca8e537f261b59fb4e069d47d24e21abbdeca46
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202012"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>チュートリアル:Azure Active Directory と LinkedIn Elevate の統合

このチュートリアルでは、LinkedIn Elevate と Azure Active Directory (Azure AD) を統合する方法について説明します。

LinkedIn Elevate と Azure AD の統合には、次の利点があります。

- LinkedIn Elevate にアクセスする Azure AD ユーザーを制御できます
- ユーザーが自分の Azure AD アカウントで自動的に LinkedIn Elevate にサインオン (シングル サインオン) できるようにします
- 1 つの中央サイト (Microsoft Azure 管理ポータル) でアカウントを管理できます

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

LinkedIn Elevate と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- LinkedIn Elevate でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、 [こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます。

## <a name="scenario-description"></a>シナリオの説明
このチュートリアルでは、テスト環境で Azure AD のシングル サインオンをテストします。
このチュートリアルで説明するシナリオは、主に次の 2 つの要素で構成されています。

1. ギャラリーからの LinkedIn Elevate の追加
1. Azure AD シングル サインオンの構成とテスト

## <a name="adding-linkedin-elevate-from-the-gallery"></a>ギャラリーからの LinkedIn Elevate の追加
Azure AD への LinkedIn Elevate の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Elevate を追加する必要があります。

**ギャラリーから LinkedIn Elevate を追加するには、次の手順に従います。**

1. **[Microsoft Azure 管理ポータル](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Active Directory][1]

1. **[エンタープライズ アプリケーション]** に移動します。 次に、**[すべてのアプリケーション]** に移動します。

    ![[アプリケーション]][2]

1. ダイアログの上部にある **[追加]** をクリックします。

    ![[アプリケーション]][3]

1. 検索ボックスに、「**LinkedIn Elevate**」と入力します。 結果パネルで、**[LinkedIn Elevate]** をクリックしてアプリケーションを追加します。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_000.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト
このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、LinkedIn Elevate で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する LinkedIn Elevate ユーザーが Azure AD で認識されている必要があります。 言い換えると、Azure AD ユーザーと LinkedIn Elevate の関連ユーザーの間で、リンク関係が確立されている必要があります。

このリンク関係を確立するには、Azure AD の **[ユーザー名]** の値を LinkedIn Elevate の **[Username (ユーザー名)]** の値として割り当てます。

LinkedIn Elevate で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configuring-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
1. **[Azure AD のテスト ユーザーの作成](#creating-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[LinkedIn Elevate のテスト ユーザーの作成](#creating-a-linkedin-elevate-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
1. **[Azure AD テスト ユーザーの割り当て](#assigning-the-azure-ad-test-user)** - Britta Simon が Azure AD のシングル サインオンを使用できるようにします。
1. **[シングル サインオンのテスト](#testing-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Microsoft Azure 管理ポータルで Azure AD のシングル サインオンを有効にして、LinkedIn Elevate アプリケーションでシングル サインオンを構成します。

**LinkedIn Elevate で Azure AD シングル サインオンを構成するには、次の手順に従います。**

1. Microsoft Azure 管理ポータルの **LinkedIn Elevate** アプリケーション統合ページで、**[シングル サインオン]** をクリックします。

    ![Configure single sign-on][4]

1. **[シングル サインオン]** ダイアログで、**[モード]** として **[SAML ベースのサインオン]** を選択し、シングル サインオンを有効にします。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial-linkedin_01.png)

1. 別の Web ブラウザーのウィンドウで、管理者として LinkedIn Elevate テナントにサインオンします。

1. **[Account Center (アカウント センター)]** で、**[Settings (設定)]** の下の **[Global Settings (グローバル設定)]** をクリックします。 また、ドロップダウン リストから **[Elevate - Elevate AAD Test (Elevate - Elevate AAD テスト)]** を選択します。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. **[OR Click Here to load and copy individual fields from the form (または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする)]** をクリックし、**[エンティティ ID]** と **[Assertion Consumer Access (ACS) Url (Assertion Consumer Access (ACS) URL)]** をコピーします

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

1. Azure Portal の **[LinkedIn Elevate Domain and URLs (LinkedIn Elevate のドメインと URL)]** で、SSO を **[IdP Initiated (IdP 開始)]** モードで構成する場合は、次の手順を実行します

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_01.png)

    a. **[識別子]** テキストボックスに、LinkedIn ポータルからコピーした**エンティティ ID** を入力します 

    b. **[応答 URL]** テキストボックスに、LinkedIn ポータルからコピーした **Assertion Consumer Access (ACS) URL** を入力します

1. SSO を **[SP Initiated (SP 開始)]** で構成する場合は、構成セクションの [詳細な URL 設定の表示] オプションをクリックし、次のパターンでサインオン URL を構成します。

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>` 

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_signon_02.png) 

1. LinkedIn Elevate アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットはその例です。 **[ユーザー識別子]** の既定値は **user.userprincipalname** ですが、LinkedIn Elevate はこれがユーザーの電子メール アドレスにマップされることを想定します。 そのため、一覧の **user.mail** 属性を使用するか、組織構成に基づいて適切な属性値を使用できます。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/updateusermail.png)

1. **[ユーザー属性]** セクションで、**[その他のすべてのユーザー属性を表示および編集する]** をクリックし、属性を設定します。 **department** という別の要求に追加する必要があり、値を **user.department** にマップする必要があります。

    | 属性名 | 属性値 |
    | --- | --- |
    | department| user.department |

      ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/userattribute.png)

      a. [属性の追加] をクリックして属性の詳細ページを開き、以下のように department 属性を追加します。

      ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/adduserattribute.png)

      b. **[OK]** をクリックして属性を保存します。

      c. 属性 **emailaddress** の名前を **email** に変更します。

1. **[SAML 署名証明書]** セクションで、**[メタデータ XML]** をクリックし、コンピューターに XML ファイルを保存します。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_certificate.png) 

1. **[Save]** をクリックします。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_general_400.png)

1. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 [Upload XML file (XML ファイルのアップロード)] オプションをクリックして、Azure Portal からダウンロードした XML ファイルをアップロードします。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. **[On (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[未接続]** から **[接続済み]** に変更されます

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="creating-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成
このセクションの目的は、Microsoft Azure 管理ポータルで Britta Simon というテスト ユーザーを作成することです。

![Azure AD ユーザーの作成][100]

**Azure AD でテスト ユーザーを作成するには、次の手順に従います。**

1. **Microsoft Azure 管理ポータル**の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/create_aaduser_01.png) 

1. **[ユーザーとグループ]** に移動し、**[すべてのユーザー]** をクリックして、ユーザーの一覧を表示します。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/create_aaduser_02.png) 

1. ダイアログの上部にある **[追加]** をクリックして **[ユーザー]** ダイアログを開きます。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/create_aaduser_03.png) 

1. **[ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/create_aaduser_04.png) 

    a. **[名前]** ボックスに「**BrittaSimon**」と入力します。

    b. **[ユーザー名]** ボックスに BrittaSimon の**電子メール アドレス**を入力します。

    c. **[パスワードを表示]** を選択し、**[パスワード]** の値をメモします。

    d.[Tableau Server return URL]: Tableau Server ユーザーがアクセスする URL。 **Create** をクリックしてください。

### <a name="creating-a-linkedin-elevate-test-user"></a>LinkedIn Elevate テスト ユーザーの作成

LinkedIn Elevate アプリケーションでは、ジャストインタイムのユーザー プロビジョニングがサポートされています。認証後に、ユーザーが自動的に、アプリケーションに作成されます。 LinkedIn Elevate ポータルの管理者設定ページで、スイッチ **[Automatically Assign licenses (ライセンスを自動的に割り当てる)]** を切り替えて、ジャストインタイム プロビジョニングを有効にします。これにより、ユーザーにライセンスも割り当てられます。 LinkedIn Elevate は、　自動ユーザー プロビジョニングもまた、サポートしています。自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](linkedinelevate-provisioning-tutorial.md)をご覧ください。

   ![Azure AD のテスト ユーザーの作成](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に LinkedIn Elevate へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

![ユーザーの割り当て][200] 

**LinkedIn Elevate に Britta Simon を割り当てるには、次の手順に従います。**

1. Azure 管理ポータルでアプリケーション ビューを開き、ディレクトリ ビューに移動します。次に、**[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** をクリックします。

    ![ユーザーの割り当て][201]

1. アプリケーションの一覧で **[LinkedIn Elevate]** を選択します。

    ![Configure single sign-on](./media/linkedinelevate-tutorial/tutorial-linkedinElevate_0001.png) 

1. 左側のメニューで **[ユーザーとグループ]** をクリックします。

    ![ユーザーの割り当て][202] 

1. **[追加]** ボタンをクリックします。 次に、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![ユーザーの割り当て][203]

1. **[ユーザーとグループ]** ダイアログで、ユーザーの一覧から **[Britta Simon]** を選択します。

1. **[ユーザーとグループ]** ダイアログで **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで **[割り当て]** ボタンをクリックします。

### <a name="testing-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで LinkedIn Elevate タイルをクリックすると、Azure サインオン ページが表示され、サインオンに成功すると LinkedIn Elevate アプリケーションに入ります。

## <a name="additional-resources"></a>その他のリソース

* [チュートリアル:LinkedIn Elevate を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる](linkedinelevate-provisioning-tutorial.md)
* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [[ユーザー プロビジョニングの構成]](linkedinelevate-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/linkedinElevate-tutorial/tutorial_general_01.png
[2]: ./media/linkedinElevate-tutorial/tutorial_general_02.png
[3]: ./media/linkedinElevate-tutorial/tutorial_general_03.png
[4]: ./media/linkedinElevate-tutorial/tutorial_general_04.png

[100]: ./media/linkedinElevate-tutorial/tutorial_general_100.png

[200]: ./media/linkedinElevate-tutorial/tutorial_general_200.png
[201]: ./media/linkedinElevate-tutorial/tutorial_general_201.png
[202]: ./media/linkedinElevate-tutorial/tutorial_general_202.png
[203]: ./media/linkedinElevate-tutorial/tutorial_general_203.png
