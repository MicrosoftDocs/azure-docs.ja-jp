---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Nitro Productivity Suite の統合 | Microsoft Docs
description: Azure Active Directory と Nitro Productivity Suite の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2020
ms.author: jeedes
ms.openlocfilehash: 3280c97e735e68aa36f018a8de59964ade9567b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181919"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Nitro Productivity Suite の統合

このチュートリアルでは、Nitro Productivity Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Nitro Productivity Suite を統合すると、次のことができます。

* Nitro Productivity Suite にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Nitro Productivity Suite に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Nitro Productivity Suite [Enterprise サブスクリプション](https://www.gonitro.com/pricing)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Nitro Productivity Suite では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。
* Nitro Productivity Suite では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>ギャラリーからの Nitro Productivity Suite の追加

Azure AD への Nitro Productivity Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Nitro Productivity Suite を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Nitro Productivity Suite**」と入力します。
1. 結果から **[Nitro Productivity Suite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Nitro Productivity Suite の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Nitro Productivity Suite に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Nitro Productivity Suite の関連ユーザーとの間にリンク関係を確立する必要があります。

Nitro Productivity Suite に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。

    a. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    
    b. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
    
2. [Nitro Productivity Suite のテスト ユーザーを作成](#create-a-nitro-productivity-suite-test-user)して、B.Simon に対応するユーザーを Nitro Productivity Suite に作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Nitro Productivity Suite** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![[ダウンロード] リンクが強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/certificatebase64.png)
    
1. **[Set up Nitro Productivity Suite]\(Nitro Productivity Suite のセットアップ\)** セクションで、 **[ログイン URL]** の横のコピー アイコンを選択します。
    
    ![URL とコピー アイコンが強調表示された [Set up Nitro Productivity Suite]\(Nitro Productivity Suite のセットアップ\) セクションのスクリーンショット](common/copy-configuration-urls.png)
    
1. [Nitro Admin ポータル](https://admin.gonitro.com/)の **[Enterprise Settings]\(エンタープライズ設定\)** ページで、 **[Single Sign-On]\(シングル サインオン\)** セクションを見つけます。 **[Setup SAML SSO]\(SAML SSO のセットアップ\)** を選択します。

    a. 前の手順でコピーした **ログイン URL** を **[サインイン URL]** フィールドに貼り付けます。
    
    b. 前の手順でダウンロードした **証明書 (Base64)** を **[X509 Signing Certificate]\(X509 署名証明書\)** フィールドにアップロードします。
    
    c. **[Submit]\(送信\)** をクリックします。
    
    d. **[シングル サインオンを有効にする]** を選択します。


1. [Azure portal](https://portal.azure.com/) に戻ります。 **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

   ![鉛筆アイコンが強調表示された [SAML によるシングル サインオンのセットアップ] ページのスクリーンショット](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、[Nitro Admin ポータル](https://admin.gonitro.com/)から **[SAML Entity ID]\(SAML エンティティ ID\)** フィールドをコピーして貼り付けます。 この値は `urn:auth0:gonitro-prod:<ENVIRONMENT>` 形式になっています。

    b. **[応答 URL]** ボックスに、[Nitro Admin ポータル](https://admin.gonitro.com/)から **[ACS URL]** フィールドをコピーして貼り付けます。 この値は `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>` 形式になっています。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** を選択して次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://sso.gonitro.com/login`」と入力します。

1. **[保存]** を選択します。

1. Nitro Productivity Suite アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![既定の属性のスクリーンショット](common/default-attributes.png)

1. 上記の属性に加えて、Nitro Productivity Suite アプリケーションでは、その他にいくつかの属性が SAML 応答で返されることが想定されています。 これらの属性は値が事前に設定されますが、要件に従ってそれらの値を確認することができます。
    
    | 名前  |  ソース属性|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[Show password]\(パスワードの表示\)** チェック ボックスをオンにし、パスワードを書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Nitro Productivity Suite へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[Nitro Productivity Suite]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

### <a name="create-a-nitro-productivity-suite-test-user"></a>Nitro Productivity Suite テスト ユーザーを作成する

Nitro Productivity Suite では、Just-In-Time ユーザー プロビジョニンがサポートされています。この設定は既定で有効になっています。 追加の操作は必要ありません。 Nitro Productivity Suite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Nitro Productivity Suite のサインオン URL にリダイレクトされます。  

2. Nitro Productivity Suite のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Nitro Productivity Suite に自動的にサインインされます 

また、Microsoft アクセス パネルを使用して、任意のモードでアプリケーションをテストすることもできます。 アクセス パネルで [Nitro Productivity Suite] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Nitro Productivity Suite に自動的にサインインされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

Nitro Productivity Suite を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。