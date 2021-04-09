---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と PerimeterX の統合 | Microsoft Docs
description: Azure Active Directory と PerimeterX の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/27/2020
ms.author: jeedes
ms.openlocfilehash: 29ffaaa1b1b6efbcd5523a76018c92645e13d187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181800"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-perimeterx"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と PerimeterX の統合

このチュートリアルでは、PerimeterX と Azure Active Directory (Azure AD) を統合する方法について説明します。 PerimeterX と Azure AD を統合すると、次のことができます。

* PerimeterX にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して PerimeterX に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* PerimeterX でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。


* PerimeterX では、**IDP** Initiated SSO がサポートされます

## <a name="adding-perimeterx-from-the-gallery"></a>ギャラリーからの PerimeterX の追加

Azure AD への PerimeterX の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に PerimeterX を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**PerimeterX**」と入力します。
1. 結果のパネルから **[PerimeterX]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-perimeterx"></a>PerimeterX の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、PerimeterX に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと PerimeterX の関連ユーザーとの間にリンク関係を確立する必要があります。

PerimeterX に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[PerimeterX の SSO の構成](#configure-perimeterx-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[PerimeterX のテスト ユーザーの作成](#create-perimeterx-test-user)** - PerimeterX で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **PerimeterX** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。


1. PerimeterX アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングをご自分の SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、PerimeterX アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前 | ソース属性|
    | ------------ | --------- |
    | firstName | User.givenname |
    | lastName  | User.surname |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[PerimeterX のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B. Simon に PerimeterX へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[PerimeterX]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-perimeterx-sso"></a>PerimeterX の SSO の構成

1. 管理者のアクセス許可を使用して、PerimeterX コンソールにログインします。

1. **[Admin]\(管理\) > [ACCOUNTS]\(アカウント\)** の順に移動します 

    ![PerimeterX の SSO](./media/perimeterx-tutorial/accounts.png)

1. **[編集]**

4.  [Edit Account]\(アカウントの編集\) ダイアログで、次の手順を実行します。

    ![PerimeterX の SSO - [Edit Account]\(アカウントの編集\)](./media/perimeterx-tutorial/saml-sso.png)

    a.  **[Enable Single Sign-On (SSO)]\(シングル サインオン (SSO) を有効にする\)** をオンにします

    b.  **[Azure SAML]** を選択します。

    c.  **[SAML Endpoint]\(SAML エンドポイント\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. **[Issuer]/(発行者/)** テキスト ボックスに、Azure portal からコピーした Azure AD 識別子の値を貼り付けます。

    e. Azure portal からダウンロードした **証明書 (Base64)** をメモ帳で開き、その内容を **[X.509 Certificate]\(X.509 証明書\)** ボックスに貼り付けます。

    f. **[Save Changes]\(変更の保存\)** をクリックします

### <a name="create-perimeterx-test-user"></a>PerimeterX のテスト ユーザーの作成

PerimeterX のテスト ユーザーの作成方法については、[PerimeterX の管理ユーザー ガイド](https://docs.perimeterx.com/pxconsole/docs/managing-users)を参照してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

1. Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した PerimeterX に自動的にサインインされます

1. Microsoft アクセス パネルを使用することができます。 アクセス パネル上で [PerimeterX] タイルをクリックすると、SSO を設定した PerimeterX に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次の手順

PerimeterX を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。