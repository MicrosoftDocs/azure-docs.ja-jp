---
title: チュートリアル:Azure Active Directory と Secret Server (On-Premises) の統合 | Microsoft Docs
description: Azure Active Directory と Secret Server (On-Premises) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4926fc1833cc14b2ad81a01e230a5c3c37ba6ab3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880171"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>チュートリアル:Secret Server (On-Premises) と Azure Active Directory の統合

このチュートリアルでは、Secret Server (On-Premises) と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Secret Server (On-Premises) を統合すると、次のことができます。

* Secret Server (On-Premises) にアクセスするユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Secret Server (On-Premises) に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Secret Server (On-Premises) のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Secret Server (On-Premises) では、**SP および IDP** Initiated SSO がサポートされます

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>ギャラリーから Secret Server (On-Premises) を追加する

Azure AD への Secret Server (On-Premises) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Secret Server (On-Premises) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Secret Server (On-Premises)** 」と入力します。
1. 結果ウィンドウで **[Secret Server (On-Premises)]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Secret Server (On-Premises) に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Secret Server (On-Premises) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Secret Server (On-Premises) で Azure AD の SSO を構成してテストするには、次の手順を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Secret Server (On-Premises) SSO の構成](#configure-secret-server-on-premises-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Secret Server (On-Premises) テスト ユーザーの作成](#create-secret-server-on-premises-test-user)** - Secret Server (On-Premises) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Secret Server (On-Premises)** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、ユーザーが選択した値を `https://secretserveronpremises.azure` のように入力します。

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

    ![署名オプション](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. **[証明書オプション]** で **[SAML 応答とアサーションへの署名]** を選択します。

    ![署名オプション](./media/secretserver-on-premises-tutorial/signing-option.png)

1. **[Secret Server (On-Premises) のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>Secret Server (On-Premises) SSO の構成

**Secret Server (On-Premises)** 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)** と Azure portal からコピーした適切な URL を [Secret Server (On-Premises) サポート チーム](https://thycotic.force.com/support/s/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

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

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-secret-server-on-premises-test-user"></a>Secret Server (On-Premises) テスト ユーザーの作成

このセクションでは、Secret Server (On-Premises) で Britta Simon というユーザーを作成します。  [Secret Server (On-Premises) サポート チーム](https://thycotic.force.com/support/s/)と連携しながら、Secret Server (On-Premises) プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで Secret Server (On-Premises) のタイルをクリックすると、SSO を設定した Secret Server (On-Premises) に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)