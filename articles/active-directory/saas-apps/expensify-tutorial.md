---
title: 'チュートリアル: Azure Active Directory と Expensify の統合 | Microsoft Docs'
description: Azure Active Directory と Expensify の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1e761484-7a2f-4321-91f4-6d5d0b69344e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe57c0655cf01f8dfa0f9cd0d75584fd4f130c0a
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976101"
---
# <a name="tutorial-integrate-expensify-with-azure-active-directory"></a>チュートリアル:Expensify と Azure Active Directory の統合

このチュートリアルでは、Expensify と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Expensify を統合すると、次のことができます。

* Expensify にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Expensify に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Expensify でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Expensify では、**SP** Initiated SSO がサポートされます

## <a name="adding-expensify-from-the-gallery"></a>ギャラリーからの Expensify の追加

Azure AD への Expensify の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Expensify を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Expensify**」と入力します。
1. 結果ウィンドウで **[Expensify]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-expensify"></a>Expensify の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Expensify に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Expensify の関連ユーザーとの間にリンク関係を確立する必要があります。

Expensify で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Expensify SSO の構成](#configure-expensify-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Expensify のテスト ユーザーの作成](#create-expensify-test-user)** - Expensify で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Expensify** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに URL として「`https://www.expensify.com/authentication/saml/login`」と入力します。

    b. **[識別子 (エンティティ ID)]** ボックスに、`https://www.expensify.com` という URL を入力します。

    c. b. **[応答 URL]** ボックスに、`https://www.expensify.com/authentication/saml/loginCallback?domain=<yourdomain>` のパターンを使用して URL を入力します

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Expensify クライアント サポート チーム](mailto:help@expensify.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Expensify のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Expensify へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Expensify]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-expensify-sso"></a>Expensify SSO の構成

Expensify で SSO を有効にするには、まず、アプリケーションで **Domain Control** を有効にする必要があります。 Domain Control は、[こちら](https://help.expensify.com/domain-control)で説明されている手順を使ってアプリケーションで有効にできます。 さらにサポートが必要な場合は、[Expensify クライアント サポート チーム](mailto:help@expensify.com)に問い合わせてください。 Domain Control を有効にしたら、以下の手順に従います。

![Configure single sign-on](./media/expensify-tutorial/tutorial_expensify_51.png)

1. Expensify アプリケーションにサインオンします。

2. 左側のパネルで **[設定]** をクリックし、 **[SAML]** に移動します。

3. **[SAML ログイン]** オプションを **[有効]** に切り替えます。

4. Azure AD からダウンロードしたフェデレーション メタデータをメモ帳で開き、その内容をコピーして、 **[Identity Provider Metadata]\(ID プロバイダー メタデータ\)** ボックスに貼り付けます。

### <a name="create-expensify-test-user"></a>Expensify のテスト ユーザーの作成

このセクションでは、Expensify で B.Simon というユーザーを作成します。 [Expensify クライアント サポート チーム](mailto:help@expensify.com)と協力して、Expensify プラットフォームにユーザーを追加します。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Expensify] タイルをクリックすると、SSO を設定した Expensify に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)