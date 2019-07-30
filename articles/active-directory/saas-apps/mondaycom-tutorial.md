---
title: チュートリアル:Azure Active Directory と monday.com の統合 | Microsoft Docs
description: Azure Active Directory と monday.com の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c5e473c62dc6b38f0b2c2906560d6099842d49
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67718503"
---
# <a name="tutorial-integrate-mondaycom-with-azure-active-directory"></a>チュートリアル:monday.com と Azure Active Directory を統合する

このチュートリアルでは、monday.com と Azure Active Directory (Azure AD) を統合する方法について説明します。 monday.com と Azure AD を統合すると、次のことが可能になります。

* monday.com にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで monday.com に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* monday.com でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 monday.com では、**SP Initiated SSO と IDP Initiated SSO** のほか、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-mondaycom-from-the-gallery"></a>ギャラリーからの monday.com の追加

Azure AD への monday.com の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に monday.com を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**monday.com**」と入力します。
1. 結果のパネルから **[monday.com]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、monday.com に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと monday.com の関連ユーザーとの間にリンク関係を確立する必要があります。

monday.com で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[monday.com の構成](#configure-mondaycom)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[monday.com のテスト ユーザーの作成](#create-mondaycom-test-user)** - monday.com で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **monday.com** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ウィンドウで、サービス プロバイダー メタデータ ファイルがあり、**IDP 開始モード**で構成する場合、次の手順を実行します。

    1. **[メタデータ ファイルをアップロードする]** を選択します。

    1. メタデータ ファイルを選択するには、フォルダー アイコンを選択し、 **[アップロード]** を選択します。

    1. メタデータ ファイルが正常にアップロードされると、 **[基本的な SAML 構成]** ウィンドウで **[識別子]** と **[応答 URL]** の値が自動的に入力されます。

       > [!Note]
       > **識別子**と**応答 URL** の値が自動的に設定されない場合は、手動で値を入力してください。 **識別子**と**応答 URL** は同じであり、その値は `https://<your-domain>.monday.com/saml/saml_callback` というパターンになっています。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<your-domain>.monday.com` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[monday.com クライアント サポート チーム](mailto:dev@food.ee)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. monday.com アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]**   アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![[ユーザー属性] ウィンドウ](common/edit-attribute.png)

1. その他に、monday.com アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name | ソース属性|
    | ---------------| --------------- |
    | Email | User.mail |
    | FirstName | User.givenname |
    | LastName | User.surname |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** を削除します。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up monday.com]\(monday.com のセットアップ\)** セクションで、要件に従って適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-mondaycom"></a>monday.com の構成

1. monday.com 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイアプリによるセキュリティで保護された Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Setup monday.com]\(monday.com のセットアップ\)** をクリックすると、monday.com アプリケーションに移動します。 そこから、管理者の資格情報を入力して monday.com にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. monday.com を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として monday.com サイトにサインインして、次の手順を実行します。

1. ページの右上隅にある **[プロファイル]** に移動し、 **[管理者]** をクリックします。

    ![monday.com の構成](./media/mondaycom-tutorial/configuration01.png)

1. **[セキュリティ]** を選択し、SAML の横の **[開く]** をクリックします。

    ![monday.com の構成](./media/mondaycom-tutorial/configuration02.png)

1. 実際の IDP から、以下の詳細を入力します。

    ![monday.com の構成](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >詳細については、[こちら](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642)の記事を参照してください。

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

このセクションでは、B.Simon に monday.com へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[monday.com]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-mondaycom-test-user"></a>monday.com のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを monday.com に作成します。 monday.com では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ユーザーがまだ monday.com に存在しない場合は、monday.com にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [monday.com] タイルを選択すると、SSO を設定した monday.com に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
