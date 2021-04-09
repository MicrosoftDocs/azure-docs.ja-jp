---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Raketa の統合 | Microsoft Docs
description: Azure Active Directory と Raketa の間でシングル サインオンを構成する方法について確認します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 649a26e474c7c4d6b4f51a035b3f8f9da35b9dd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511156"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Raketa の統合

このチュートリアルでは、Raketa と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Raketa を統合すると、次のことができます。

* Raketa にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Raketa に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Raketa でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Raketa では、**SP** によって開始される SSO がサポートされます。
* Raketa を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-raketa-from-the-gallery"></a>ギャラリーからの Raketa の追加

Azure AD への Raketa の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Raketa を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービス (1) を選択します。

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. **[エンタープライズ アプリケーション]** (2) に移動し、 **[すべてのアプリケーション]** (3) を選択します。

1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** (4) を選択します。 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. **[ギャラリーから追加する]** (5) セクションで、検索ボックスに「**Raketa**」と入力します (6)。

1. 結果パネルから **Raketa** を選択し (7)、 **[追加]** ボタン (8) をクリックします。 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Raketa の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Raketa に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Raketa の関連ユーザーとの間にリンク関係を確立する必要があります。

Raketa で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Raketa SSO の構成](#configure-raketa-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Raketa テスト ユーザーの作成](#create-raketa-test-user)** - Raketa で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Raketa** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** (9) を選択します。

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. **[シングル サインオン方式の選択]** ページで (9)、 **[SAML]** (10) を選択します。

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** (11) の編集 (ペン) アイコンをクリックして設定を編集します。

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    1. **[識別子 (エンティティ ID)]** (12) および **[サインオン URL]** (14) テキスト ボックスに、URL `https://raketa.travel/` を入力します。

    1. **[応答 URL]** テキスト ボックス (13) に、パターン `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>` を使用して URL を入力します。  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 この値を取得するには、[Raketa クライアント サポート チーム](mailto:help@raketa.travel)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** (15) を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

1. **[Raketa のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    1. [ログイン URL] (16) – 認証システムにユーザーをリダイレクトするために使用される承認 Web ページの URL。

    1. [Azure AD 識別子] (17) – Azure AD の識別子。

    1. [ログアウト URL] (18) – ログアウト後にユーザーをリダイレクトするために使用される Web ページの URL。

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** (1)、 **[ユーザー]** (19)、 **[すべてのユーザー]** (20) の順に選択します。

1. 画面の上部にある **[新しいユーザー]** (21) を選択します。

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. **[ユーザー]** プロパティで、以下の手順を実行します。

   1. **[ユーザー名]** フィールド (22) に「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。

   1. **[名前]** フィールド (23) に「`B.Simon`」と入力します。

   1. **[パスワードを表示]** チェック ボックス (25) をオンにし、 **[パスワード]** ボックス (24) に表示された値を記録しておきます。

   1. **[作成]** (26) をクリックします。 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Raketa へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** (2) を選択し、 **[すべてのアプリケーション]** (3) を選択します。

1. アプリケーションの一覧で **Raketa** (27) を選択します。  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** (28) を選択します。 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. **[ユーザーの追加]** (29) を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** (30) を選択します。

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **B.Simon** (31) を選択し、画面の下部にある **[選択]** (32) ボタンをクリックします。

1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。

1. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタン (33) をクリックします。 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Raketa SSO の構成

**Raketa** 側でシングル サインオンを構成するには、ダウンロードした **証明書 (Base64)** と Azure portal からコピーした適切な URL を [Raketa サポート チーム](mailto:help@raketa.travel)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-raketa-test-user"></a>Raketa テスト ユーザーの作成

このセクションでは、Raketa で B.Simon というユーザーを作成します。 [Raketa サポート チーム](mailto:help@raketa.travel)と協力して、Raketa プラットフォームにユーザーを追加します。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Raketa] タイルをクリックすると、SSO を設定した Raketa に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Raketa を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Raketa を保護する方法](/cloud-app-security/proxy-intro-aad)