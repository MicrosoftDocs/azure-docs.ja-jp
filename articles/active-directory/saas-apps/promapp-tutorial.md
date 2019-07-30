---
title: 'チュートリアル: Azure Active Directory と Promapp の統合 | Microsoft Docs'
description: このチュートリアルでは、Azure Active Directory と Promapp の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 418d0601-6e7a-4997-a683-73fa30a2cfb5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 2ddb8777a6470c0e739545e71867a694022d1723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67093598"
---
# <a name="tutorial-azure-active-directory-integration-with-promapp"></a>チュートリアル:Azure Active Directory と Promapp の統合

このチュートリアルでは、Promapp と Azure Active Directory (Azure AD) を統合する方法について説明します。
この統合には、次の利点があります。

* Azure AD を使用して、Promapp にアクセスできるユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Promapp に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure ポータル) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

Promapp と Azure AD の統合を構成するには、以下が必要です。

* Azure AD サブスクリプション。 Azure AD 環境がない場合は、[1 か月の試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。
* シングル サインオンが有効な Promapp サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Promapp では、SP Initiated SSO と IdP Initiated SSO がサポートされます。

* Promapp では、Just In Time ユーザー プロビジョニングがサポートされます。

## <a name="add-promapp-from-the-gallery"></a>ギャラリーから Promapp を追加する

Azure AD への Promapp の統合を設定するには、ギャラリーからマネージド SaaS アプリの一覧に Promapp を追加する必要があります。

1. [Azure portal](https://portal.azure.com) の左側のウィンドウで、 **[Azure Active Directory]** を選択します。

    ![[Azure Active Directory] を選択します。](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に移動します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. アプリケーションを追加するには、ウィンドウの上部の **[新しいアプリケーション]** を選択します。

    ![[新しいアプリケーション] を選択する](common/add-new-app.png)

4. 検索ボックスに「**Promapp**」と入力します。 検索結果で **[Promapp]** を選択し、 **[追加]** を選択します。

     ![[検索結果]](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、Britta Simon という名前のテスト ユーザーを使用して、Promapp に対する Azure AD シングル サインオンを構成およびテストします。
シングル サインオンを有効にするには、Azure AD ユーザーと Promapp の対応するユーザーの間で、関係を確立する必要があります。

Promapp に対する Azure AD シングル サインオンを構成およびテストするには、以下の手順を完了する必要があります。

1. **[Azure AD のシングル サインオンを構成](#configure-azure-ad-single-sign-on)** して、この機能をユーザーに対して有効にします。
2. アプリケーション側で **[Promapp シングル サインオンを構成](#configure-promapp-single-sign-on)** します。
3. Azure AD のシングル サインオンをテストするための **[Azure AD テスト ユーザーを作成](#create-an-azure-ad-test-user)** します。
4. **[Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)** て、Azure AD のシングル サインオンをそのユーザーに対して有効にします。
5. **[シングル サインオンをテスト](#test-single-sign-on)** して、この構成が機能することを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal で Azure AD のシングル サインオンを有効にします。

Promapp に対する Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の Promapp アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] の選択](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン方式の選択](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**編集**アイコンを選択して **[基本的な SAML 構成]** ダイアログ ボックスを開きます。

    ![[編集] アイコン](common/edit-urls.png)

4. **[基本的な SAML 構成]** ダイアログ ボックスで、アプリケーションを IDP 開始モードで構成する場合は、次の手順に従います。

    ![[基本的な SAML 構成] ダイアログ ボックス](common/idp-intiated.png)

    1. **[識別子]** ボックスに、次のパターンで URL を入力します。

       | |
        |--|
        | `https://go.promapp.com/TENANTNAME/`|
        | `https://au.promapp.com/TENANTNAME/`|
        | `https://us.promapp.com/TENANTNAME/`|
        | `https://eu.promapp.com/TENANTNAME/`|
        | `https://ca.promapp.com/TENANTNAME/`|
        |   |

       > [!NOTE]
       > Promapp と Azure AD の統合は、現在、サービスによって開始される認証向けにのみ構成されています。 (つまり、Promapp URL に移動することで、認証プロセスが開始されます)。ただし、 **[応答 URL]** フィールドは必須です。

    1. **[応答 URL]** ボックスに、次のパターンで URL を入力します。

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

5. アプリケーションを SP-initiated モードで構成する場合は、 **[追加の URL を設定します]** を選択します。 **[サイン オン URL]** ボックスに、次のパターンの URL を入力します。

      `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate`

    ![[Promapp ドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

   

    > [!NOTE]
    > これらの値はプレースホルダーです。 実際の識別子、応答 URL、およびサインオン URL を使用する必要があります。 この値を取得するには、[Promapp サポート チーム](https://www.promapp.com/about-us/contact-us/)に問い合わせてください。 また、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、要件に従って **[証明書 (Base64)]** の横にある **[ダウンロード]** リンクを選択し、証明書をコンピューターに保存します。

    ![証明書のダウンロード リンク](common/certificatebase64.png)

7. **[Set up Promapp]\(Promapp の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL をコピーする](common/copy-configuration-urls.png)

    1. **[ログイン URL]** 。

    1. **[Azure AD 識別子]** 。

    1. **[ログアウト URL]** 。

### <a name="configure-promapp-single-sign-on"></a>Promapp シングル サインオンの構成

1. Promapp 企業サイトに管理者としてサインインします。

2. ウィンドウ上部のメニューで **[Admin]\(管理者\)** を選択します。
   
    ![[Admin]\(管理者\) の選択][12]

3. **[Configure]\(構成\)** を選択します。
   
    ![[Configure]\(構成\) の選択][13]

4. **[Security]\(セキュリティ\)** ダイアログ ボックスで、次の手順を実行します。
   
    ![[Security]\(セキュリティ\) ダイアログ ボックス][14]
    
    1. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。
    
    1. **[SSO - Single Sign-on Mode]\(SSO - シングル サインオン モード\)** の一覧で **[Optional]\(省略可能\)** を選択します。 **[保存]** を選択します。

       > [!NOTE]
       > [Optional]\(省略可能\) モードはテスト目的専用です。 構成が完了したら、 **[SSO - Single Sign-on Mode]\(SSO - シングル サインオン モード\)** の一覧で **[Required]\(必須\)** を選択して、すべてのユーザーに強制的に Azure AD による認証が実施されるようにします。

    1. メモ帳で、前のセクションでダウンロードした証明書を開きます。 証明書の内容の、最初の行 ( **-----BEGIN CERTIFICATE-----** ) と最後の行 ( **-----END CERTIFICATE-----** ) 以外の部分をコピーします。 証明書の内容を **[SSO-x.509 Certificate]\(SSO-x.509 証明書\)** ボックスに貼り付けてから、 **[Save]\(保存\)** を選択します。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon という名前のテスト ユーザーを作成します。

1. Azure portal で、左側のウィンドウの **[Azure Active Directory]** を選択し、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[すべてのユーザー] を選択する](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] を選択する](common/new-user.png)

3. **[ユーザー]** ダイアログ ボックスで、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    1. **[名前]** ボックスに「**BrittaSimon**」と入力します。
  
    1. **[ユーザー名]** ボックスに、「**BrittaSimon@\<yourcompanydomain>.\<extension>** 」と入力します。 (例: BrittaSimon@contoso.com)。

    1. **[パスワードを表示]** を選択し、 **[パスワード]** ボックス内の値を書き留めます。

    1. **作成** を選択します。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に Promapp へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[Promapp]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Promapp]** を選択します。

    ![アプリケーションの一覧](common/all-applications.png)

3. 左側のウィンドウで **[ユーザーとグループ]** を選択します。

    ![[ユーザーとグループ] の選択](common/users-groups-blade.png)

4. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] を選択する](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 画面の下部にある **[選択]** ボタンをクリックします。

7. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="just-in-time-user-provisioning"></a>Just-In-Time ユーザー プロビジョニング

Promapp では、Just In Time ユーザー プロビジョニングがサポートされます。 この機能は、既定で有効になっています。 Promapp にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

ここで、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストする必要があります。

アクセス パネル上で [Promapp] タイルを選択すると、SSO を設定した Promapp インスタンスに自動的にサインインします。 アクセス パネルの詳細については、「[マイ アプリ ポータルでアプリにアクセスして使用する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png
