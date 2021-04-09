---
title: 'チュートリアル: Azure Active Directory と UNIFI の統合 | Microsoft Docs'
description: Azure Active Directory と UNIFI の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 1fa1c40c96694b71bc762050998257102b687c18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650630"
---
# <a name="tutorial-azure-active-directory-integration-with-unifi"></a>チュートリアル: Azure Active Directory と UNIFI の統合

このチュートリアルでは、UNIFI と Azure Active Directory (Azure AD) を統合する方法について説明します。 UNIFI を Azure AD と統合すると、次のことが可能になります。

* UNIFI にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して UNIFI に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な UNIFI サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* UNIFI では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* UNIFI では、**自動化された** ユーザー プロビジョニングがサポートされます。

## <a name="add-unifi-from-the-gallery"></a>ギャラリーからの UNIFI の追加

Azure AD への UNIFI の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に UNIFI を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**UNIFI**」と入力します。
1. 結果パネルから **[UNIFI]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-unifi"></a>UNIFI の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、UNIFI に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと UNIFI の関連ユーザーとの間にリンク関係を確立する必要があります。

UNIFI に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[UNIFI の SSO の構成](#configure-unifi-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[UNIFI のテスト ユーザーの作成](#create-unifi-test-user)** - UNIFI で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **UNIFI** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    **[識別子]** ボックスに、`INVIEWlabs` という URL を入力します。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://app.discoverunifi.com/login`」と入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[UNIFI の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に UNIFI へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[UNIFI]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-unifi-sso"></a>UNIFI の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として **UNIFI** 企業サイトにサインオンします。

2. **[ユーザー]** をクリックします。

    ![UNIFI サイトから [Users]\(ユーザー\) が選択されていることを示すスクリーンショット。](./media/unifi-tutorial/app-1.png)

3. **[Add New Identity Provider(新しい ID プロバイダーを追加)]** をクリックします。

    ![[Ad New Identity Provider]\(新しい ID プロバイダーを追加\) が選択されている画面のスクリーンショット。](./media/unifi-tutorial/app-2.png)

4. **[Add Identity Provider(ID プロバイダーの追加)]** セクションで、次の手順を実行します。

    ![[Add Identity Provider]\(ID プロバイダーの追加\) 画面のスクリーンショット。ここで、説明されている値を入力できます。](./media/unifi-tutorial/app-3.png) 

    a. **[プロバイダー名]** テキストボックスに、ID プロバイダーの名前を入力します。

    b. **[Provider URL]\(プロバイダーの URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    c. Azure Portal からダウンロードした証明書をメモ帳で開き、 **---BEGIN CERTIFICATE---** タグおよび **---END CERTIFICATE---** タグを削除して、残りの内容を **[証明書]** テキストボックスに貼り付けます。

    d. **[is Default Provider(既定のプロバイダーにする)]** チェックボックスをオンします。

### <a name="create-unifi-test-user"></a>UNIFI のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを作成します。 **UNIFI** は、自動ユーザー プロビジョニングをサポートしているため、手動操作は必要ありません。 ユーザーは、Azure AD からの認証が成功した後に自動的に作成されます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる UNIFI のサインオン URL にリダイレクトされます。  

* UNIFI のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した UNIFI に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [UNIFI] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した UNIFI に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

UNIFI を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。