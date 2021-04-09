---
title: 'チュートリアル: Azure Active Directory と ArcGIS Enterprise の統合 | Microsoft Docs'
description: Azure Active Directory と ArcGIS Enterprise の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9cde75440292fffb830656c32181d7be64a55f3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645525"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>チュートリアル: Azure Active Directory と ArcGIS Enterprise の統合

このチュートリアルでは、ArcGIS Enterprise と Azure Active Directory (Azure AD) を統合する方法について説明します。 ArcGIS Enterprise を Azure AD と統合すると、次のことが可能になります。

* ArcGIS Enterprise にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して ArcGIS Enterprise に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な ArcGIS Enterprise サブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* ArcGIS Enterprise では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。
* ArcGIS Enterprise では、**Just In Time** ユーザー プロビジョニングがサポートされます。

## <a name="add-arcgis-enterprise-from-the-gallery"></a>ギャラリーからの ArcGIS Enterprise の追加

Azure AD への ArcGIS Enterprise の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に ArcGIS Enterprise を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**ArcGIS Enterprise**」と入力します。
1. 結果パネルから **[ArcGIS Enterprise]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>ArcGIS Enterprise の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、ArcGIS Enterprise に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと ArcGIS Enterprise の関連ユーザーとの間にリンク関係を確立する必要があります。

ArcGIS Enterprise に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[ArcGIS Enterprise の SSO の構成](#configure-arcgis-enterprise-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[ArcGIS Enterprise のテスト ユーザーの作成](#create-arcgis-enterprise-test-user)** - ArcGIS Enterprise で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **ArcGIS Enterprise** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順を実行します。

    a. **[識別子]** ボックスに、`<EXTERNAL_DNS_NAME>.portal` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2` のパターンを使用して URL を入力します

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[ArcGIS Enterprise クライアント サポート チーム](mailto:support@esri.com)に問い合わせてください。 識別子の値は、このチュートリアルで後から説明する **[Set Identity Provider]\(ID プロバイダーの設定\)** セクションで取得します。

5. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

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

このセクションでは、B.Simon に ArcGIS Enterprise へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、**[ArcGIS Enterprise]\(ArcGIS Enterprise\)** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-arcgis-enterprise-sso"></a>ArcGIS Enterprise の SSO の構成

1. ArcGIS Enterprise 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[ArcGIS Enterprise のセットアップ]** をクリックすると、ArcGIS Enterprise アプリケーションに移動します。 そこから、管理者の資格情報を入力して ArcGIS Enterprise にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. ArcGIS Enterprise を手動で設定する場合は、ArcGIS Enterprise の企業サイトに管理者としてログインします。


1. **[組織] > [設定の編集]** を選択します。

    ![ArcGIS Enterprise の [組織] タブが表示されているスクリーンショット。[設定の編集] が強調表示されています。](./media/arcgisenterprise-tutorial/configure-1.png)

1. **[セキュリティ]** タブを選びます。

    ![[セキュリティ] タブが選択されている画面のスクリーンショット。](./media/arcgisenterprise-tutorial/configure-2.png)

1. **[Enterprise Logins via SAML]\(SAML 経由でのエンタープライズ ログイン\)** セクションまで下へスクロールして **[SET ENTERPRISE LOGIN]\(エンタープライズ ログインの設定\)** を選択します。

    ![[Set Enterprise Login]\(エンタープライズ ログインの設定\) を選択できる [Enterprise Logins via SAML]\(SAML 経由でのエンタープライズ ログイン\) 画面のスクリーンショット。](./media/arcgisenterprise-tutorial/configure-3.png)

1. **[Set Identity Provider]\(ID プロバイダーの設定\)** セクションで、次の手順に従います。

    ![ここで説明されている手順の実行場所となる [Set Identity Provider]\(ID プロバイダーの設定\) 画面のスクリーンショット。](./media/arcgisenterprise-tutorial/configure-4.png)

    a. **[名前]** テキスト ボックスに、**Azure Active Directory Test** のような名前を指定します。

    b. **[URL]** ボックスに、Azure Portal からコピーした **[アプリのフェデレーション メタデータ URL]** の値を貼り付けます。

    c. **[詳細設定の表示]** をクリックし、**[エンティティ ID]** の値をコピーして、Azure Portal の **[ArcGIS Enterprise Domain and URLs]\(ArcGIS Enterprise のドメインと URL\)** セクションの **[識別子]** テキスト ボックスに貼り付けます。

    ![エンティティ ID を取得して ID プロバイダーを更新する画面のスクリーンショット。](./media/arcgisenterprise-tutorial/configure-5.png)

    d. **[ID プロバイダーの更新]** をクリックします。

### <a name="create-arcgis-enterprise-test-user"></a>ArcGIS Enterprise テスト ユーザーを作成する

このセクションでは、Britta Simon というユーザーを ArcGIS Enterprise に作成します。 ArcGIS Enterprise では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 ArcGIS Enterprise にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

> [!Note]
> ユーザーを手動で作成する必要がある場合は、[ArcGIS Enterprise のサポート チーム](mailto:support@esri.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる ArcGIS Enterprise のサインオン URL にリダイレクトされます。  

* ArcGIS Enterprise のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した ArcGIS Enterprise に自動的にサインインされます。 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [ArcGIS Enterprise] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した ArcGIS Enterprise に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

ArcGIS Enterprise を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。