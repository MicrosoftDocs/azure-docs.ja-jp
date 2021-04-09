---
title: チュートリアル:Azure Active Directory と Mind Tools Toolkit の統合 | Microsoft Docs
description: Azure Active Directory と Mind Tools Toolkit の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2020
ms.author: jeedes
ms.openlocfilehash: 57598ea22874cfba772f6cf0e6b2ba779ef7c482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92448420"
---
# <a name="tutorial-azure-active-directory-integration-with-mind-tools-toolkit"></a>チュートリアル:Azure Active Directory と Mind Tools Toolkit の統合

このチュートリアルでは、Mind Tools Toolkit と Azure Active Directory (Azure AD) を統合する方法について説明します。

この統合により、次のことができます。

* Mind Tools Toolkit にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Mind Tools Toolkitt に自動的にサインイン (シングル サインオン) するように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](../manage-apps/what-is-single-sign-on.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

Mind Tools Toolkit と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Mind Tools Toolkit サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Mind Tools Toolkit では、SP Initiated SSO がサポートされます。
* Mind Tools Toolkit では、Just-In-Time ユーザー プロビジョニングがサポートされます。
* Mind Tools Toolkit を構成した後、セッション制御を適用できます。 この制御により、組織の機密データを流出と侵入からリアルタイムで保護することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="add-mind-tools-toolkit-from-the-gallery"></a>ギャラリーからの Mind Tools Toolkit の追加

Azure AD への Mind Tools Toolkit の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Mind Tools Toolkit を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左端のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Mind Tools Toolkit**」と入力します。
1. 検索結果から **[Mind Tools Toolkit]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**B.Simon** というテスト ユーザーを使用して、Mind Tools Toolkit で Azure AD のシングル サインオンを構成し、テストします。 シングル サインオンを機能させるには、Azure AD ユーザーと Mind Tools Toolkit 内の関連ユーザーとの間にリンク関係を確立する必要があります。

Mind Tools Toolkit で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Mind Tools Toolkit の SSO の構成](#configure-mind-tools-toolkit-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Mind Tools Toolkit のテスト ユーザーの作成](#create-a-mind-tools-toolkit-test-user)** - Mind Tools Toolkit で B.Simon に対応するユーザーを作成します。 このユーザーを Azure AD の対応するユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、次の手順に従って、Mind Tools Toolkit で Azure AD シングル サインオンを構成します。

1. [Azure portal](https://portal.azure.com/) の **Mind Tools Toolkit** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![[シングル サインオン] が強調表示された [管理] セクション](common/select-sso.png)

1. **[シングル サインオン方式の選択]** ダイアログ ボックスで、 **[SAML/WS-Fed]** を選択して、シングル サインオンを有効にします。

    ![[SAML] が強調表示された [シングル サインオン方式の選択] ダイアログ ボックス](common/select-saml-option.png)

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

    ![[基本的な SAML 構成] の鉛筆アイコンが強調表示された [SAML によるシングル サインオンのセットアップ] ページ](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに、`https://app.goodpractice.net/#/<subscriptionUrl>/s/<locationId>` というパターンの URL を入力します。

    > [!NOTE]
    > **サインオン URL** は、実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Mind Tools Toolkit クライアント サポート チーム](mailto:support@goodpractice.com)にお問い合わせください。

1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[SAML 署名証明書]** セクションに移動します。 **[フェデレーション メタデータ XML]** の右側にある **[ダウンロード]** を選択して、XML テキストをダウンロードし、お使いのコンピューターに保存します。 XML コンテンツは、選択したオプションによって異なります。

    ![[フェデレーション メタデータ XML] の横にある [ダウンロード] が強調表示された [SAML 署名証明書] セクション](common/metadataxml.png)

1. **[Mind Tools Toolkit のセットアップ]** セクションで、次の URL のうち必要なものをコピーします。

    * **ログイン URL**

    * **Azure AD 識別子**

    * **ログアウト URL**

    ![構成 URL が強調表示された [Mind Tools Toolkit のセットアップ] セクション](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左端で、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「**B.Simon**」と入力します。  
   1. **[ユーザー名]** フィールドに、「**B.Simon@** _companydomain_ **.** _extension_」と入力します。 たとえば、「 B.Simon@contoso.com 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Mind Tools Toolkit へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Mind Tools Toolkit]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションに移動し、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] が強調表示された [管理] セクション](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

   ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ウィンドウ](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** ボタンを選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-mind-tools-toolkit-sso"></a>Mind Tools Toolkit の SSO の構成

**Mind Tools Toolkit** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** テキストと以前にコピーした URL を [Mind Tools Toolkit サポート チーム](mailto:support@goodpractice.com)に送信します。 この設定が構成され、SAML SSO 接続が両側で正しく行われます。

### <a name="create-a-mind-tools-toolkit-test-user"></a>Mind Tools Toolkit のテスト ユーザーの作成

このセクションでは、Mind Tools Toolkit で B.Simon というユーザーを作成します。

Mind Tools Toolkit では、Just-In-Time プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションには、ユーザー側で行うアクションはありません。 ユーザーがまだ Mind Tools Toolkit に存在していない場合、Mind Tools Toolkit にアクセスしようとしたときに新しいユーザーが作成されます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、マイ アプリ ポータルを使用して自分の Azure AD のシングル サインオン構成をテストします。

マイ アプリ ポータルで [Mind Tools Toolkit] タイルを選択すると、SSO を設定した Mind Tools Toolkit に自動的にサインインします。 マイ アプリ ポータルの詳細については、[マイ アプリ ポータルの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory の統合に関するチュートリアル](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Mind Tools Toolkit を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Mind Tools Toolkit を保護する方法](/cloud-app-security/proxy-intro-aad)