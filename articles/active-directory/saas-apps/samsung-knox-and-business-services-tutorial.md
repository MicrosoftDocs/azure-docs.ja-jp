---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Samsung Knox およびビジネス サービスの統合 | Microsoft Docs
description: Azure Active Directory と Samsung Knox およびビジネス サービスの間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: jeedes
ms.openlocfilehash: 330c02f15c0818f0a5c69088757c92a91a523589
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952645"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Samsung Knox およびビジネス サービスの統合

このチュートリアルでは、Samsung Knox およびビジネス サービスと Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Samsung Knox およびビジネス サービスを統合すると、次のことができます。

* Samsung Knox およびビジネス サービスにアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Samsung Knox およびビジネス サービスにサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Samsung Knox アカウント。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Samsung Knox およびビジネス サービスでは、**SP** Initiated SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>ギャラリーからの Samsung Knox およびビジネス サービスの追加

Azure AD への Samsung Knox およびビジネス サービスの統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Samsung Knox およびビジネス サービスを追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Samsung Knox およびビジネス サービス**」と入力します。
1. 結果のパネルから **[Samsung Knox およびビジネス サービス]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Samsung Knox およびビジネス サービスの Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Samsung Knox およびビジネス サービスに対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと [SamsungKnox.com](https://samsungknox.com/) の関連ユーザーとの間にリンク関係を確立する必要があります。

Samsung Knox およびビジネス サービスに対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Samsung Knox およびビジネス サービスの SSO の構成](#configure-samsung-knox-and-business-services-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Samsung Knox およびビジネス サービスのテスト ユーザーの作成](#create-samsung-knox-and-business-services-test-user)** - Samsung Knox およびビジネス サービスで B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Samsung Knox およびビジネス サービス** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    * **[サインオン URL]** ボックスに、URL として「`https://www.samsungknox.com`」と入力します。
    * **[応答 URL (Assertion Consumer Service URL)]** ボックスに、URL として「`https://central.samsungknox.com/ams/ad/saml/acs`」を入力します
    
    ![基本的な SAML 構成値](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

1. **[Set up single sign-on with SAML]\(SAML でシングル サインオンをセットアップします\)** ページの **[SAML 署名証明書]** セクションで、コピー ボタンをクリックして **[アプリのフェデレーション メタデータ URL]** をコピーして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

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

このセクションでは、B.Simon に Samsung Knox およびビジネス サービスへのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Samsung Knox およびビジネス サービス]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-samsung-knox-and-business-services-sso"></a>Samsung Knox およびビジネス サービス SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として [SamsungKnox.com](https://samsungknox.com/) にサインインします。

1. 右上隅にある **[Avatar]\(アバター\)** をクリックします。

    ![Samsung Knox アバター](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. 左側のサイド バーで **[ACTIVE DIRECTORY SETTINGS]\(Active Directory の設定\)** をクリックし、次の手順を実行します。

    ![Active Directory の設定](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. **[Identifier(entity ID)]\(識別子 (エンティティ ID)\)** ボックスに、Azure portal で入力した **[ID]** の値を貼り付けます。

    b. **[App federation metadata URL]\(アプリのフェデレーション メタデータ URL\)** ボックスに、Azure portal からコピーした **アプリのフェデレーション メタデータ URL** の値を貼り付けます。

    c. **[CONNECT TO AD SSO]\(AD SSO への接続\)** をクリックします。

### <a name="create-samsung-knox-and-business-services-test-user"></a>Samsung Knox およびビジネス サービスのテスト ユーザーの作成

このセクションでは、Samsung Knox およびビジネス サービスで Britta Simon というユーザーを作成します。 Samsung Knox 組織にサブ管理者またはテスト ユーザーを招待する方法については、[Knox の構成](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm)または [Knox モバイルの登録](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm)に関する管理者ガイドを参照してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる [SamsungKnox.com](https://samsungknox.com/) にリダイレクトされます。 

* [SamsungKnox.com](https://samsungknox.com/) に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Samsung Knox およびビジネス サービス] タイルをクリックすると、[SamsungKnox.com](https://samsungknox.com/) にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Samsung Knox およびビジネス サービスを構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。