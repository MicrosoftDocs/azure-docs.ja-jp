---
title: 'チュートリアル: Azure Active Directory と TalentLMS の統合 | Microsoft Docs'
description: Azure Active Directory と TalentLMS の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/25/2021
ms.author: jeedes
ms.openlocfilehash: 86ce076df076c990b8bdefa9695805d34674a03f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063494"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>チュートリアル: Azure Active Directory と TalentLMS の統合

このチュートリアルでは、TalentLMS と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と TalentLMS を統合すると、次のことができます。

* TalentLMS にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して TalentLMS に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

TalentLMS と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* TalentLMS でのシングル サインオンが有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* TalentLMS では、**SP** によって開始される SSO がサポートされます

## <a name="add-talentlms-from-the-gallery"></a>ギャラリーからの TalentLMS の追加

Azure AD への TalentLMS の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に TalentLMS を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**TalentLMS**」と入力します。
1. 結果のパネルから **[TalentLMS]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-talentlms"></a>TalentLMS の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、TalentLMS に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと TalentLMS の関連ユーザーとの間にリンク関係を確立する必要があります。

TalentLMS に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[TalentLMS の SSO の構成](#configure-talentlms-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[TalentLMS のテスト ユーザーの作成](#create-talentlms-test-user)** - TalentLMS で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **TalentLMS** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    ![[TalentLMS ドメインと URL] のシングル サインオン情報](common/sp-identifier.png)

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<tenant-name>.TalentLMSapp.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[TalentLMS クライアント サポート チーム](https://www.talentlms.com/contact)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML 署名証明書]** セクションで **[編集]** ボタンをクリックして、 **[SAML 署名証明書]** ダイアログを開きます。

    ![SAML 署名証明書の編集](common/edit-certificate.png)

6. **[SAML 署名証明書]** セクションで **[THUMBPRINT]\(拇印\)** をコピーし、お使いのコンピューターに保存します。

    ![[Thumbprint]\(拇印\) の値をコピーする](common/copy-thumbprint.png)

7. **[TalentLMS のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に TalentLMS へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[TalentLMS]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="configure-talentlms-sso"></a>TalentLMS の SSO の構成

1. 別の Web ブラウザー ウィンドウで、TalentLMS 企業サイトに管理者としてサインインします。

1. **[Account & Settings]** セクションで、**[Users]** タブをクリックします。

    ![[Account & Settings]\(アカウントと設定\)](./media/talentlms-tutorial/IC777296.png "アカウントと設定")

1. **[Single Sign-On (SSO)]** をクリックします。

1. [Single Sign-On] セクションで、次の手順に従います。

    ![シングル サインオン](./media/talentlms-tutorial/saml.png "シングル サインオン")

    a. **[SSO integration type]** 一覧から、**[SAML 2.0]** を選択します。

    b. **[Identity provider (IDP)]\(ID プロバイダー (IDP)\)** ボックスに、Azure portal からコピーした **[Azure AD 識別子]** の値を貼り付けます。

    c. Azure Portal の **拇印** の値を、 **[Certificate fingerprint]\(証明書のフィンガープリント\)** ボックスに貼り付けます。

    d.  **[Remote sign-in URL]\(リモート サインイン URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    e. **[Remote sign-out URL]\(リモート サインアウト URL\)** ボックスに、Azure portal からコピーした **[ログアウト URL]** の値を貼り付けます。

    f. 次の入力を行います。

    * **[TargetedID]\(ターゲット ID\)** ボックスに、「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`」と入力します。

    * **[First name]\(名\)** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`」と入力します。

    * **[Last name]\(姓\)** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`」と入力します。

    * **[Email]\(電子メール\)** ボックスに「`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`」と入力します。

1. **[保存]** をクリックします。

### <a name="create-talentlms-test-user"></a>TalentLMS のテスト ユーザーの作成

Azure AD ユーザーが TalentLMS にサインインできるようにするには、そのユーザーを TalentLMS にプロビジョニングする必要があります。 TalentLMS の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **TalentLMS** テナントにサインインします。

1. **[Users]** をクリックし、**[Add User]** をクリックします。

1. **[Add user]** ダイアログ ページで、以下の手順を実行します。

    ![ユーザーの追加](./media/talentlms-tutorial/IC777299.png "ユーザーの追加")  

    a. **[First name]\(名\)** ボックスに、ユーザーの名前を入力します (この例では **Britta**)。

    b. **[Last name]\(姓\)** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。
 
    c. **[Email address]\(メール アドレス\)** ボックスに、ユーザーのメール アドレスを入力します (たとえば、`brittasimon\@contoso.com`)。

    d. **[ユーザーの追加]** をクリックします。

> [!NOTE]
> 他の TalentLMS ユーザー アカウント作成ツールや、TalentLMS から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる TalentLMS のサインオン URL にリダイレクトされます。 

* TalentLMS のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [TalentLMS] タイルをクリックすると、TalentLMS サインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

TalentLMS を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。
