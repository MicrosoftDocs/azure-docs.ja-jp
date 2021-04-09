---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と SKYSITE の統合 | Microsoft Docs
description: Azure Active Directory と SKYSITE の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 9d4413c47e40a611fb1559b32aac1e32a71d1998
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92515986"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-skysite"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と SKYSITE の統合

このチュートリアルでは、SKYSITE と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と SKYSITE を統合すると、次のことができます。

* SKYSITE にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SKYSITE に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* SKYSITE でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SKYSITE では、**IDP** Initiated SSO がサポートされます

* SKYSITE では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-skysite-from-the-gallery"></a>ギャラリーからの SKYSITE の追加

Azure AD への SKYSITE の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に SKYSITE を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SKYSITE**」と入力します。
1. 結果のパネルから **[SKYSITE]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-skysite"></a>SKYSITE の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、SKYSITE に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと SKYSITE の関連ユーザーとの間にリンク関係が確立されている必要があります。

SKYSITE に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SKYSITE の SSO の構成](#configure-skysite-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SKYSITE のテスト ユーザーの作成](#create-skysite-test-user)** - SKYSITE で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **SKYSITE** アプリケーション統合ページで、 **[プロパティ]** タブをクリックし、次の手順を実行します。 

    ![シングル サインオンのプロパティ](./media/skysite-tutorial/config05.png)

    * **[ユーザーのアクセス URL]** をコピーします。これを **Configure SKYSITE SSO (SKYSITE の SSO の構成)** セクションに貼り付ける必要がありますが、この操作については、このチュートリアルで後述します。

1. **SKYSITE** アプリケーション統合ページで、 **[シングル サインオン]** に移動します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは **IDP** 開始モードで事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. SKYSITE アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![このスクリーンショットは、[編集] アイコンが選択された状態の [User Attributes]\(ユーザー属性\) を示しています。](common/edit-attribute.png)

1. その他に、SKYSITE アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[グループ要求 (プレビュー)]** ダイアログの **[ユーザー属性とクレーム]** セクションで、次の手順を実行します。

    a. **[Groups returned in claim]\(要求で返されるグループ\)** の横にある **ペン** をクリックします。

    ![[新しい要求の追加] オプションが備わっている [ユーザー要求] のスクリーンショット。](./media/skysite-tutorial/config01.png)

    ![スクリーンショットは、説明されている値を入力できる [ユーザー要求の管理] ダイアログ ボックスを示しています。](./media/skysite-tutorial/config02.png)

    b. ラジオ ボタンのリストから **[すべてのグループ]** を選択します。

    c. **[グループ ID]** の **[ソース属性]** を選択します。

    d. **[保存]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[SKYSITE のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に SKYSITE へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SKYSITE]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-skysite-sso"></a>SKYSITE の SSO の構成

1. 新しい Web ブラウザー ウィンドウを開き、SKYSITE 企業サイトに管理者としてサインインして、次の手順を実行します。

4. ページの右上にある **[Settings]\(設定\)** をクリックし、 **[Account setting]\(アカウント設定\)** に移動します。

    ![[Settings]\(設定\) の [Account setting]\(アカウント設定\) が選択された画面のスクリーンショット。](./media/skysite-tutorial/config03.png)

5. **[Single sign on (SSO)]\(シングル サインオン (SSO)\)** タブで、次の手順を実行します。

    ![[Single sign on]\(シングル サインオン\) タブのスクリーンショット。ここで、説明されている値を入力できます。](./media/skysite-tutorial/config04.png)

    a. **[Identity Provider sign in URL]\(ID プロバイダー サインイン URL\)** ボックスに、Azure portal の **[プロパティ]** タブからコピーした **[ユーザーのアクセス URL]** の値を貼り付けます。

    b. **[Upload certificate]\(証明書のアップロード\)** をクリックして、Azure portal からダウンロードした Base64 でエンコードされた証明書をアップロードします。

    c. **[保存]** をクリックします。

### <a name="create-skysite-test-user"></a>SKYSITE のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを SKYSITE に作成します。 SKYSITE では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 SKYSITE にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SKYSITE] タイルをクリックすると、SSO を設定した SKYSITE に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で SKYSITE を試す](https://aad.portal.azure.com/)