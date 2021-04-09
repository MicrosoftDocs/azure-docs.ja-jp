---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Nuclino の統合 | Microsoft Docs
description: Azure Active Directory と Nuclino の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: 4307838df56fb2dc1cdf6ee4044b8ce8de164227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518604"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Nuclino の統合

このチュートリアルでは、Nuclino と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Nuclino を統合すると、次のことができます。

* Nuclino にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Nuclino に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Nuclino でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Nuclino では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* Nuclino では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-nuclino-from-the-gallery"></a>ギャラリーからの Nuclino の追加

Azure AD への Nuclino の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Nuclino を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Nuclino**」と入力します。
1. 結果のパネルから **[Nuclino]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Nuclino の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Nuclino に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Nuclino の関連ユーザーとの間にリンク関係を確立する必要があります。

Nuclino での Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Nuclino の SSO の構成](#configure-nuclino-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Nuclino テスト ユーザーの作成](#create-nuclino-test-user)** - Nuclino で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Nuclino** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 **[認証]** セクションの実際の識別子と応答 URL にこれらの値を置き換えます。実際の値については後で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://app.nuclino.com/<UNIQUE-ID>/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 この値を取得するには、[Nuclino クライアント サポート チーム](mailto:contact@nuclino.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. Nuclino アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)

7. その他に、Nuclino アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 |  ソース属性|
    | ---------------| --------- |
    | first_name | User.givenname |
    | last_name | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Nuclino のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Nuclino へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Nuclino]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-nuclino-sso"></a>Nuclino の SSO の構成

1. Nuclino 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Nuclino のセットアップ]** をクリックすると、Nuclino アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Nuclino にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Nuclino を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Nuclino 企業サイトにサインインして、次の手順を実行します。

4. **アイコン** をクリックします。

    ![[Azure AD SSO] の横にある "メニュー" アイコンが選択されているスクリーンショット。](./media/nuclino-tutorial/configure1.png)

5. **[Azure AD SSO]** をクリックし、ドロップダウンから **[Team settings]\(チームの設定\)** を選択します。

    ![[Team settings]\(チームの設定\) が選択されている [Azure AD SSO] ドロップダウンを示すスクリーンショット。](./media/nuclino-tutorial/configure2.png)

6. 左側のナビゲーション ウィンドウから **[Authentication]\(認証\)** を選択します。

    ![[Authentication]\(認証\) が選択されているスクリーンショット。](./media/nuclino-tutorial/configure3.png)

7. **[Authentication]\(認証\)** セクションで、次の手順に従います。

    ![Nuclino 構成](./media/nuclino-tutorial/configure4.png)

    a. **[SAML-based single sign-on (SSO)]\(SAML ベースのシングル サインオン (SSO)\)** を選択します。

    b. **[ACS URL (You need to copy and paste this to your SSO provider)]\(ACS URL (この値をコピーして SSO プロバイダーにコピーしてください)\)** 値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    c. **[Entity ID (You need to copy and paste this to your SSO provider)]\(エンティティ ID (この値をコピーして SSO プロバイダーにコピーしてください)\)** 値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子]** ボックスに貼り付けます。

    d. **[SSO URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. **[エンティティ ID]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    f. ダウンロードした **証明書 (Base64)** ファイルをメモ帳で開きます。 その内容をクリップボードにコピーし、 **[Public certificate]\(公開証明書\)** ボックスに貼り付けます。

    g. **[SAVE CHANGES]\(変更の保存\)** をクリックします。

### <a name="create-nuclino-test-user"></a>Nuclino テスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Nuclino に作成します。 Nuclino では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Nuclino にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[Nuclino サポート チーム](mailto:contact@nuclino.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Nuclino] タイルをクリックすると、SSO を設定した Nuclino に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Nuclino を試す](https://aad.portal.azure.com/)