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
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: b67e280824c5e2336aa79e14d3e09d3a670118a5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554334"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Nitro Productivity Suite の統合

このチュートリアルでは、Nitro Productivity Suite と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Nitro Productivity Suite を統合すると、次のことができます。

* Nitro Productivity Suite にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Nitro Productivity Suite に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

サービスとしてのソフトウェア (SaaS) アプリと Azure AD の統合の詳細については、[Azure Active Directory を使用したアプリケーション アクセスとシングル サインオンの概要](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、以下が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Nitro Productivity Suite [Enterprise サブスクリプション](https://www.gonitro.com/pricing)。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Nitro Productivity Suite では、**SP** Initiated SSO と **IDP** Initiated SSO がサポートされます。
* Nitro Productivity Suite では、**Just-In-Time** ユーザー プロビジョニングがサポートされます。
* Nitro Productivity Suite を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 詳細については、[Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)に関するページを参照してください。

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>ギャラリーからの Nitro Productivity Suite の追加

Azure AD への Nitro Productivity Suite の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Nitro Productivity Suite を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Nitro Productivity Suite**」と入力します。
1. 結果から **[Nitro Productivity Suite]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Nitro Productivity Suite の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Nitro Productivity Suite に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Nitro Productivity Suite の関連ユーザーとの間にリンク関係を確立する必要があります。

Nitro Productivity Suite に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. [Azure AD SSO を構成](#configure-azure-ad-sso)して、ユーザーがこの機能を使用できるようにします。
    1. [Azure AD のテスト ユーザーを作成](#create-an-azure-ad-test-user)して、B.Simon を使って Azure AD のシングル サインオンをテストします。
    1. [Azure AD テスト ユーザーを割り当て](#assign-the-azure-ad-test-user)て、B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. [Nitro Productivity Suite SSO を構成](#configure-nitro-productivity-suite-sso)して、アプリケーション側でシングル サインオン設定を構成します。
    1. [Nitro Productivity Suite のテスト ユーザーを作成](#create-a-nitro-productivity-suite-test-user)して、B.Simon に対応するユーザーを Nitro Productivity Suite に作成し、Azure AD の B.Simon にリンクさせます。
1. [SSO をテスト](#test-sso)して、構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Nitro Productivity Suite** アプリケーション統合ページで、 **[管理]** セクションを見つけます。 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけます。 **[ダウンロード]** を選択して証明書をダウンロードし、コンピューターに保存します。

    ![[ダウンロード] リンクが強調表示された [SAML 署名証明書] セクションのスクリーンショット](common/certificatebase64.png)
    
1. **[Set up Nitro Productivity Suite]\(Nitro Productivity Suite のセットアップ\)** セクションで、 **[ログイン URL]** の横のコピー アイコンを選択します。
    
    ![URL とコピー アイコンが強調表示された [Set up Nitro Productivity Suite]\(Nitro Productivity Suite のセットアップ\) セクションのスクリーンショット](common/copy-configuration-urls.png)
    
1. [Nitro Admin ポータル](https://admin.gonitro.com/)の **[Enterprise Settings]\(エンタープライズ設定\)** ページで、 **[Single Sign-On]\(シングル サインオン\)** セクションを見つけます。 **[Setup SAML SSO]\(SAML SSO のセットアップ\)** を選択します。

    a. 前の手順でコピーした**ログイン URL** を **[サインイン URL]** フィールドに貼り付けます。
    
    b. 前の手順でダウンロードした**証明書 (Base64)** を **[X509 Signing Certificate]\(X509 署名証明書\)** フィールドにアップロードします。
    
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

   ![[ユーザーとグループ] が強調表示された [管理] セクションのスクリーンショット](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] が強調表示された [ユーザーとグループ] ページのスクリーンショット](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. SAML アサーション内にロール値が必要な場合、 **[ロールの選択]** ダイアログ ボックスで、一覧からユーザーに適したロールを選択します。 次に、画面の下部にある **[選択]** を選択します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-nitro-productivity-suite-sso"></a>Nitro Productivity Suite SSO の構成

Nitro Productivity Suite 側でシングル サインオンを構成するには、ダウンロードした**証明書 (Base64)** と Azure portal からコピーした適切な URL を [Nitro Productivity Suite サポート チーム](https://www.gonitro.com/support)に送信します。 サポート チームは、SAML SSO 接続が両方の側で正しく設定されていることを確認します。

### <a name="create-a-nitro-productivity-suite-test-user"></a>Nitro Productivity Suite テスト ユーザーを作成する

Nitro Productivity Suite では、Just-In-Time ユーザー プロビジョニンがサポートされています。この設定は既定で有効になっています。 追加の操作は必要ありません。 Nitro Productivity Suite にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Nitro Productivity Suite] タイルを選択すると、SSO を設定した Nitro Productivity Suite に自動的にサインインします。 詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリケーションと Azure Active Directory との統合に関するチュートリアル](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Nitro Productivity Suite を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Nitro Productivity Suite を保護する](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

