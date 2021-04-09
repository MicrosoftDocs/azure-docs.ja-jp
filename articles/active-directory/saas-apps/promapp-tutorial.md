---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と Nintex Promapp の統合 | Microsoft Docs'
description: Azure Active Directory と Nintex Promapp の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: jeedes
ms.openlocfilehash: fc31195e7f544bdce7fe2f135a39cb9992875d0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92505886"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と Nintex Promapp の統合

このチュートリアルでは、Nintex Promapp と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Nintex Promapp を統合すると、次のことができます。

* Nintex Promapp にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Nintex Promapp に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Nintex Promapp でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Nintex Promapp では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Nintex Promapp では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-nintex-promapp-from-the-gallery"></a>ギャラリーから Nintex Promapp を追加する

Azure AD への Nintex Promapp の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Nintex Promapp を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Nintex Promapp**」と入力します。
1. 結果のパネルから **[Nintex Promapp]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-nintex-promapp"></a>Nintex Promapp の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Nintex Promapp に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Nintex Promapp の関連ユーザーとの間にリンク関係を確立する必要があります。

Nintex Promapp で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Nintex Promapp の SSO の構成](#configure-nintex-promapp-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Nintex Promapp のテスト ユーザーの作成](#create-nintex-promapp-test-user)** - Nintex Promapp で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Nintex Promapp** アプリケーション統合ページで、**[管理]** セクションを見つけて、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    1. **[識別子]** ボックスに、次のパターンで URL を入力します。

        ```https
        https://go.promapp.com/TENANTNAME/
        https://au.promapp.com/TENANTNAME/
        https://us.promapp.com/TENANTNAME/
        https://eu.promapp.com/TENANTNAME/
        https://ca.promapp.com/TENANTNAME/
        ```

       > [!NOTE]
       > Nintex Promapp と Azure AD の統合は、現在、サービスによって開始される認証向けにのみ構成されています  (つまり、Nintex Promapp URL に移動することで、認証プロセスが開始されます)。ただし、 **[応答 URL]** フィールドは必須です。

    1. **[応答 URL]** ボックスに、次のパターンで URL を入力します。

       `https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サイン オン URL]** ボックスに、`https://<DOMAINNAME>.promapp.com/TENANTNAME/saml/authenticate` というパターンの URL を入力します。

    > [!NOTE]
    > これらの値はプレースホルダーです。 実際の識別子、応答 URL、サインオン URL を使用する必要があります。 この値を取得するには、[Nintex Promapp サポート チーム](https://www.promapp.com/about-us/contact-us/)に問い合わせてください。 また、Azure portal の **[基本的な SAML 構成]** ダイアログ ボックスに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Nintex Promapp のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Nintex Promapp へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Nintex Promapp]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-nintex-promapp-sso"></a>Nintex Promapp の SSO の構成

1. Nintex Promapp 企業サイトに管理者としてサインインします。

2. ウィンドウ上部のメニューで **[Admin]\(管理者\)** を選択します。

    ![[Admin]\(管理者\) の選択][12]

3. **[Configure]\(構成\)** を選択します。

    ![[Configure]\(構成\) の選択][13]

4. **[Security]\(セキュリティ\)** ダイアログ ボックスで、次の手順を実行します。

    ![[Security]\(セキュリティ\) ダイアログ ボックス][14]

    1. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** を貼り付けます。

    1. **[SSO - Single Sign-on Mode]\(SSO - シングル サインオン モード\)** の一覧で **[Optional]\(省略可能\)** を選択します。 **[保存]** を選択します。

       > [!NOTE]
       > [Optional]\(省略可能\) モードはテスト目的専用です。 構成が完了したら、**[SSO - Single Sign-on Mode]\(SSO - シングル サインオン モード\)** の一覧で **[Required]\(必須\)** を選択して、すべてのユーザーに強制的に Azure AD による認証が実施されるようにします。

    1. メモ帳で、前のセクションでダウンロードした証明書を開きます。 証明書の内容の、最初の行 (**-----BEGIN CERTIFICATE-----**) と最後の行 (**-----END CERTIFICATE-----**) 以外の部分をコピーします。 証明書の内容を **[SSO-x.509 Certificate]\(SSO-x.509 証明書\)** ボックスに貼り付けてから、**[Save]\(保存\)** を選択します。

### <a name="create-nintex-promapp-test-user"></a>Nintex Promapp のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Nintex Promapp に作成します。 Nintex Promapp では、Just-In-Time ユーザー プロビジョニングがサポートされており、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Nintex Promapp にユーザーがまだ存在していない場合は、認証後に新しく作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Nintex Promapp] タイルをクリックすると、SSO を設定した Nintex Promapp に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Nintex Promapp を試す](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/promapp-tutorial/tutorial_promapp_05.png
[13]: ./media/promapp-tutorial/tutorial_promapp_06.png
[14]: ./media/promapp-tutorial/tutorial_promapp_07.png