---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と WhosOffice の統合 | Microsoft Docs
description: Azure Active Directory と WhosOffice の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/05/2021
ms.author: jeedes
ms.openlocfilehash: 622aa6ea143eb9c279cc7451ccc2c91a7b9e602f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734463"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-whosoffice"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と WhosOffice の統合

このチュートリアルでは、WhosOffice と Azure Active Directory (Azure AD) を統合する方法について説明します。 WhosOffice を Azure AD と統合すると、次のことができます。

* WhosOffice にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して WhosOffice に自動的にサインインできるようになります。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* WhosOffice でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* WhosOffice では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-whosoffice-from-the-gallery"></a>ギャラリーからの WhosOffice の追加

Azure AD への WhosOffice の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に WhosOffice を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**WhosOffice**」と入力します。
1. 結果のパネルで **[WhosOffice]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-whosoffice"></a>WhosOffice の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、WhosOffice に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと WhosOffice の関連ユーザーとの間にリンク関係を確立する必要があります。

WhosOffice に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[WhosOffice の SSO の構成](#configure-whosoffice-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[WhosOffice のテスト ユーザーの作成](#create-whosoffice-test-user)** - WhosOffice で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **WhosOffice** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (鉛筆) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.my.whosoffice.com/int/azure/consume.aspx` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.my.whosoffice.com/int/azure` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URL とサインオン URL でこれらの値を更新します。 これらの値を取得するには、[WhosOffice クライアント サポート チーム](mailto:support@whosoffice.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[WhosOffice のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に WhosOffice へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[WhosOffice]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-whosoffice-sso"></a>WhosOffice の SSO の構成

1. WhosOffice 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[WhosOffice のセットアップ]** をクリックすると、WhosOffice アプリケーションに移動します。 そこから、管理者の資格情報を入力して WhosOffice にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. WhosOffice を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、WhosOffice 企業サイトに管理者としてサインインします。

1. **[Settings]\(設定\)** をクリックし、 **[Company]\(会社\)** を選択します。

    ![[Settings]\(設定\) から [Company]\(会社\) が選択されていることを示すスクリーンショット。](./media/whosoffice-tutorial/configuration1.png)

1. **[Apps/Integrations]\(アプリおよび統合\)** をクリックします。

    ![[Company settings]\(会社の設定\) から [Apps / Integrations]\(アプリおよび統合\) が選択されていることを示すスクリーンショット。](./media/whosoffice-tutorial/configuration2.png)

1. [Provider]\(プロバイダー\) ボックスの一覧から **[Microsoft Azure]** を選択し、 **[Activate Login Provider]\(ログイン プロバイダーのアクティブ化\)** をクリックします。

    ![[Microsoft Azure] に対して [Activate Login Provider]\(ログイン プロバイダーのアクティブ化\) が選択されていることを示すスクリーンショット。](./media/whosoffice-tutorial/configuration3.png)

1. **[Upload]\(アップロード\)** オプションをクリックして、Azure portal からダウンロードしたフェデレーション メタデータ ファイルをアップロードします。
    
    ![メタ データ ファイルの [Upload]\(アップロード\) オプションを示すスクリーンショット。](./media/whosoffice-tutorial/configuration4.png)

### <a name="create-whosoffice-test-user"></a>WhosOffice のテスト ユーザーの作成

1. 別の Web ブラウザー ウィンドウで、管理者として WhosOffice Web サイトにサインインします。

1. **[Settings]\(設定\)** をクリックし、 **[Users]\(ユーザー\)** を選択します。

    ![[Settings]\(設定\) から [Users]\(ユーザー\) が選択されていることを示すスクリーンショット。](./media/whosoffice-tutorial/user1.png)

1. **[Create new User]\(新しいユーザーの作成\)** を選択します。

    ![[Create new User]\(新しいユーザーの作成\) が選択されていることを示すスクリーンショット。](./media/whosoffice-tutorial/user2.png)

1. 組織の要件に従って、ユーザーに関する必要な詳細情報を入力します。

    ![[new User]\(新しいユーザー\) ダイアログ ボックスを示すスクリーンショット。ここで、ユーザー データを入力できます。](./media/whosoffice-tutorial/user3.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる WhosOffice のサインオン URL にリダイレクトされます。

* WhosOffice のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した WhosOffice に自動的にサインインされます

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [WhosOffice] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した WhosOffice に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

WhosOffice を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。