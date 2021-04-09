---
title: チュートリアル:Azure Active Directory と MyWorkDrive の統合 | Microsoft Docs
description: Azure Active Directory と MyWorkDrive の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: b639ee45bfa6356054d93a1b6fcc8a7959173acd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516088"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>チュートリアル:MyWorkDrive と Azure Active Directory の統合

このチュートリアルでは、MyWorkDrive と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と MyWorkDrive を統合すると、次のことができます。

* MyWorkDrive にアクセスする Azure AD ユーザーを制御します。
* ユーザーが自分の Azure AD アカウントを使用して MyWorkDrive に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* MyWorkDrive でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 MyWorkDrive では、**SP** と **IDP** によって開始される SSO がサポートされます

## <a name="adding-myworkdrive-from-the-gallery"></a>ギャラリーからの MyWorkDrive の追加

Azure AD への MyWorkDrive の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MyWorkDrive を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**MyWorkDrive**」と入力します。
1. 結果ウィンドウで **[MyWorkDrive]** を選択し、アプリケーションを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、MyWorkDrive で Azure AD の SSO を構成し、テストします。 SSO が機能するには、Azure AD ユーザーと MyWorkDrive の関連ユーザーの間で、リンク関係を確立する必要があります。

MyWorkDrive で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[MyWorkDrive の SSO の構成](#configure-myworkdrive-sso)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[MyWorkDrive テスト ユーザーの作成](#create-myworkdrive-test-user)** - Azure AD の Britta Simon にリンクさせるために、対応するユーザーを MyWorkDrive で作成します。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **MyWorkDrive** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SERVER.DOMAIN.COM>/Account/Login-saml` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の応答 URLとサインオン URL でこれらの値を更新します。 自社の MyWorkDrive サーバーのホスト名を入力します。たとえば、次のようになります。
    > 
    > 応答 URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > サインオン URL: `https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > これらの値に対して独自のホスト名と TLS/SSL 証明書を設定する方法がわからない場合は、[MyWorkDrive サポート チーム](mailto:support@myworkdrive.com)にお問い合わせください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をクリップボードにコピーします。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>MyWorkDrive の SSO を構成する

1. MyWorkDrive 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Setup MyWorkDrive]\(MyWorkDrive のセットアップ\)** をクリックすると、MyWorkDrive アプリケーションに移動します。 そこから、管理者の資格情報を入力して MyWorkDrive にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 4 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. MyWorkDrive を手動で設定する場合、別の Web ブラウザー ウィンドウで、セキュリティ管理者として MyWorkDrive にサインインします。

1. MyWorkDrive サーバー上の管理パネルで、 **[ENTERPRISE]\(エンタープライズ\)** をクリックし、次の手順を実行します。

    ![管理](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. **[SAML/ADFS SSO]\(SAML/ADFS SSO\)** を有効にします。

    b. **[SAML - Azure AD]\(SAML - Azure AD\)** を選択します

    c. **[Azure App Federation Metadata Url]\(Azure アプリのフェデレーション メタデータ URL\)** テキスト ボックスに、Azure portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    d. **[保存]**

    > [!NOTE]
    > 追加情報については、[MyWorkDrive の Azure AD サポートに関する記事](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/)をご覧ください。

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

このセクションでは、Britta Simon に MyWorkDrive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[MyWorkDrive]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-myworkdrive-test-user"></a>MyWorkDrive テスト ユーザーの作成

このセクションでは、MyWorkDrive で Britta Simon というユーザーを作成します。 [MyWorkDrive サポート チーム](mailto:support@myworkdrive.com)と連携し、MyWorkDrive プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [MyWorkDrive] タイルを選択すると、SSO を設定した MyWorkDrive に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)