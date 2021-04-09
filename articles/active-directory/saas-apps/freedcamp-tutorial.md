---
title: チュートリアル:Freedcamp と Azure Active Directory の統合 | Microsoft Docs
description: Freedcamp と Azure Active Directory の間でシングル サインオンを構成する方法について学習します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 19378251408d55868ed844a5505ae48ece55dc3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92451497"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>チュートリアル:Freedcamp と Azure Active Directory の統合

このチュートリアルでは、Freedcamp と Azure Active Directory (Azure AD) を統合する方法について学習します。 Freedcamp と Azure AD を統合すると、次のことができます。

* Freedcamp にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Freedcamp に自動的にサインインできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Freedcamp でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Freedcamp では、**SP Initiated SSO と IDP Initiated SSO** をサポートしています。

## <a name="adding-freedcamp-from-the-gallery"></a>ギャラリーからの Freedcamp の追加

Freedcamp を Azure AD に統合するには、管理している SaaS アプリの一覧にギャラリーから Freedcamp を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで検索ボックスに「**Freedcamp**」と入力します。
1. 結果パネルから「**Freedcamp**」を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、Freedcamp で Azure AD の SSO を構成し、テストします。 SSO が機能するには、Azure AD ユーザーと Freedcamp の関連ユーザーの間で、リンク関係を確立する必要があります。

Freedcamp で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Freedcamp の構成](#configure-freedcamp)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Freedcamp テスト ユーザーの作成](#create-freedcamp-test-user)** - Freedcamp で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Freedcamp** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    1. **[識別子]** ボックスに、`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>` の形式で URL を入力します。

    2. **[応答 URL]** ボックスに、`https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<SUBDOMAIN>.freedcamp.com/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 ユーザーの顧客のドメインの URL 値を入力することも可能です。これは必ずしも `freedcamp.com` のパターンに従っている必要はなく、顧客のアプリケーション インスタンスに固有の顧客ドメインに固有な任意の値を入力できます。 URL のパターンの詳細については、[Freedcamp クライアント サポート チーム](mailto:devops@freedcamp.com)にお問い合わせいただくことも可能です。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Freedcamp のセットアップ]** セクションで、ご自分の要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Freedcamp の構成

1. Freedcamp 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup Freedcamp]\(Freedcamp の設定)** をクリックすると、Freedcamp アプリケーションに移動します。 そこから、管理者資格情報を提供して Freedcamp にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Freedcamp を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の Freedcamp 企業サイトにサインインして、次の手順のようにします。

4. ページの右上隅の **[プロファイル]** をクリックし、 **[マイ アカウント]** に移動します。

    ![選択されている [プロファイル] と [マイ アカウント] 示すスクリーンショット。](./media/freedcamp-tutorial/config01.png)

5. メニュー バーの左の **[SSO]** をクリックし、 **[Your SSO connections]\(お使いの SSO 接続\)** ページで次の手順を実行します。

    ![左側のメニュー バーで選択されている [S S O] と、値が入力され、[送信] ボタンが選択されている [Your S S O connections]\(お使いの S S O 接続\) ページを示すスクリーンショット。](./media/freedcamp-tutorial/config02.png)

    a. **[タイトル]** テキスト ボックスに、タイトルを入力します。

    b. **[エンティティ ID]** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[ログイン URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. Base64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[証明書]** テキスト ボックスに貼り付けます。

    e. **[送信]** をクリックします。

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

このセクションでは、Freedcamp へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Freedcamp]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-freedcamp-test-user"></a>Freedcamp テスト ユーザーの作成

Azure AD ユーザーが Freedcamp にサインインできるようにするには、そのユーザーを Freedcamp にプロビジョニングする必要があります。 Freedcamp では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 別の Web ブラウザー ウィンドウで、Freedcamp にセキュリティ管理者としてサインインします。

2. ページの右上隅の **[Profile]\(プロファイル\)** をクリックし、 **[Manage System]\(システムの管理\)** に移動します。

    ![Freedcamp の構成](./media/freedcamp-tutorial/config03.png)

3. [Manage System]\(システムの管理\) ページの右側で、次の手順を実行します。

    ![選択されている [Add or invite Users]\(ユーザーの追加または招待\) ボタン、強調表示されている [Email]\(電子メール\) フィールド、選択されている [Add User]\(ユーザーの追加\) ボタン示すスクリーンショット。](./media/freedcamp-tutorial/config04.png)

    a. **[Add or invite Users]** \(ユーザーの追加または招待\) をクリックします。

    b. **[Email]\(電子メール\)** テキスト ボックスに、`Brittasimon@contoso.com` などユーザーのメール アドレスを入力します。

    c. **[ユーザーの追加]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Freedcamp] タイルを選択すると、SSO を設定した Freedcamp に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)