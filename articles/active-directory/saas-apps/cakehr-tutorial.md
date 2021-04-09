---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と CakeHR の統合 | Microsoft Docs
description: Azure Active Directory と CakeHR の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 08e028ba057ad57f3d600bc59bf7595c0b1d354c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92456576"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と CakeHR の統合

このチュートリアルでは、CakeHR と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と CakeHR を統合すると、次のことができます。

* CakeHR にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して CakeHR に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* CakeHR でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* CakeHR では、**SP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-cakehr-from-the-gallery"></a>ギャラリーからの CakeHR の追加

Azure AD への CakeHR の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に CakeHR を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**CakeHR**」と入力します。
1. 結果のパネルから **[CakeHR]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-cakehr"></a>CakeHR の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、CakeHR に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと CakeHR の関連ユーザーとの間にリンク関係を確立する必要があります。

CakeHR に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[CakeHR SSO の構成](#configure-cakehr-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[CakeHR テスト ユーザーの作成](#create-cakehr-test-user)** - CakeHR で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **CakeHR** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<yourcakedomain>.cake.hr/` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<yourcakedomain>.cake.hr/services/saml/consume` のパターンを使用して URL を入力します
    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 この値を取得するには、[CakeHR クライアント サポート チーム](mailto:info@cake.hr)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

1. **[SAML 署名証明書]** セクションで **[THUMBPRINT]\(拇印\)** の値をコピーし、メモ帳に保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

1. **[CakeHR のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に CakeHR へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[CakeHR]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cakehr-sso"></a>CakeHR SSO の構成

1. CakeHR 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[CakeHR のセットアップ]** をクリックすると、CakeHR アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して CakeHR にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. CakeHR を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として CakeHR 企業サイトにサインインして、次の手順のようにします。

1. ページの右上隅の **[プロファイル]** をクリックし、 **[設定]** に移動します。

    ![このスクリーンショットは、[設定] が選択された状態の [プロファイル] を示しています。](./media/cakehr-tutorial/config01.png)

1. メニュー バーの左側で **[INTEGRATIONS]\(統合\)**  >  **[SAML SSO]** の順にクリックし、以下の手順を実行します。

    ![このスクリーンショットは、[設定] ペインを示しています。ここで、これらの手順を実行します。](./media/cakehr-tutorial/config02.png)

    a. **[Entity ID]\(エンティティ ID\)** ボックスに、「`cake.hr`」と入力します。

    b. **[Authentication URL]\(認証 URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. **[Key fingerprint (SHA1 format)]\(キーの拇印 (SHA1 形式)\)** ボックスに、Azure portal からコピーした **THUMBPRINT (拇印)** 値を貼り付けます。

    d. **[Enable Single Sign on]\(シングル サインオンを有効にする\)** ボックスをオンにします。

    e. **[保存]** をクリックします。

### <a name="create-cakehr-test-user"></a>CakeHR テスト ユーザーの作成

Azure AD ユーザーが CakeHR にサインインできるようにするには、ユーザーを CakeHR にプロビジョニングする必要があります。 CakeHR では、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. セキュリティ管理者として CakeHR にサインインします。

2. メニュー バーの左側で **[COMPANY]\(会社\)**  >  **[ADD]\(追加\)** の順にクリックします。

    ![このスクリーンショットは、[COMPANY]\(会社\) と [ADD]\(追加\) が選択された状態の CakeHR を示しています。](./media/cakehr-tutorial/config03.png)

3. **[Add new employee]\(新しい従業員の追加\)** ポップアップで、以下の手順を実行します。

     ![このスクリーンショットは、[Add new employee]\(新しい従業員の追加\) を示しています。ここで、これらの手順を実行します。](./media/cakehr-tutorial/config04.png)

    a. **[Full name]\(氏名\)** ボックスに、ユーザーの氏名を入力します (例: B.Simon)。

    b. **[Work email]\(仕事用メール\)** ボックスに、`B.Simon@contoso.com` など、ユーザーのメール アドレスを入力します。

    c. **[アカウントの作成]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [CakeHR] タイルをクリックすると、SSO を設定した CakeHR に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で CakeHR を試す](https://aad.portal.azure.com/)