---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zoom の統合 | Microsoft Docs
description: Azure Active Directory と Zoom 間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/11/2020
ms.author: jeedes
ms.openlocfilehash: d105c83ba3db9502cf83f943dec6fcbfd5640d07
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735626"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Zoom の統合

このチュートリアルでは、Zoom と Azure Active Directory (Azure AD) を統合する方法について説明します。 Zoom と Azure AD を統合すると、次のことができます。

* Zoom にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して Zoom に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Zoom のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Zoom では、**SP** Initiated SSO がサポートされます。 
* Zoom では、[**自動化された** ユーザー プロビジョニング](./zoom-provisioning-tutorial.md)がサポートされます。

## <a name="adding-zoom-from-the-gallery"></a>ギャラリーからの Zoom の追加

Azure AD への Zoom の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Zoom を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Zoom**」と入力します。
1. 結果パネルで **[Zoom]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-zoom"></a>Zoom の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Zoom に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Zoom の関連ユーザーとの間にリンク関係を確立する必要があります。

Zoom に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Zoom SSO の構成](#configure-zoom-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Zoom テスト ユーザーの作成](#create-zoom-test-user)** - Zoom で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Zoom** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.zoom.us`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`<companyname>.zoom.us`

    c. **[応答 URL]** ボックスに、`https://<companyname>.zoom.us` のパターンを使用して URL を入力します

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 この値を取得するには、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)にお問い合わせください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Zoom のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

> [!NOTE]
> Azure AD でロールを構成する方法については、[エンタープライズ アプリケーション用の SAML トークン内に発行されるロール要求を構成する方法](../develop/active-directory-enterprise-app-role-management.md)に関するページを参照してください。

> [!NOTE]
> Zoom は、グループ要求が SAML ペイロードに含まれていることを想定している場合があります。 グループを作成した場合は、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)にグループ情報を連絡し、Zoom クライアント サポート チーム側で、そのグループ情報を構成できるようにしてください。 また、Zoom クライアント サポート チーム側でオブジェクト ID を構成できるよう、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)にオブジェクト ID を提供する必要もあります。 オブジェクト ID の取得については、[Azure での Zoom の構成](https://support.zoom.us/hc/articles/115005887566)に関するページを参照してください。

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

このセクションでは、B.Simon に Zoom へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Zoom]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-zoom-sso"></a>Zoom SSO の構成

1. Zoom 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして、**My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Zoom のセットアップ]** をクリックすると、Zoom アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Zoom にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 6 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Zoom を手動でセットアップする場合は、別の Web ブラウザー ウィンドウで、Zoom 企業サイトに管理者としてサインインします。

2. [**シングル サインオン**] タブをクリックします。

    ![[Single sign-on]\(シングル サインオン\) タブ](./media/zoom-tutorial/zoom-sso1.png "シングル サインオン")

3. [**セキュリティ制御**] タブをクリックし、[**シングル サインオン**] 設定に移動します。

4. [Single Sign-On] セクションで、次の手順に従います。

    ![[Single sign-on]\(シングル サインオン\) セクション](./media/zoom-tutorial/zoom-sso2.png "シングル サインオン")

    a. **[Sign-in page URL]\(サインイン ページの URL\)** テキスト ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. **[サインアウト ページの URL]** 値については、Azure portal に移動し、左側の **[Azure Active Directory]** をクリックしてから **[アプリの登録]** に移動する必要があります。

    ![Azure Active Directory のボタン](./media/zoom-tutorial/appreg.png)

    c. **[エンドポイント]** をクリックします

    ![[エンドポイント] ボタン](./media/zoom-tutorial/endpoint.png)

    d. **[SAML-P サインアウト エンドポイント]** をコピーして **[サインアウト ページの URL]** テキストボックスに貼り付けます。

    ![エンドポイントのコピー ボタン](./media/zoom-tutorial/endpoint1.png)

    e. base-64 でエンコードされた証明書をメモ帳で開き、内容をクリップボードにコピーし、**[Identity Provider Certificate]** ボックスに貼り付けます。

    f. **[Issuer]\(発行者\)** テキストボックスに、Azure portal からコピーした **Azure AD 識別子** の値を貼り付けます。 

    g. **[変更を保存]** をクリックします。

    > [!NOTE]
    > 詳しくは、Zoom のドキュメント [https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566) をご覧ください。

### <a name="create-zoom-test-user"></a>Zoom テスト ユーザーの作成

このセクションの目的は、Zoom で B.Simon というユーザーを作成することです。 Zoom では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](./zoom-provisioning-tutorial.md)をご覧ください。

> [!NOTE]
> ユーザーを手動で作成する必要がある場合、[Zoom クライアント サポート チーム](https://support.zoom.us/hc/)に問い合わせる必要があります

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Zoom のサインオン URL にリダイレクトされます。

* Zoom のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Zoom] タイルをクリックすると、Zoom のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Azure AD Zoom を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。