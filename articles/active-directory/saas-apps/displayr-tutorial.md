---
title: チュートリアル:Azure Active Directory と Displayr の統合 | Microsoft Docs
description: Azure Active Directory と Displayr の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 3cb6ee3162c70d2d07c4868ae90ecc54bd489966
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98622493"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>チュートリアル:Displayr と Azure Active Directory との統合

このチュートリアルでは、Displayr と Azure Active Directory (Azure AD) を統合する方法について説明します。 Displayr を Azure AD に統合すると、次のことができます。

* Displayr にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Displayr に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Displayr でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Displayr では、**SP** によって開始される SSO がサポートされます

## <a name="adding-displayr-from-the-gallery"></a>ギャラリーからの Displayr の追加

Azure AD への Displayr の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Displayr を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに "**Displayr**" と入力します。
1. 結果のパネルから **[Displayr]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、Displayr で Azure AD の SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Displayr の関連ユーザーとの間にリンク関係を確立する必要があります。

Displayr で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Displayr の構成](#configure-displayr)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Displayr のテスト ユーザーの作成](#create-displayr-test-user)** - Displayr で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Displayr** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、`https://<YOURDOMAIN>.displayr.com` という形式で URL を入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`<YOURDOMAIN>.displayr.com`
    
    c. **[応答 URL]** ボックスに、「`https://app.displayr.com/Login/ProcessSamlResponse`」と入力します。
    
    d. **[保存]** をクリックします。

    >[!NOTE]
    >これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Displayr クライアント サポート チーム](mailto:support@displayr.com)に問い合わせてください。 Azure portal の [基本的な SAML 構成] セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして **証明書 (Base64)** をダウンロードし、コンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. Displayr アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

   ![[編集] アイコンが強調表示されている [ユーザー属性] セクションを示すスクリーンショット。](common/edit-attribute.png)

1. その他に、Displayr アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[グループ要求 (プレビュー)]** ダイアログの **[ユーザー属性とクレーム]** セクションで、次の手順を実行します。

   a. **[グループ要求を追加する]** をクリックします。

      ![各設定が選択されている [グループ要求 (プレビュー)] ウィンドウを示すスクリーンショット。](./media/displayr-tutorial/config05.png)

   b. ラジオ ボタンのリストから **[すべてのグループ]** を選択します。

   c. **[グループ ID]** の **[ソース属性]** を選択します。

   d. **[グループ要求の名前をカスタマイズする]** をオンにします。

   e. **[グループをロール要求として生成する]** をオンにします。

   f. **[保存]** をクリックします。

1. **[Displayr の設定]** セクションで、要件どおりの適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Displayr の構成

1. Displayr 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **マイアプリによるセキュリティで保護されたサインイン拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Displayr の設定]** をクリックすると、Displayr アプリケーションに移動します。 そこから、管理者資格情報を提供して Displayr にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Displayr を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Displayr の企業サイトにサインインして、次の手順を実行します。

4. **[設定]** をクリックし、 **[アカウント]** に移動します。

    ![[設定] アイコンと [アカウント] が選択されていることを示すスクリーンショット。](./media/displayr-tutorial/config01.png)

5. 上部のメニューから **[設定]** に切り替え、ページを下へスクロールして **[Configure Single Sign On (SAML)]\(シングル サインオンの構成 (SAML)\)** をクリックします。

    ![[設定] タブが選択され、[Configure Single Sign On (S A M L)]\(シングル サインオンの構成 (S A M L)\) アクションが選択されていることを示すスクリーンショット。](./media/displayr-tutorial/config02.png)

6. **[Single sign-on (SAML)]\(シングル サインオン (SAML)\)** ページで、次の手順に従います。

    ![構成](./media/displayr-tutorial/config03.png)

    a. **[Enable Single Sign On (SAML)]\(シングル サインオン (SAML) を有効にする\)** ボックスをオンにします。

    b. Azure AD の **[基本的な SAML 構成]** セクションから実際の **ID** 値をコピーして、 **[発行者]** テキスト ボックスに貼り付けます。

    c. **[ログイン URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[ログアウト URL]** ボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    e. 証明書 (Base64) をメモ帳で開き、その内容をコピーして **[証明書]** テキスト ボックスに貼り付けます。

    f. **グループ マッピング** は省略可能です。

    g. **[保存]** をクリックします。  

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

このセクションでは、Displayr へのアクセスを許可することで、Britta Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Displayr]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-displayr-test-user"></a>Displayr テスト ユーザーを作成する

Azure AD ユーザーが Displayr にサインインできるようにするには、ユーザーを Displayr にプロビジョニングする必要があります。 Displayr では、プロビジョニングは手動のタスクです。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. 管理者として Displayr にサインインします。

2. **[設定]** をクリックし、 **[アカウント]** に移動します。

    ![[アカウント] が選択されている [設定] (歯車) アイコンを示すスクリーンショット。](./media/displayr-tutorial/config01.png)

3. 上部のメニューから **[設定]** に切り替えて、 **[ユーザー]** セクションまでページを下にスクロールして、 **[新しいユーザー]** をクリックします。

    ![[ユーザー] が強調表示され、[新しいユーザー] ボタンが選択されている [設定] タブのスクリーンショット。](./media/displayr-tutorial/config07.png)

4. **[新しいユーザー]** ページで、次の手順を実行します。

    ![Displayr の構成](./media/displayr-tutorial/config06.png)

    a. **[名前]** ボックスに、ユーザーの名前を入力します (例: **Brittasimon**)。

    b. **[電子メール]** ボックスに、ユーザーのメール アドレスを入力します (例: `Brittasimon@contoso.com`)。

    c. 適切な **グループ メンバーシップ** を選択します。

    d. **[保存]** をクリックします。

### <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [Displayr] タイルを選択すると、SSO を設定した Displayr に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
