---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Mapbox の統合 | Microsoft Docs
description: Azure Active Directory と Mapbox の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: e74018d4b145f8dec44137ca1a3b0713b1b5cadd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728610"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mapbox"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Mapbox の統合

このチュートリアルでは、Mapbox と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Mapbox を統合すると、次のことができます。

* Mapbox にアクセスできるユーザーを Azure AD で制御できます。
* ご自分のユーザーが自分の Azure AD アカウントを使用して Mapbox に自動的にサインインするようにできます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。


## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Mapbox のシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Mapbox で **IDP** Initiated SSO がサポートされています

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-mapbox-from-the-gallery"></a>ギャラリーからの Mapbox の追加

Azure AD に Mapbox を統合するには、ギャラリーからご自分の管理対象 SaaS アプリの一覧に Mapbox を追加構成する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Mapbox**」と入力します。
1. 結果ウィンドウで **[Mapbox]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-mapbox"></a>Mapbox の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Mapbox に Azure AD SSO を構成してテストします。 SSO が機能するには、Azure AD ユーザーと Mapbox のその関連ユーザーとの間にリンク関係を確立する必要があります。

Mapbox に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Mapbox SSO の構成](#configure-mapbox-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Mapbox テスト ユーザーの作成](#create-mapbox-test-user)** - Mapbox で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Mapbox** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

1. Mapbox アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングをご自分の SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、Mapbox アプリケーションでは、いくつかの属性が SAML 応答で返されることを想定しています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前   |  ソース属性|
    | -----|--------- |
    | role | user.assignedroles |
    | | |

    > [!NOTE]
    > Azure AD でロールを構成する方法については、[こちら](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui--preview)を参照してください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (未加工)]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificateraw.png)

1. **[Mapbox のセットアップ]** セクションで、ご自分の要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Mapbox へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Mapbox]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. 前述のロールを設定した場合、 **[ロールの選択]** ボックスの一覧からそれを選択できます。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-mapbox-sso"></a>Mapbox SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として Mapbox にサインインします。

1. **[設定]** タブをクリックします。

    ![Mapbox の [Settings]\(設定\) タブ](./media/mapbox-tutorial/configure1.png)

1. 左側のナビゲーション ウィンドウで、 **[セキュリティ]** をクリックします。

    ![Mapbox の [Security]\(セキュリティ\) タブ](./media/mapbox-tutorial/configure2.png)

1. **[シングル サインオンの編集]** をクリックします。

    ![Mapbox の [Edit single sign-on]\(シングルサインオンの編集\)](./media/mapbox-tutorial/configure3.png)

1. **[手順 3:Setup SAML single sign-on for Mapbox]\(Mapbox に SAML シングル サインオンを設定する\)** までスクロールダウンし、次の手順を実行します。

    ![Mapbox の構成](./media/mapbox-tutorial/configure4.png)

    1. **[Idp Sign-on URL]\(Idp サインオン URL\)** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    1. **[発行者 ID]** テキストボックスに、Azure portal からコピーした、**Azure AD ID** の値を貼り付けます。

    1. Azure portal からダウンロードした **証明書 (未加工)** ファイルをメモ帳で開き、証明書ファイルの内容をコピーして **[X.509 証明書]** テキストボックスに貼り付けます。

    1. **[シングル サインオン設定の保存]** をクリックします。

### <a name="create-mapbox-test-user"></a>Mapbox テスト ユーザーの作成

このセクションでは、Mapbox に Britta Simon というユーザーを作成します。 [Mapbox サポート チーム](mailto:help@mapbox.com)と連携して、Mapbox プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した Mapbox に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Mapbox] タイルをクリックすると、SSO を設定した Mapbox に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Mapbox を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
