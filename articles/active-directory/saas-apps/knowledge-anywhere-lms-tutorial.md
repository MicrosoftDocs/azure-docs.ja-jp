---
title: チュートリアル:Azure Active Directory と Knowledge Anywhere LMS の統合 | Microsoft Docs
description: Azure Active Directory と Knowledge Anywhere LMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: eab1304408f896f5c9d736488997c1246ae167f2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458984"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と Knowledge Anywhere LMS の統合

このチュートリアルでは、Knowledge Anywhere LMS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Knowledge Anywhere LMS を統合すると、次のことができます。

* Knowledge Anywhere LMS にアクセスできるユーザーを Azure AD で管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Knowledge Anywhere LMS に自動的にサインインできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Knowledge Anywhere LMS サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Knowledge Anywhere LMS では、**SP** Initiated SSO と **Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>ギャラリーからの Knowledge Anywhere LMS の追加

Azure AD への Knowledge Anywhere LMS の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Knowledge Anywhere LMS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Knowledge Anywhere LMS**」と入力します。
1. 結果のパネルから **[Knowledge Anywhere LMS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B. Simon** というテスト ユーザーを使用して、Knowledge Anywhere LMS に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと Knowledge Anywhere LMS の関連ユーザーの間で、リンク関係を確立する必要があります。

Knowledge Anywhere LMS に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Knowledge Anywhere LMS の構成](#configure-knowledge-anywhere-lms)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user)** - B. Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B. Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Knowledge Anywhere LMS のテスト ユーザーの作成](#create-knowledge-anywhere-lms-test-user)** - Knowledge Anywhere LMS で B. Simon に対応するユーザーを作成し、Azure AD の B. Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Knowledge Anywhere LMS** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    1. **[識別子]** ボックスに、`https://<CLIENTNAME>.knowledgeanywhere.com/` の形式で URL を入力します。

    1. **[応答 URL]** ボックスに、`https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子と応答 URL に値を置き換えます。実際の値については後で説明します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<CLIENTNAME>.knowledgeanywhere.com/` という形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[Knowledge Anywhere LMS クライアント サポート チーム](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Set up Knowledge Anywhere LMS]\(Knowledge Anywhere LMS の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Knowledge Anywhere LMS の構成

1. Knowledge Anywhere LMS 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Setup Knowledge Anywhere LMS]\(Knowledge Anywhere LMS のセットアップ\)** をクリックすると、Knowledge Anywhere LMS アプリケーションに移動します。 そこから、管理者資格情報を入力して Knowledge Anywhere LMS にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Knowledge Anywhere LMS を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者としてご自分の Knowledge Anywhere LMS 企業サイトにサインインして、次の手順を実行します。

4. **[Site]\(サイト\)** タブを選択します。

    ![[Site]\(サイト\) タブを示すスクリーンショット。](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. **[SAML Settings]\(SAML 設定\)** タブを選択します。

    ![[SAML Settings]\(SAML 設定\) が選択されている [Knowledge anywhere] ページが示されているスクリーンショット。](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. **[Add New]\(新規追加\)** をクリックします。

    ![[Service Provider Settings]\(サービス プロバイダーの設定\) の [Add New]\(新規追加\) ボタンを示すスクリーンショット。](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. **[Add/Update SAML Settings]\(SAML 設定の追加/更新\)** ページで、次の手順を実行します。

    ![ここで説明されている変更を行うことができる [Add/Update SAML Settings]\(SAML 設定の追加/更新\) ページを示すスクリーンショット。](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. 所属する組織に応じて [IDP Name]\(IDP 名\) に入力します (例: `Azure`)。

    b. **[IDP Entity ID]\(IDP エンティティ ID\)** ボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。

    c. **[IDP URL]\(IDP URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    d. Azure Portal からダウンロードした証明書ファイルをメモ帳で開き、その内容をコピーして **[Certificate]\(証明書\)** ボックスに貼り付けます。

    e. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

    f. **[Domain]\(ドメイン\)** には、ボックスの一覧から **[Main Site]\(メイン サイト\)** を選択します。

    g. **[SP Entity ID]\(SP エンティティ ID\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[識別子]** ボックスに貼り付けます。

    h. **[SP Response(ACS) URL]\(SP 応答 (ACS) URL\)** の値をコピーして、Azure portal の **[基本的な SAML 構成]** セクションにある **[応答 URL]** ボックスに貼り付けます。

    i. **[保存]** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B. Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Knowledge Anywhere LMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Knowledge Anywhere LMS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B. Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-knowledge-anywhere-lms-test-user"></a>Knowledge Anywhere LMS のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Knowledge Anywhere LMS に作成します。 Knowledge Anywhere LMS では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Knowledge Anywhere LMS にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネル上で [Knowledge Anywhere LMS] タイルを選択すると、SSO を設定した Knowledge Anywhere LMS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)