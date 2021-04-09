---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Splan Visitor を統合する | Microsoft Docs
description: Azure Active Directory と Splan Visitor の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/14/2020
ms.author: jeedes
ms.openlocfilehash: 20f49c174dde90bc7f1a9b34f3dea3132e9b177e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644699"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Splan Visitor を統合する

このチュートリアルでは、Splan Visitor と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Splan Visitor を統合すると、次のことができます。

* Azure AD を使用して Splan Visitor にアクセスできるユーザーを管理できます。
* ユーザーが自分の Azure AD アカウントを使用して Splan Visitor に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Splan Visitor でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

Splan Visitor では、IdP Initiated SSO がサポートされます。

## <a name="add-splan-visitor-from-the-gallery"></a>ギャラリーからの Splan Visitor の追加

Azure AD への Splan Visitor の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Splan Visitor を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Splan Visitor**」と入力します。
1. 結果のパネルから **Splan Visitor** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Splan Visitor の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Splan Visitor に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Splan Visitor の関連ユーザーとの間にリンク関係を確立する必要があります。

Splan Visitor に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - テスト ユーザー B.Simon を使用して Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Splan Visitor の SSO の構成](#configure-splan-visitor-sso)** - Splan Visitor でシングル サインオン設定を構成します。
    1. **[Splan Visitor のテスト ユーザーの作成](#create-a-splan-visitor-test-user)** - Splan Visitor で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Splan Visitor** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の **編集 (ペン)** アイコンを選択して設定を編集します。

   ![[基本的な SAML 構成] の編集 (ペン) アイコンが強調表示されたスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションが事前に構成されており、必要な URL が Azure で事前に設定されています。 **[保存]** ボタンを選択して、構成を保存します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![[フェデレーション メタデータ XML] のダウンロード リンクが強調表示されたスクリーンショット。](common/metadataxml.png)

1. **[Splan Visitor のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![URL の構成セクションが強調表示されたスクリーンショット。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のペインで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** フィールドに、 _username@companydomain.extension_ の形式でユーザー名を入力します。 たとえば、「 **B.Simon@contoso.com** 」と入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Splan Visitor へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Splan Visitor]** を選択して、アプリの概要を開きます。
1. **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスの **[ユーザー]** の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** をクリックします。
1. ユーザーにロールを割り当てる場合は、 **[ロールの選択]** ドロップダウン メニューから選択します。 このアプリに対してロールが設定されていない場合は、 **[既定のアクセス]** ロールが選択されている状態のままにします。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-splan-visitor-sso"></a>Splan Visitor の SSO の構成

Splan Visitor でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と、Azure portal からコピーした適切な URL を [Splan Visitor サポート チーム](mailto:support@splan.com)に送信する必要があります。 これにより、SAML SSO 接続が両方の側で確実に正しく設定されます。

### <a name="create-a-splan-visitor-test-user"></a>Splan Visitor のテスト ユーザーの作成

Splan Visitor で **Britta Simon** というテスト ユーザーを作成します。 [Splan Visitor サポート チーム](mailto:support@splan.com)と連携して、Splan Visitor にユーザーを追加します。 シングル サインオンを使用するには、ユーザーを作成し、有効にする必要があります。

## <a name="test-sso"></a>SSO のテスト

次のオプションのいずれかを使用して Azure AD のシングル サインオン構成をテストします。

* **Azure ポータル**: **[このアプリケーションをテストします]** を選択すると、SSO を設定する Splan Visitor に自動的にサインインします。
* **Microsoft マイ アプリ ポータル**: **[Splan Visitor]** タイルを選択すると、SSO を設定する Splan Visitor に自動的にサインインします。 マイ アプリ ポータルの詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Splan Visitor を構成したら、[Microsoft Cloud App Security でセッション制御を強制する方法を確認](/cloud-app-security/proxy-deployment-any-app)できます。 セッション制御により、組織の機密データの流出と侵入をリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。