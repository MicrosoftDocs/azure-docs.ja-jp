---
title: 'チュートリアル: Azure Active Directory と Helper Helper の統合 | Microsoft Docs'
description: Azure Active Directory と Helper Helper の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.openlocfilehash: 0f85f3c747c634e0118ac516fc83284350fc2584
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92445023"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>チュートリアル: Helper Helper と Azure Active Directory の統合

このチュートリアルでは、Helper Helper と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Helper Helper を統合すると、次のことができます。

* Helper Helper にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して Helper Helper に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Helper Helper でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Helper Helper では、**SP と IDP** によって開始される SSO と、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="adding-helper-helper-from-the-gallery"></a>ギャラリーからの Helper Helper の追加

Azure AD への Helper Helper の統合を構成するには、管理対象の SaaS アプリ一覧に Helper Helper をギャラリーから追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Helper Helper**」と入力します。
1. 結果のパネルから **[Helper Helper]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B. Simon** というテスト ユーザーを使用して、Helper Helper に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Helper Helper の関連ユーザーとの間にリンク関係を確立する必要があります。

Helper Helper に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[Helper Helper の構成](#configure-helper-helper)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user)** - B. Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B. Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[Helper Helper のテスト ユーザーの作成](#create-helper-helper-test-user)** - Helper Helper で B. Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Helper Helper** アプリケーション統合ページで、**[管理]** セクションを探して、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **サービス プロバイダー メタデータ ファイル** を保持しており、**IDP** によって開始されるモードに構成したい場合は、 **[基本的な SAML 構成]** セクション上で次の手順を実行します。

    >[!NOTE]
    >URL `https://sso.helperhelper.com/saml/<customer_id>` に移動し、サービス プロバイダーのメタデータ ファイルを入手します。 `<customer_id>` については、[Helper Helper クライアント サポート チーム](mailto:info@helperhelper.com)にお問い合わせください。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    > [!Note]
    > **識別子** と **応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://sso.helperhelper.com/saml/<customer_id>/login` という形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 この値を実際のサインオン URL で更新してください。 この値を取得するには、[Helper Helper クライアント サポート チーム](mailto:info@helperhelper.com)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、メモ帳に保存します。

   ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

1. **[Set up Helper Helper] (Helper Helper の設定)** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Helper Helper の構成

**Helper Helper** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [Helper Helper サポート チーム](mailto:info@helperhelper.com)に送る必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

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

このセクションでは、B. Simon に Helper Helper へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Helper Helper]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B. Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-helper-helper-test-user"></a>Helper Helper のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを Helper Helper に作成します。 Helper Helper では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Helper Helper にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Helper Helper] タイルを選択すると、SSO を設定した Helper Helper に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)