---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Palo Alto Networks - GlobalProtect の統合 | Microsoft Docs
description: Azure Active Directory と Palo Alto Networks - GlobalProtect の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 72072feebfcf8dba249d2045a399e09714177698
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963691"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-palo-alto-networks---globalprotect"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Palo Alto Networks - GlobalProtect の統合

このチュートリアルでは、Palo Alto Networks - GlobalProtect と Azure Active Directory (Azure AD) を統合する方法について説明します。 Palo Alto Networks - GlobalProtect と Azure AD を統合すると、以下のことができます。

* Palo Alto Networks - GlobalProtect にアクセスするユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Palo Alto Networks - GlobalProtect に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Palo Alto Networks - GlobalProtect でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Palo Alto Networks - GlobalProtect では、**SP** によって開始される SSO がサポートされます
* Palo Alto Networks - GlobalProtect では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-palo-alto-networks---globalprotect-from-the-gallery"></a>ギャラリーからの Palo Alto Networks - GlobalProtect の追加

Azure AD への Palo Alto Networks - GlobalProtect の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Palo Alto Networks - GlobalProtect を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Palo Alto Networks - GlobalProtect**」と入力します。
1. 結果パネルで **[Palo Alto Networks - GlobalProtect]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-palo-alto-networks---globalprotect"></a>Palo Alto Networks - GlobalProtect の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Palo Alto Networks - GlobalProtect に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Palo Alto Networks - GlobalProtect の関連ユーザーとの間にリンク関係を確立する必要があります。

Palo Alto Networks - GlobalProtect に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Palo Alto Networks - GlobalProtect SSO の構成](#configure-palo-alto-networks---globalprotect-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Palo Alto Networks - GlobalProtect テスト ユーザーの作成](#create-palo-alto-networks---globalprotect-test-user)** - Palo Alto Networks - GlobalProtect で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Palo Alto Networks - GlobalProtect** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<Customer Firewall URL>`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<Customer Firewall URL>/SAML20/SP`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Palo Alto Networks - GlobalProtect クライアント サポート チーム](https://support.paloaltonetworks.com/support)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[Palo Alto Networks - GlobalProtect のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に Palo Alto Networks - GlobalProtect へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Palo Alto Networks - GlobalProtect]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-palo-alto-networks---globalprotect-sso"></a>Palo Alto Networks - GlobalProtect の SSO の構成

1. 別のブラウザー ウィンドウで Palo Alto Networks - GlobalProtect を管理者として開きます。

2. **[Device]\(デバイス\)** をクリックします。

    ![Palo Alto シングル サインオンの構成 1](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin1.png)

3. 左側のナビゲーション バーから **[SAML Identity Provider]\(SAML ID プロバイダー\)** を選択し、[Import]\(インポート\) をクリックしてメタデータ ファイルをインポートします。

    ![Palo Alto シングル サインオンの構成 2](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin2.png)

4. [Import]\(インポート\) ウィンドウで次の操作を実行します。

    ![Palo Alto シングル サインオンの構成 3](./media/paloaltoglobalprotect-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. **[Profile Name]\(プロファイル名\)** ボックスに名前 (「Azure AD GlobalProtect」など) を入力します。

    b. **[Identity Provider Metadata]\(ID プロバイダーのメタデータ\)** の **[Browse]\(参照\)** をクリックし、Azure Portal からダウンロードした metadata.xml ファイルを選択します。

    c. **[OK]**

### <a name="create-palo-alto-networks---globalprotect-test-user"></a>Palo Alto Networks - GlobalProtect テスト ユーザーの作成

このセクションでは、Palo Alto Networks - GlobalProtect で B.Simon というユーザーを作成します。 Palo Alto Networks - GlobalProtect では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Palo Alto Networks - GlobalProtect にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Palo Alto Networks - GlobalProtect のサインオン URL にリダイレクトされます。 

* Palo Alto Networks - GlobalProtect のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Palo Alto Networks - GlobalProtect] タイルをクリックすると、SSO を設定した Palo Alto Networks - GlobalProtect に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Palo Alto Networks - GlobalProtect を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。