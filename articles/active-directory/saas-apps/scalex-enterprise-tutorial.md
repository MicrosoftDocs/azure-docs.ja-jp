---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と ScaleX Enterprise の統合 | Microsoft Docs
description: Azure Active Directory と ScaleX Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/16/2019
ms.author: jeedes
ms.openlocfilehash: a2065286581b6e7de61e4d1a29382b98563cdbb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-scalex-enterprise"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と ScaleX Enterprise の統合

このチュートリアルでは、ScaleX Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。 ScaleX Enterprise と Azure AD を統合すると、次のことができます。

* ScaleX Enterprise にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して ScaleX Enterprise に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* ScaleX Enterprise でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* ScaleX Enterprise では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

## <a name="adding-scalex-enterprise-from-the-gallery"></a>ギャラリーからの ScaleX Enterprise の追加

Azure AD への ScaleX Enterprise の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に ScaleX Enterprise を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**ScaleX Enterprise**」と入力します。
1. 結果のパネルから **[ScaleX Enterprise]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-scalex-enterprise"></a>ScaleX Enterprise の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、ScaleX Enterprise に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと ScaleX Enterprise の関連ユーザーとの間にリンク関係を確立する必要があります。

ScaleX Enterprise に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ScaleX Enterprise の SSO の構成](#configure-scalex-enterprise-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[ScaleX Enterprise のテスト ユーザーの作成](#create-scalex-enterprise-test-user)** - ScaleX Enterprise で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **ScaleX Enterprise** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://platform.rescale.com/saml2/<company id>/` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://platform.rescale.com/saml2/<company id>/acs/` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://platform.rescale.com/saml2/<company id>/sso/` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[ScaleX Enterprise クライアント サポート チーム](https://info.rescale.com/contact_sales)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. ScaleX Enterprise アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**emailaddress** は **user.mail** にマップされています。 ScaleX Enterprise アプリケーションでは、**emailaddress** が **user.userprincipalname** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[ScaleX Enterprise のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に ScaleX Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[ScaleX Enterprise]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-scalex-enterprise-sso"></a>ScaleX Enterprise の SSO の構成

1. ScaleX Enterprise 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **[My Apps Secure Sign-in browser extension]\(My Apps Secure Sign-in ブラウザー拡張機能\)** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[ScaleX Enterprise のセットアップ]** をクリックすると、ScaleX Enterprise アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して ScaleX Enterprise にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. ScaleX Enterprise を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として ScaleX Enterprise 企業サイトにサインインして、次の手順を実行します。

1. 右上隅のメニューを選択し、 **[Contoso Administration (Contoso の管理)]** をクリックします。

    > [!NOTE]
    > Contoso は一例です。 これは、実際の会社名でなければなりません。

    ![右上のメニューで選択された会社名の例を示すスクリーンショット。](./media/scalex-enterprise-tutorial/Test_Admin.png)

1. 上部メニューから **[Integrations]\(統合\)** を選択し、 **[single sign-on]\(シングル サインオン\)** を選択します。

    ![ドロップダウン メニューで選択されている [Integrations]\(統合\)、[Single Sign-On]\(シングル サインオン\) を示すスクリーンショット。](./media/scalex-enterprise-tutorial/admin_sso.png) 

1. 次のようにフォームの操作を実行します。

    ![Configure single sign-on](./media/scalex-enterprise-tutorial/scalex_admin_save.png)

    a. **[Create any user who can authenticate with SSO]\(SSO で認証できるユーザーを作成する\)** を選択します。

    b. **[Service Provider saml]\(サービス プロバイダー SAML\)** : 値 **_urn:oasis:names:tc:SAML:2.0:nameid-format:persistent_** を貼り付けます

    c. **[Name of Identity Provider email field in ACS response (ACS 応答での ID プロバイダーの電子メール フィールドの名前)]** : 値 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を貼り付けます。

    d. **[Identity Provider EntityDescriptor Entity ID]\(ID プロバイダーの EntityDescriptor エンティティ ID\):** Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    e. **Identity Provider SingleSignOnService URL\(ID プロバイダーの SingleSignOnService URL\):** Azure portal からの **ログイン URL** を貼り付けます。

    f. **[Identity Provider public X509 certificate]\(ID プロバイダーのパブリック X509 証明書\):** Azure からダウンロードした X509 証明書をメモ帳で開いて、このボックスに貼り付けます。 証明書の内容の途中に改行が含まれていないことを確認します。

    g. 次のチェックボックスをオンにします: **[Enabled]\(有効化\)、[Encrypt NameID]\(暗号 NameID\)、[Sign AuthnRequests]\(AuthnRequests に署名する\)** 。

    h. **[Update SSO Settings (SSO 設定を更新する)]** をクリックして設定を保存します。

### <a name="create-scalex-enterprise-test-user"></a>ScaleX Enterprise のテスト ユーザーの作成

Azure AD ユーザーが ScaleX Enterprise にサインインできるようにするには、ユーザーを ScaleX Enterprise にプロビジョニングする必要があります。 ScaleX Enterprise の場合、プロビジョニングは自動化されたタスクであり、手動の手順は必要ありません。 SSO 資格情報で正しく認証できるすべてのユーザーは、ScaleX 側で自動的にプロビジョニングされます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [ScaleX Enterprise] タイルをクリックすると、SSO を設定した ScaleX Enterprise に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で ScaleX Enterprise を試す](https://aad.portal.azure.com/)