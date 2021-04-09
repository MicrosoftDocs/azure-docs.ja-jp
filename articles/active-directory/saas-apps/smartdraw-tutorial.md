---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SmartDraw の統合 | Microsoft Docs
description: Azure Active Directory と SmartDraw の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: b4b9aa16dfc474575bd522238f06fe487508e7e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515850"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SmartDraw の統合

このチュートリアルでは、SmartDraw と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SmartDraw を統合すると、次のことができます。

* SmartDraw にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SmartDraw に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SmartDraw でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SmartDraw では、**SP と IDP** によって開始される SSO がサポートされます
* SmartDraw では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-smartdraw-from-the-gallery"></a>ギャラリーから SmartDraw を追加する

Azure AD への SmartDraw の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SmartDraw を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SmartDraw**」と入力します。
1. 結果のパネルから **[SmartDraw]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>SmartDraw の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SmartDraw に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと SmartDraw の関連ユーザーの間で、リンク関係が確立されている必要があります。

SmartDraw で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SmartDraw の SSO の構成](#configure-smartdraw-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[SmartDraw のテスト ユーザーの作成](#create-smartdraw-test-user)** - SmartDraw で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SmartDraw** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. アプリは Azure と事前に統合済みであるため、 **[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://cloud.smartdraw.com/sso/saml/login/<domain>` という形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 サインオン URL の値は、後で実際のサインオン URL に置き換えることになります。実際の値については後で説明します。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[保存]** をクリックします。

1. SmartDraw アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、SmartDraw アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | FirstName | User.givenname |
    | LastName | User.surname |
    | Email | User.mail |
    | グループ | user.groups |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[SmartDraw の設定]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に SmartDraw へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SmartDraw]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-smartdraw-sso"></a>SmartDraw の SSO を構成する

1. SmartDraw 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[SmartDraw のセットアップ]** をクリックすると、SmartDraw アプリケーションに移動します。 そこから、管理者の資格情報を入力して SmartDraw にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. SmartDraw を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として SmartDraw 企業サイトにサインインして、次の手順を実行します。

1. [Manage your SmartDraw License]\(SmartDraw ライセンスの管理\) の下にある **[シングルサインオン]** をクリックします。

    ![[Manage your SmartDraw License]\(SmartDraw ライセンスの管理\) ダイアログ ボックスのスクリーンショット。ここでシングル サインオンを選択することができます。](./media/smartdraw-tutorial/configure01.png)

1. [Configuration]\(構成\) ページで、次の手順を実行します。

    ![[Configuration]\(構成\) ページを示すスクリーンショット。ここで、説明されている値を入力できます。](./media/smartdraw-tutorial/configure02.png)

    a. **[Your Domain (like acme.com)]\(ご使用のドメイン (acme.com など)\)** ボックスに、ご使用のドメインを入力します。

    b. インスタンスの **[Your SP Initiated Login Url will be]\(SP 開始ログイン URL\)** をコピーし、Azure portal の **[基本的な SAML 構成]** の [サインオン URL] ボックスに貼り付けます。

    c. **[Security Groups to Allow SmartDraw Access]\(SmartDraw アクセスを許可するセキュリティ グループ\)** ボックスに、「**Everyone**」と入力します。

    d. **[Your SAML Issuer Url]\(SAML 発行者の URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    e. Azure portal からダウンロードしたメタデータ XML ファイルをメモ帳で開き、その内容をコピーして **[Your SAML MetaData]\(SAML メタデータ\)** ボックスに貼り付けます。

    f. **[構成の保存]** をクリックします。 

### <a name="create-smartdraw-test-user"></a>SmartDraw テスト ユーザーを作成する

このセクションでは、B.Simon というユーザーを SmartDraw に作成します。 SmartDraw では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 SmartDraw にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SmartDraw] タイルをクリックすると、SSO を設定した SmartDraw に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で SmartDraw を試す](https://aad.portal.azure.com/)