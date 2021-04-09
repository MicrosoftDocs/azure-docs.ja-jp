---
title: チュートリアル:Azure Active Directory と Zscaler Private Access (ZPA) の統合 | Microsoft Docs
description: Azure Active Directory と Zscaler Private Access (ZPA) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: jeedes
ms.openlocfilehash: 58e2a19f2d57eafc7d2967141d584dc7a22fe76c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955671"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と Zscaler Private Access (ZPA) の統合

このチュートリアルでは、Zscaler Private Access (ZPA) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zscaler Private Access (ZPA) を Azure AD に統合すると、次のことができます。

* Azure AD で、Zscaler Private Access (ZPA) にアクセスできるユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで Zscaler Private Access (ZPA) に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Zscaler Private Access (ZPA) サブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 

* Zscaler Private Access (ZPA) では **SP** Initiated SSO がサポートされます。

## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>ギャラリーから Zscaler Private Access (ZPA) を追加する

Azure AD への Zscaler Private Access (ZPA) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zscaler Private Access (ZPA) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zscaler Private Access (ZPA)** 」と入力します。
1. 結果のパネルから **[Zscaler Private Access (ZPA)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zscaler Private Access (ZPA) に対する Azure AD SSO を構成し、テストします。 SSO が機能するために、Azure AD ユーザーと Zscaler Private Access (ZPA) の関連ユーザーの間で、リンク関係を確立する必要があります。

Zscaler Private Access (ZPA) に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Zscaler Private Access (ZPA) SSO の構成](#configure-zscaler-private-access-zpa-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zscaler Private Access (ZPA) のテスト ユーザーの作成](#create-zscaler-private-access-zpa-test-user)** - Zscaler Private Access (ZPA) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zscaler Private Access (ZPA)** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    1. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. **[識別子 (エンティティ ID)]** ボックスに `https://samlsp.private.zscaler.com/auth/metadata` という URL を入力します。

    > [!NOTE]
    > **サインオン URL** は実際の値ではありません。 実際のサインオン URL で値を更新する必要があります。 値を取得するには、[Zscaler Private Access (ZPA) サポート チーム](https://help.zscaler.com/zpa-submit-ticket)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Set up Zscaler Private Access (ZPA)]\(Zscaler Private Access (ZPA) の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Zscaler Private Access (ZPA) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Zscaler Private Access (ZPA)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zscaler-private-access-zpa-sso"></a>Zscaler Private Access (ZPA) の SSO の構成

1. Zscaler Private Access (ZPA) 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup Zscaler Private Access (ZPA)]\(Zscaler Private Access (ZPA) のセットアップ\)** をクリックすると、Zscaler Private Access (ZPA) アプリケーションに移動します。 そこから、管理者資格情報を入力して Zscaler Private Access (ZPA) にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Zscaler Private Access (ZPA) を手動で設定する場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Zscaler Private Access (ZPA) 企業サイトにサインインして、次の手順を実行します。

4. メニュー左側の **[Administration]\(管理\)** をクリックして **[AUTHENTICATION]\(認証\)** セクションに移動し、 **[IdP Configuration]\(IdP 構成\)** をクリックします。

    ![Zscaler Private Access Administrator の [Administration]\(管理\)](./media/zscalerprivateaccess-tutorial/administration.png)

5. 右上隅の **[Add IdP Configuration]** をクリックします。 

    ![Zscaler Private Access Administrator の IdP](./media/zscalerprivateaccess-tutorial/metadata.png)

6. **[Add IdP Configuration]** ページで、次の手順に従います。
 
    ![Zscaler Private Access Administrator での選択](./media/zscalerprivateaccess-tutorial/select.png)

    a. **[IdP Metadata File Upload]** フィールドの **[Select File]** をクリックして、Azure AD からダウンロードしたメタデータ ファイルをアップロードします。

    b. Azure AD から **IdP メタデータ** が読み取られ、以下に示すようにすべてのフィールド情報が設定されます。

    ![Zscaler Private Access Administrator での構成](./media/zscalerprivateaccess-tutorial/configure.png)

    c. **[Domains]** フィールドから自分のドメインを選択します。
    
    d. **[保存]** をクリックします。

### <a name="create-zscaler-private-access-zpa-test-user"></a>Zscaler Private Access (ZPA) テスト ユーザーの作成

このセクションでは、Zscaler Private Access (ZPA) で Britta Simon というユーザーを作成します。 [Zscaler Private Access (ZPA) のサポート チーム](https://help.zscaler.com/zpa-submit-ticket)に問い合わせて、Zscaler Private Access (ZPA) プラットフォームでユーザーを追加します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zscaler Private Access (ZPA) のサインオン URL にリダイレクトされます。 

* Zscaler Private Access (ZPA) のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zscaler Private Access (ZPA)] タイルをクリックすると、Zscaler Private Access (ZPA) のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Zscaler Private Access (ZPA) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。