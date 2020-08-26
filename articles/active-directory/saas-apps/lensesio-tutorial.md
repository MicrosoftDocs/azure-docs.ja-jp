---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Lenses.io の統合 | Microsoft Docs
description: Azure Active Directory と Lenses.io の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/02/2020
ms.author: jeedes
ms.openlocfilehash: 0b8d736ab169ad07bd23a21d3a420bb6a044bf01
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528641"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Lenses.io DataOps ポータルの統合

このチュートリアルでは、[Lenses.io](https://lenses.io/) DataOps ポータルと Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Lenses.io を統合すると、次のことができます。

* Lenses.io ポータルにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Lenses に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Lenses ポータルのインスタンス。 Lenses ポータルは、[さまざまな方法](https://lenses.io/product/deployment/)でデプロイできます。
* シングル サインオン (SSO) をサポートする Lenses.io [ライセンス](https://lenses.io/product/pricing/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Lenses.io では、**SP** Initiated SSO がサポートされます

* Lenses.io を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-lensesio-from-the-gallery"></a>ギャラリーからの Lenses.io の追加

Azure AD への Lenses.io の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Lenses.io を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Lenses.io**」と入力します。
1. 結果のパネルから **[Lenses.io]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Lenses.io の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Lenses.io ポータルに対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Lenses.io の関連ユーザーとの間にリンク関係を確立する必要があります。

Lenses.io で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーおよびグループの作成](#create-an-azure-ad-test-user-and-group)** - B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Lenses.io の SSO の構成](#configure-lensesio-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Lenses.io テスト グループのアクセス許可の作成](#create-lensesio-test-group-permissions)** - Lenses.io で B.Simon がアクセスできる内容を制御します (承認)。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Lenses.io** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<CUSTOMER_LENSES_BASE_URL>` のパターンを使用して URL を入力します (例: `https://lenses.my.company.com`)

    b. **[識別子 (エンティティ ID)]** ボックスに、`https://<CUSTOMER_LENSES_BASE_URL>` のパターンを使用して URL を入力します (例: `https://lenses.my.company.com`)

    c. **[応答 URL]** ボックスに、`https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` のパターンを使用して URL を入力します
    例: `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`

    > [!NOTE]
    > これらは実際の値ではありません。 お使いの Lenses ポータル インスタンスのベース URL に基づいて、これらの値を実際のサインオン URL、応答 URL、および識別子で更新します。 詳細については、[Lenses.io SSO のドキュメント](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)を参照してください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Lenses.io のセットアップ]** セクションで、上記の XML ファイルを使用して、Azure SSO に対して Lenses を構成します。

### <a name="create-an-azure-ad-test-user-and-group"></a>Azure AD のテスト ユーザーおよびグループの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。 また、B.Simon のテスト グループも作成します。これは、Lenses 内での B.Simon のアクセス権を制御するために使用します。
Lenses でグループ メンバーシップのマッピングを使用して承認を行う方法については、[Lenses SSO のドキュメント](https://docs.lenses.io/install_setup/configuration/security.html#id3)を参照してください

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

グループを作成するには、次のようにします。
1. **[Azure Active Directory]** に戻り、 **[グループ]** を選択します
1. 画面の上部にある **[新しいグループ]** を選択します。
1. **[グループのプロパティ]** で、以下の手順を実行します。
   1. **[グループの種類]** フィールドで、`Security` を選択します。
   1. **[グループ名]** フィールドに「`LensesUsers`」と入力します
   1. **Create** をクリックしてください。
1. グループ `LensesUsers` を選択し、 **[オブジェクト ID]** (例: `f8b5c1ec-45de-4abd-af5c-e874091fb5f7`) を書き留めます。 この ID は、このグループのユーザーを[適切なアクセス許可](https://docs.lenses.io/install_setup/configuration/security.html#id3)にマップするために、Lenses で使用します。  
   
テスト ユーザーにグループを割り当てるには、次のようにします。 
1. **[Azure Active Directory]** に戻り、 **[ユーザー]** を選択します。
1. テスト ユーザー `B.Simon` を選択します。
1. **[グループ]** を選びます。
1. 画面の上部にある **[メンバーシップの追加]** を選択します。
1. `LensesUsers` を検索して選択します。
1. **[選択]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Lenses.io へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Lenses.io]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-lensesio-sso"></a>Lenses.io の SSO の構成

**Lenses.io** ポータルでシングル サインオンを構成するには、ダウンロードした**フェデレーション メタデータ XML** を Lenses インスタンスにインストールし、[SSO が有効になるように Lenses を構成します](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses)。 

### <a name="create-lensesio-test-group-permissions"></a>Lenses.io テスト グループのアクセス許可を作成する

このセクションでは、ユーザーの[作成セクション](#create-an-azure-ad-test-user-and-group)でメモした `LensesUsers` グループの**オブジェクト ID** を使用して、Lenses にグループを作成します。
`B.Simon` が Lenses 内で必要とするアクセス許可を割り当てます。
詳細については、[Azure と Lenses のグループのマッピング](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)に関するページを参照してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Lenses.io] タイルをクリックすると、SSO を設定した Lenses.io ポータルに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [Lenses.io インスタンスで SSO をセットアップする](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Lenses.io を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Lenses.io を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
