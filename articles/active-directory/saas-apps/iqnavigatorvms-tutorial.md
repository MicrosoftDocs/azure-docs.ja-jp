---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と IQNavigator VMS の統合 | Microsoft Docs
description: Azure Active Directory と IQNavigator VMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: jeedes
ms.openlocfilehash: beef52641b9744b39873a7f48e39eb2f94ed4c9f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92459732"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iqnavigator-vms"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と IQNavigator VMS の統合

このチュートリアルでは、IQNavigator VMS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と IQNavigator VMS を統合すると、次のことができます。

* IQNavigator VMS にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して IQNavigator VMS に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* IQNavigator VMS でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。


* IQNavigator VMS では、**IDP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。



## <a name="adding-iqnavigator-vms-from-the-gallery"></a>ギャラリーからの IQNavigator VMS の追加

Azure AD への IQNavigator VMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に IQNavigator VMS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**IQNavigator VMS**」と入力します。
1. 結果のパネルから **IQNavigator VMS** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-iqnavigator-vms"></a>IQNavigator VMS の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、IQNavigator VMS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと IQNavigator VMS の関連ユーザーとの間にリンク関係を確立する必要があります。

IQNavigator VMS で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[IQNavigator VMS の SSO の構成](#configure-iqnavigator-vms-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[IQNavigator VMS テスト ユーザーの作成](#create-iqnavigator-vms-test-user)** - Azure AD の B.Simon にリンクさせるために、対応するユーザーを IQNavigator VMS で作成します。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **IQNavigator VMS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[識別子]** テキスト ボックスに、`iqn.com` という URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>` のパターンを使用して URL を入力します

    c. **[追加の URL を設定します]** をクリックします。

    d. **[リレー状態]** ボックスに、`https://<subdomain>.iqnavigator.com` のパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際の応答 URL とリレー状態で更新してください。 これらの値を取得するには、[IQNavigator VMS クライアント サポート チーム](https://www.beeline.com/support-iqn/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. IQNavigator アプリケーションでは、名前識別子の要求で一意のユーザー識別子の値が必要です。 顧客は、名前識別子要求の適切な値をマップできます。 ここでは、デモのために user.UserPrincipalName をマップしました。 ただし、組織の設定に従って、正しい値をマップする必要があります。

    ![image](common/edit-attribute.png)

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に IQNavigator VMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[IQNavigator VMS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-iqnavigator-vms-sso"></a>IQNavigator VMS の SSO の構成

**IQNavigator VMS** 側でシングル サインオンを構成するには、**アプリのフェデレーション メタデータ URL** を [IQNavigator VMS サポート チーム](https://www.beeline.com/support-iqn/)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-iqnavigator-vms-test-user"></a>IQNavigator VMS テスト ユーザーの作成

このセクションでは、IQNavigator VMS で Britta Simon というユーザーを作成します。 [IQNavigator VMS サポート チーム](https://www.beeline.com/support-iqn/)と連携して、IQNavigator VMS プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [IQNavigator VMS] タイルをクリックすると、SSO を設定した IQNavigator VMS に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で IQNavigator VMS を試す](https://aad.portal.azure.com/)