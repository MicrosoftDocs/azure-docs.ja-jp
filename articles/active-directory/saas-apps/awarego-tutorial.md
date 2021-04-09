---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と AwareGo の統合 | Microsoft Docs
description: Azure Active Directory と AwareGo の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: 4682396f68d6ff1af0b2fb6a5b1a8419d6963529
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98735341"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>チュートリアル:Azure Active Directory シングル サインオンと AwareGo の統合

このチュートリアルでは、AwareGo と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と AwareGo を統合すると、次のことができます。

* AwareGo にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して AwareGo に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AwareGo でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 AwareGo では、SP (Service Provider) Initiated SSO がサポートされます。


## <a name="adding-awarego-from-the-gallery"></a>ギャラリーからの AwareGo の追加

Azure AD への AwareGo の統合を構成するには、ギャラリーからマネージド SaaS (サービスとしてのソフトウェア) アプリの一覧に AwareGo を追加する必要があります。

1. 職場アカウント、学校アカウント、または個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AwareGo**」と入力します。
1. 結果ペインで **[AwareGo]** を選択し、アプリを追加します。 数秒で、お使いのテナントにアプリが追加されます。


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>AwareGo の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、AwareGo に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AwareGo の関連ユーザーとの間にリンク関係を確立する必要があります。

AwareGo に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。  

    a. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - ユーザー B.Simon で Azure AD のシングル サインオンをテストします。  
    b. **[Azure AD のテスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - ユーザー B.Simon が Azure AD シングル サインオンを使用できるようにします。  

1. **[AwareGo の SSO の構成](#configure-awarego-sso)** - アプリケーション側でシングル サインオン設定を構成します。

    a. **[AwareGo のテスト ユーザーの作成](#create-an-awarego-test-user)** - AwareGo で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。  
    b. **[SSO をテスト](#test-sso)** して、構成が正しく機能することを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

Azure portal で Azure AD SSO を有効にするには、以下を実行します。

1. Azure portal の **AwareGo** アプリケーション統合ページで、 **[管理]** の下にある **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. 設定を編集するには、 **[SAML によるシングル サインオンのセットアップ]** ペインで **[編集]** ボタンを選択します。

   ![[基本的な SAML 構成] の編集ボタンのスクリーンショット。](common/edit-urls.png)

1. **[基本的な SAML 構成]** の編集ペインで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次の URL のいずれかを入力します。

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. **[識別子 (エンティティ ID)]** ボックスに、次の形式で URL を入力します: `https://<SUBDOMAIN>.awarego.com`

    c. **[応答 URL]** ボックスに、次の形式で URL を入力します: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > 上記の値は、実際の値ではありません。 これらの値は実際の識別子と応答 URL で更新してください。 値を取得するには、[AwareGo クライアント サポート チーム](mailto:support@awarego.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションの例を参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** の横にある **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![[SAML 署名証明書] ペインの証明書の "ダウンロード" リンクのスクリーンショット。](common/certificatebase64.png)

1. **[AwareGo のセットアップ]** セクションで、要件に基づいて 1 つまたは複数の URL をコピーします。

    ![構成 URL をコピーするための [AwareGo の設定] ペインのスクリーンショット。](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のペインで **[Azure Active Directory]** を選択し、 **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティ ペインで、次の手順を実行します。

   a. **[名前]** ボックスに「**B.Simon**」と入力します。  
   b. **[ユーザー名]** ボックスに、`<username>@<companydomain>.<extension>` 形式でユーザー名を入力します (例: B.Simon@contoso.com)。  
   c. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を、後で使用できるように書き留めます。  
   d. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、ユーザー B.Simon に AwareGo へのアクセスを許可することで、このユーザーが Azure SSO を使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. **[アプリケーション]** の一覧で **[AwareGo]** を選択します。
1. アプリの概要ページの **[管理]** セクションで、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ペインで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ペインの **[ユーザー]** の一覧で **[B.Simon]** を選択し、 **[選択]** ボタンを選択します。
1. ユーザーにロールを割り当てる必要がある場合は、 **[ロールの選択]** ボックスの一覧で選択できます。 このアプリに対してロールが設定されていない場合は、 *[既定のアクセス]* ロールが選択されます。
1. **[割り当ての追加]** ペインで、 **[割り当て]** を選択します。

## <a name="configure-awarego-sso"></a>AwareGo の SSO の構成

**AwareGo** 側でシングルサインオンを構成するには、前に Azure portal からダウンロードした **証明書 (Base64)** 証明書と、コピーした URL を [AwareGo サポート チーム](mailto:support@awarego.com)に送信します。 サポート チームはこの設定を作成して、両方の側で SAML SSO 接続を正しく確立します。

### <a name="create-an-awarego-test-user"></a>AwareGo のテスト ユーザーの作成

このセクションでは、AwareGo で Britta Simon というユーザーを作成します。 [AwareGo サポート チーム](mailto:support@awarego.com)と協力して、AwareGo プラットフォームにユーザーを追加します。 シングル サインオンを使用できるようにするには、ユーザーを作成してアクティブにする必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のいずれかを実行することにより、Azure AD のシングル サインオン構成をテストできます。 

* Azure portal で、 **[このアプリケーションをテストします]** を選択します。 これによってリダイレクトされる AwareGo のサインインページで、サインイン フローを開始できます。 

* AwareGo のサインイン ページに直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリに移動します。 マイ アプリで **[AwareGo]** タイルを選択すると、AwareGo サインイン ページにリダイレクトされます。 詳細については、「[マイ アプリ ポータルからアプリにサインインして開始する](../user-help/my-apps-portal-end-user-access.md)」を参照してください。


## <a name="next-steps"></a>次の手順

AwareGo を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、アプリの条件付きアクセス制御を拡張したものです。 詳細については、[Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)に関するページを参照してください。