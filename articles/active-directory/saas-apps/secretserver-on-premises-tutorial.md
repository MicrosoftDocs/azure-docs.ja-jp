---
title: チュートリアル:Azure Active Directory と Secret Server (On-Premises) の統合 | Microsoft Docs
description: Azure Active Directory と Secret Server (On-Premises) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: d723bf1ce82e6d443dfa55dda7d33a3a9bfc16b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647019"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>チュートリアル:Secret Server (On-Premises) と Azure Active Directory の統合

このチュートリアルでは、Secret Server (On-Premises) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Secret Server (On-Premises) を統合すると、次のことができます。

* Secret Server (On-Premises) にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Secret Server (On-Premises) に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Secret Server (On-Premises) のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Secret Server (On-Premises) では、**SP および IDP** Initiated SSO がサポートされます

## <a name="add-secret-server-on-premises-from-the-gallery"></a>ギャラリーからの Secret Server (On-Premises) の追加

Azure AD への Secret Server (On-Premises) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Secret Server (On-Premises) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Secret Server (On-Premises)** 」と入力します。
1. 結果ウィンドウで **[Secret Server (On-Premises)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-secret-server-on-premises"></a>Secret Server (On-Premises) の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Secret Server (On-Premises) に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Secret Server (On-Premises) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Secret Server (On-Premises) に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Secret Server (On-Premises) の SSO の構成](#configure-secret-server-on-premises-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Secret Server (On-Premises) テスト ユーザーの作成](#create-secret-server-on-premises-test-user)** - Secret Server (On-Premises) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Secret Server (On-Premises)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://secretserveronpremises.azure` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<SecretServerURL>/SAML/AssertionConsumerService.aspx` のパターンを使用して URL を入力します

    > [!NOTE]
    > 上記のエンティティ ID は単なる一例です。Azure AD で Secret Server インスタンスを識別する一意の値を自由に選択することができます。 このエンティティ ID を [Secret Server (On-Premises) クライアント サポート チーム](https://thycotic.force.com/support/s/)に送り、サポート チーム側で構成してもらう必要があります。 詳細については、[こちらの記事](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server)を参照してください。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SecretServerURL>/login.aspx` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 これらの値を取得するには、[Secret Server (On-Premises) クライアント サポート チーム](https://thycotic.force.com/support/s/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[SAML 署名証明書]** ダイアログを開きます。

    ![[Certificate (Base64)]\(証明書 (Base64)\) の [Download]\(ダウンロード\) アクションが選択されている [S A M L Signing Certificate]\(S A M L 署名証明書\) セクションを示すスクリーンショット。](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. **[証明書オプション]** で **[SAML 応答とアサーションへの署名]** を選択します。

    ![署名オプション](./media/secretserver-on-premises-tutorial/signing-option.png)

1. **[Secret Server (On-Premises) のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Secret Server (On-Premises) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Secret Server (On-Premises)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-secret-server-on-premises-sso"></a>Secret Server (On-Premises) SSO の構成

**Secret Server (On-Premises)** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [Secret Server (On-Premises) サポート チーム](https://thycotic.force.com/support/s/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-secret-server-on-premises-test-user"></a>Secret Server (On-Premises) テスト ユーザーの作成

このセクションでは、Secret Server (On-Premises) で Britta Simon というユーザーを作成します。 [Secret Server (On-Premises) サポート チーム](https://thycotic.force.com/support/s/)と連携しながら、Secret Server (On-Premises) プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Secret Server (On-Premises) のサインオン URL にリダイレクトされます。  

* Secret Server (On-Premises) のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Secret Server (On-Premises) に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Secret Server (On-Premises)] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Secret Server (On-Premises) に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Secret Server (On-Premises) を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。