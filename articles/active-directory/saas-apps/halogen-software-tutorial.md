---
title: チュートリアル:Azure Active Directory と Saba TalentSpace の統合 | Microsoft Docs
description: Azure Active Directory と Saba TalentSpace の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: edb94e8e1b4e814bfac4a1a2a90c5ec71bc48c77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653040"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Saba TalentSpace の統合

このチュートリアルでは、Saba TalentSpace と Azure Active Directory (Azure AD) を統合する方法について説明します。 Saba TalentSpace と Azure AD を統合すると、次のことができます。

* Saba TalentSpace にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Saba TalentSpace に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Saba TalentSpace でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Saba TalentSpace では、**SP** Initiated SSO がサポートされます

## <a name="add-saba-talentspace-from-the-gallery"></a>ギャラリーからの Saba TalentSpace の追加

Azure AD への Saba TalentSpace の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Saba TalentSpace を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Saba TalentSpace**」と入力します。
1. 結果パネルで **[Saba TalentSpace]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-saba-talentspace"></a>Saba TalentSpace の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Saba TalentSpace に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Saba TalentSpace の関連ユーザーとの間にリンク関係を確立する必要があります。

Saba TalentSpace に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Saba TalentSpace SSO の構成](#configure-saba-talentspace-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Saba TalentSpace テスト ユーザーの作成](#create-saba-talentspace-test-user)** - Saba TalentSpace で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Saba TalentSpace** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://global.hgncloud.com/[companyname]/saml/login`

    b. **[識別子 (エンティティ ID)]** ボックスに、`https://global.hgncloud.com/[companyname]/saml/metadata` というパターンで URL を入力します。

    c. **[応答 URL (Assertion Consumer Service URL)]** ボックスに、`https://global.hgncloud.com/[companyname]/saml/SSO` というパターンで URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Saba TalentSpace クライアント サポート チーム](https://support.saba.com/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Saba TalentSpace のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Saba TalentSpace へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Saba TalentSpace]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-saba-talentspace-sso"></a>Saba TalentSpace の SSO の構成

1. 別のブラウザー ウィンドウで、管理者として **Saba TalentSpace** アプリケーションにサインオンします。

2. **[Options]** タブをクリックします。
  
    ![[Options]\(オプション\) タブが選択された Saba TalentSpace ホーム ページを示すスクリーンショット。](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. 左のナビゲーション ウィンドウで、 **[SAML Configuration]** をクリックします。
  
    ![左側の [User Interface]\(ユーザー インターフェイス\) ナビゲーション ウィンドウで [SAML Configuration]\(SAML 構成\) が選択されているスクリーンショット。](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. **[SAML 構成]** ページで、次の手順を実行します。

    ![[Settings]\(設定\) オプションが強調表示されている [SAML Configuration]\(SAML 構成\) ページを示すスクリーンショット。](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. **[Unique Identifier]** で **[NameID]** を選択します。

    b. **[Unique Identifier Maps To]** で **[Username]** を選択します。
  
    c. ダウンロードしたメタデータ ファイルをアップロードするには、 **[Browse]** をクリックしてファイルを選択し、 **[Upload File]** をクリックします。

    d. 構成をテストするには、 **[Run Test]** をクリックします。

    > [!NOTE]
    > "*The SAML test is complete.Please close this window*" というメッセージが表示されるまで待機する必要があります。 次に、開いているブラウザー ウィンドウを閉じます。 **[Enable SAML]** チェック ボックスは、テストが完了した場合にのみ有効にします。

    e. **[Enable SAML]** を選択します。

    f. **[変更を保存]** をクリックします。

### <a name="create-saba-talentspace-test-user"></a>Saba TalentSpace のテスト ユーザーの作成

このセクションの目的は、Saba TalentSpace で Britta Simon というユーザーを作成することです。

**Saba TalentSpace で Britta Simon というユーザーを作成するには、次の手順に従います。**

1. **Saba TalentSpace** アプリケーションに管理者としてサインオンします。

2. **[User Center]** タブをクリックし、 **[Create User]** をクリックします。

    ![[User Center]\(ユーザー センター\) タブと [Create User]\(ユーザーの作成\) が選択されているスクリーンショット。](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. **[New User]** ダイアログ ページで、次の手順に従います。

    ![What is Azure AD Connect](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. **[First Name]\(名\)** ボックスに、ユーザーの名を入力します (この例では **B**)。

    b. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

    c. **[Username]** ボックスに、Azure portal でのユーザー名「**B.Simon**」を入力します。

    d. **[Password]\(パスワード\)** ボックスに、B.Simon のパスワードを入力します。

    e. **[保存]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Saba TalentSpace のサインオン URL にリダイレクトされます。 

* Saba TalentSpace のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Saba TalentSpace] タイルをクリックすると、SSO を設定した Saba TalentSpace に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

 Saba TalentSpace を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。