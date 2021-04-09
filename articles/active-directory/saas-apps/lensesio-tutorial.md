---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Lenses.io の統合 | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory と Lenses.io の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 181d58baf128c4848a538e776aea0e43213994dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458610"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Lenses.io DataOps ポータルの統合

このチュートリアルでは、[Lenses.io](https://lenses.io/) DataOps ポータルと Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Lenses.io を統合すると、次のことができます。

* Lenses.io ポータルにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Lenses に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure AD のアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Lenses ポータルのインスタンス。 多数の[デプロイ オプション](https://lenses.io/product/deployment/)から選択できます。
* シングル サインオン (SSO) をサポートする Lenses.io [ライセンス](https://lenses.io/product/pricing/)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Lenses.io では、サービス プロバイダー (SP) Initiated SSO がサポートされます。

* Lenses.io を構成した後で、セッション制御を適用できます。 セッション制御により、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-lensesio-from-the-gallery"></a>ギャラリーからの Lenses.io の追加

Azure AD への Lenses.io の統合を構成するには、マネージド SaaS アプリの一覧に Lenses.io を追加します。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Lenses.io**」と入力します。
1. 結果のパネルから **[Lenses.io]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Lenses.io の Azure AD SSO の構成とテスト

*B.Simon* というテスト ユーザーを作成して、Lenses.io ポータルに対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Lenses.io の関連ユーザーとの間にリンク関係を確立する必要があります。

次の手順のようにします。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    1. [Azure AD テスト ユーザーとグループを作成](#create-an-azure-ad-test-user-and-group)し、B.Simon を使用して Azure AD SSO をテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)、B.Simon が Azure AD SSO を使用できるようにします。
1. [Lenses.io SSO を構成](#configure-lensesio-sso)して、アプリケーション側で SSO 設定を構成します。
    1. [Lenses.io テスト グループのアクセス許可を作成](#create-lensesio-test-group-permissions)して、Lenses.io で B.Simon がアクセスできる内容を制御します (認可)。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Lenses.io** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンを選択して設定を編集します。

   ![基本的な SAML 構成を編集するためのアイコンを示すスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のテキスト入力ボックスに値を入力します。

    a. **サインオン URL**: `https://<CUSTOMER_LENSES_BASE_URL>` の形式で URL を入力します。 たとえば `https://lenses.my.company.com` です。

    b. **識別子 (エンティティ ID)** : `https://<CUSTOMER_LENSES_BASE_URL>` の形式で URL を入力します。 たとえば `https://lenses.my.company.com` です。

    c. **応答 URL**:`https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client` の形式で URL を入力します。 たとえば `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client` です。

    > [!NOTE]
    > これらは実際の値ではありません。 これらは、お使いの Lenses ポータル インスタンスのベース URL を使用した実際のサインオン URL、応答 URL、および識別子で更新します。 詳細については、[Lenses.io SSO のドキュメント](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)を参照してください。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[SAML 署名証明書]** セクションに移動します。 **[フェデレーション メタデータ XML]** を見つけ、 **[ダウンロード]** を選択して、証明書をダウンロードし、お使いのコンピューターに保存します。

    ![証明書のダウンロード リンクを示すスクリーンショット。](common/metadataxml.png)

1. **[Lenses.io のセットアップ]** セクションで、ダウンロードした XML ファイルを使用して、Azure SSO に対して Lenses を構成します。

### <a name="create-an-azure-ad-test-user-and-group"></a>Azure AD のテスト ユーザーおよびグループの作成

Azure portal で、B.Simon というテスト ユーザーを作成します。 次に、B.Simon が Lenses で持つアクセス権を制御するテスト グループを作成します。

Lenses でグループ メンバーシップのマッピングを使用して認可を行う方法については、[Lenses SSO のドキュメント](https://docs.lenses.io/install_setup/configuration/security.html#id3)を参照してください。

**テスト ユーザーを作成するには、次のようにします。**

1. Azure portal の左側のペインで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** ボックスに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** ボックスに、「username@companydomain.extension」と入力します。 たとえば、「 B.Simon@contoso.com 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスを選択します。 **[パスワード]** ボックスに表示されるパスワードを書き留めます。
   1. **［作成］** を選択します

**グループを作成するには、次のようにします。**

1. **[Azure Active Directory]** に移動し、 **[グループ]** を選択します。
1. 画面の上部で、 **[新しいグループ]** を選択します。
1. **[グループのプロパティ]** で、以下の手順を実行します。
   1. **[グループの種類]** ボックスで **[セキュリティ]** を選択します。
   1. **[グループ名]** ボックスに、「**LensesUsers**」と入力します。
   1. **［作成］** を選択します
1. グループ **LensesUsers** を選択し、**オブジェクト ID** (たとえば、f8b5c1ec-45de-4abd-af5c-e874091fb5f7) をコピーします。 この ID は、このグループのユーザーを[適切なアクセス許可](https://docs.lenses.io/install_setup/configuration/security.html#id3)にマップするために、Lenses で使用します。  

**テスト ユーザーにグループを割り当てるには、次のようにします。**

1. **[Azure Active Directory]** に移動し、 **[ユーザー]** を選択します。
1. テスト ユーザー **B.Simon** を選択します。
1. **[グループ]** を選びます。
1. 画面の上部で、 **[メンバーシップの追加]** を選択します。
1. **LensesUsers** を検索して選択します。
1. **[選択]** をクリックします。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Lenses.io へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Lenses.io]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンクを示すスクリーンショット。](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選びます。

   ![[ユーザーの追加] リンクを示すスクリーンショット。](common/add-assign-user.png)

1. **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンをクリックします。
1. **[割り当ての追加]** ダイアログ ボックスで、 **[割り当て]** ボタンを選択します。

## <a name="configure-lensesio-sso"></a>Lenses.io の SSO の構成

**Lenses.io** ポータルで SSO を構成するには、ダウンロードした **フェデレーション メタデータ XML** を Lenses インスタンスにインストールし、[SSO が有効になるように Lenses を構成します](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses)。

### <a name="create-lensesio-test-group-permissions"></a>Lenses.io テスト グループのアクセス許可を作成する

1. Lenses でグループを作成するには、**LensesUsers** グループの **オブジェクト ID** を使用します。 これは、ユーザーの[作成セクション](#create-an-azure-ad-test-user-and-group)でコピーした ID です。
1. B.Simon に必要なアクセス許可を割り当てます。

詳細については、[Azure - Lenses グループ マッピング](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups)に関する記述を参照してください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD SSO の構成をテストします。

アクセス パネル上で [Lenses.io] タイルを選択すると、Lenses.io ポータルに自動的にサインインします。 詳細については、「[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)」を参照してください。

## <a name="additional-resources"></a>その他の技術情報

- [Lenses.io インスタンスで SSO をセットアップする](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0)

- [SaaS アプリと Azure AD を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure AD のアプリケーション アクセスと SSO とは](../manage-apps/what-is-single-sign-on.md)

- [Azure AD の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Lenses.io を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Lenses.io を保護する方法](/cloud-app-security/proxy-intro-aad)