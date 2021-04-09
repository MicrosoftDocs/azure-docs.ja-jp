---
title: 'チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合 | Microsoft Docs'
description: Azure Active Directory と SilkRoad Life Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 89d086ce136885e203e300f04bdbf3ade0affeb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516054"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>チュートリアル: Azure Active Directory と SilkRoad Life Suite の統合

このチュートリアルでは、SilkRoad Life Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。
SilkRoad Life Suite と Azure AD の統合には、次の利点があります。

* SilkRoad Life Suite にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで SilkRoad Life Suite に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SilkRoad Life Suite と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます
* SilkRoad Life Suite でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SilkRoad Life Suite では、**SP** Initiated SSO がサポートされます

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>ギャラリーからの SilkRoad Life Suite の追加

Azure AD への SilkRoad Life Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SilkRoad Life Suite を追加する必要があります。

**ギャラリーから SilkRoad Life Suite を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**SilkRoad Life Suite**」と入力し、結果パネルで **[SilkRoad Life Suite]** を選択し、 **[追加]** をクリックして、アプリケーションを追加します。

    ![結果リストの SilkRoad Life Suite](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SilkRoad Life Suite で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SilkRoad Life Suite 内の関連ユーザー間にリンク関係が確立されている必要があります。

SilkRoad Life Suite で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SilkRoad Life Suite のシングル サインオンの構成](#configure-silkroad-life-suite-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SilkRoad Life Suite テスト ユーザーの作成](#create-silkroad-life-suite-test-user)** - SilkRoad Life Suite で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SilkRoad Life Suite で Azure AD シングル サインオンを構成するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) の **SilkRoad Life Suite** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    > [!NOTE]
    > このチュートリアルで後述する **サービス プロバイダーのメタデータ ファイル** を取得します。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![スクリーンショットは、[メタデータ ファイルをアップロードする] リンクを含む、[基本的な SAML 構成] を示しています。](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![スクリーンショットは、ファイルを選択してアップロードできるダイアログ ボックスを示しています。](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/sp-identifier-reply.png)

    > [!Note]
    > **識別子** と **返信 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

    d. **[サインオン URL]** ボックスに、`https://<subdomain>.silkroad-eng.com/Authentication/` という形式で URL を入力します。

5. **サービス プロバイダー メタデータ ファイル** がない場合は、 **[基本的な SAML 構成]** セクションで次の手順に従います。

    ![[SilkRoad Life Suite のドメインと URL] のシングル サインオン情報](common/sp-identifier-reply.png)

    a. **[サインオン URL]** ボックスに、`https://<subdomain>.silkroad-eng.com/Authentication/` という形式で URL を入力します。

    b. **[識別子]** ボックスに、次のパターンを使用して URL を入力します。

    - `https://<subdomain>.silkroad-eng.com/Authentication/SP`
    - `https://<subdomain>.silkroad.com/Authentication/SP`

    c. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    - `https://<subdomain>.silkroad-eng.com/Authentication/`
    - `https://<subdomain>.silkroad.com/Authentication/`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL、識別子、および応答 URL で値を更新します。 これらの値を取得するには、[SilkRoad Life Suite クライアント サポート チーム](https://www.silkroad.com/locations/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

7. **[SilkRoad Life Suite のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-silkroad-life-suite-single-sign-on"></a>SilkRoad Life Suite のシングル サインオンの構成

1. SilkRoad 企業サイトに管理者としてサインインします。

    > [!NOTE]
    > Microsoft Azure AD とフェデレーションを構成するために SilkRoad Life Suite の認証アプリケーションへのアクセス権を取得するには、SilkRoad サポートまたは SilkRoad サービス担当者にお問い合わせください。

1. **[サービス プロバイダー]** に移動して、 **[フェデレーションの詳細]** をクリックします。

    ![[サービス プロバイダー] から [フェデレーションの詳細] を選択した画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png)

1. **[Download Federation Metadata (フェデレーション メタデータのダウンロード)]** をクリックし、メタデータ ファイルをコンピューターに保存します。 ダウンロードしたフェデレーション メタデータは、Azure portal の **[基本的な SAML 構成]** セクションで **[サービス プロバイダー メタデータ ファイル]** として使用します。

    ![[Download Federation Metadata]\(フェデレーション メタデータのダウンロード\) リンクのスクリーンショット。](./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png)

1. **SilkRoad** アプリケーションで、 **[Authentication Sources (認証ソース)]** をクリックします。

    ![[Authentication Sources]\(認証ソース\) が選択された画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png) 

1. **[Add Authentication Source (認証ソースの追加)]** をクリックします。

    ![[Add Authentication Source]\(認証ソースの追加\) リンクが選択された画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png)

1. **[Add Authentication Source (認証ソースの追加)]** セクションで、次の手順に従います。

    ![[Create Identity Provider using File Data]\(ファイル データを使用して ID プロバイダーを作成\) ボタンが選択されている [Add Authentication Source]\(認証ソースの追加\) 画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png)
  
    a. **[Option 2 - Metadata File]\(オプション 2 - メタデータ ファイル\)** の下の **[参照]** をクリックして、Azure Portal からダウンロードしたメタデータ ファイルをアップロードします。
  
    b. **[Create Identity Provider using File Data]** をクリックします。

1. **[Authentication Sources (認証ソース)]** セクションで、 **[編集]** をクリックします。

    ![[編集] オプションが選択されている [Authentication Sources]\(認証ソース\) 画面のスクリーンショット。](./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png)

1. **[Edit Authentication Source (認証ソースの編集)]** ダイアログ ボックスで、次の手順を実行します。

    ![[Edit Authentication Source]\(認証ソースの編集\) ダイアログ ボックスのスクリーンショット。ここで、説明されている値を入力できます。](./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png)

    a. **[Enabled]** で **[Yes]** を選択します。

    b. **[EntityId]\(エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[IdP Description]\(IdP の説明\)** ボックスに、構成の説明を入力します (例: *Azure AD の SSO*)。

    d. **[メタデータ ファイル]** ボックスに、Azure Portal からダウンロードした **メタデータ** ファイルをアップロードします。
  
    e. **[IdP Name]\(IdP 名\)** ボックスに、構成の固有の名前を入力します (例: *Azure SP*)。
  
    f. **[Logout Service URL]\(ログアウト サービス URL\)** テキスト ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    g. **[Sign-on service URL]\(サインオン サービス URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    h. **[保存]** をクリックします。

1. その他のすべての認証のソースを無効にします。

    ![[Authentication Sources]\(認証ソース\) 画面のスクリーンショット。ここで、他のソースを無効にすることができます。 ](./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに「`brittasimon@yourcompanydomain.extension`」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SilkRoad Life Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal 上で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択してから、 **[SilkRoad Life Suite]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SilkRoad Life Suite]** を選択します。

    ![アプリケーションの一覧の SilkRoad Life Suite のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-silkroad-life-suite-test-user"></a>SilkRoad Life Suite のテスト ユーザーの作成

このセクションでは、SilkRoad Life Suite で Britta Simon というユーザーを作成します。 SilkRoad Life Suite プラットフォームにユーザーを追加するには、[SilkRoad Life Suite Client サポート チーム](https://www.silkroad.com/locations/)に問い合わせてください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [SilkRoad Life Suite] タイルをクリックすると、SSO を設定した SilkRoad Life Suite に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)