---
title: チュートリアル:Azure Active Directory と Oracle Cloud Infrastructure Console の統合 | Microsoft Docs
description: Azure Active Directory と Oracle Cloud Infrastructure Console の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/04/2020
ms.author: jeedes
ms.openlocfilehash: 914858471432979e5516a1f075ed4fcbb8061a5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181854"
---
# <a name="tutorial-integrate-oracle-cloud-infrastructure-console-with-azure-active-directory"></a>チュートリアル:Oracle Cloud Infrastructure Console と Azure Active Directory を統合する

このチュートリアルでは、Oracle Cloud Infrastructure Console と Azure Active Directory (Azure AD) を統合する方法について説明します。 Oracle Cloud Infrastructure Console と Azure AD を統合すると、次のことが可能になります。

* Oracle Cloud Infrastructure Console にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで Oracle Cloud Infrastructure Console に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Oracle Cloud Infrastructure Console でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Oracle Cloud Infrastructure Console では、**SP** Initiated SSO がサポートされます。

## <a name="adding-oracle-cloud-infrastructure-console-from-the-gallery"></a>ギャラリーからの Oracle Cloud Infrastructure Console の追加

Azure AD への Oracle Cloud Infrastructure Console の統合を構成するには、マネージド SaaS アプリの一覧にギャラリーから Oracle Cloud Infrastructure Console を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Oracle Cloud Infrastructure Console**」と入力します。
1. 結果のパネルから **[Oracle Cloud Infrastructure Console]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

**B. Simon** というテスト ユーザーを使用して、Oracle Cloud Infrastructure Console に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Oracle Cloud Infrastructure Console の関連ユーザーとの間にリンク関係を確立する必要があります。

Oracle Cloud Infrastructure Console に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user)** - B. Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B. Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Oracle Cloud Infrastructure Console の構成](#configure-oracle-cloud-infrastructure-console)** - アプリケーション側で SSO 設定を構成します。
    1. **[Oracle Cloud Infrastructure Console のテスト ユーザーの作成](#create-oracle-cloud-infrastructure-console-test-user)** - Oracle Cloud Infrastructure Console で B. Simon に対応するユーザーを作成し、Azure AD の B. Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Oracle Cloud Infrastructure Console** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

   > [!NOTE]
   > チュートリアルの「**Oracle Cloud Infrastructure Console シングルサインオンの構成**」セクションで、サービス プロバイダー メタデータ ファイルを取得します。
    
   1. **[メタデータ ファイルをアップロードします]** をクリックします。

   1. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

   1. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、 **[基本的な SAML 構成]** セクションのテキストボックスに自動的に設定されます。
    
      > [!NOTE]
      > **識別子** と **応答 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

      **[サインオン URL]** ボックスに、`https://console.<REGIONNAME>.oraclecloud.com/` という形式で URL を入力します。

      > [!NOTE]
      > この値は実際のものではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Oracle Cloud Infrastructure Console のクライアント サポート チーム](https://www.oracle.com/support/advanced-customer-support/products/cloud.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. Oracle Cloud Infrastructure Console アプリケーションでは、特定の形式の SAML アサーションを受け取るため、SAML トークン属性の構成にカスタム属性マッピングを追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。 **[編集]** アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

   ![image1](common/edit-attribute.png)

1. その他に、Oracle Cloud Infrastructure Console アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[グループ要求 (プレビュー)]** ダイアログの **[ユーザー属性とクレーム]** セクションで、次の手順を実行します。

   1. **[名前識別子の値]** の横にある **ペン** をクリックします。

   1. **[名前識別子の形式の選択 ]** として **[Persistent]** を選択します。
 
   1. **[保存]** をクリックします。

      ![Image2](./media/oracle-cloud-tutorial/config07.png)
    
      ![image3](./media/oracle-cloud-tutorial/config11.png)

   1. **[Groups returned in claim]\(要求で返されるグループ\)** の横にある **ペン** をクリックします。

   1. ラジオ ボタンのリストから **[セキュリティ グループ]** を選択します。

   1. **[グループ ID]** の **[ソース属性]** を選択します。

   1. **[グループ要求の名前をカスタマイズする]** をオンにします。

   1. **[名前]** ボックスに「**groupName**」と入力します。

   1. **[名前空間 (省略可能)]** ボックスに「`https://auth.oraclecloud.com/saml/claims`」と入力します。

   1. **[保存]** をクリックします。

      ![image4](./media/oracle-cloud-tutorial/config08.png)

1. **[Set up Oracle Cloud Infrastructure Console]\(Oracle Cloud Infrastructure Console の設定\)** セクションで、要件に基づいて適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B. Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B. Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B. Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B. Simon に Oracle Cloud Infrastructure Console へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Oracle Cloud Infrastructure Console]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B. Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-oracle-cloud-infrastructure-console"></a>Oracle Cloud Infrastructure Console の構成

1. 別の Web ブラウザー ウィンドウで、Oracle Cloud Infrastructure Console に管理者としてサインインします。

1. メニューの左側をクリックし、 **[ID]** をクリックして **[Federation]\(フェデレーション\)** に移動します。

   ![構成 1](./media/oracle-cloud-tutorial/config01.png)

1. **[Download this document]\(このドキュメントをダウンロードする\)** リンクをクリックして **サービス プロバイダー メタデータ ファイル** を保存し、Azure portal の **[基本的な SAML 構成]** セクションにアップロードして、 **[ID プロバイダーの追加]** をクリックします。

   ![構成 2](./media/oracle-cloud-tutorial/config02.png)

1. **[ID プロバイダーの追加]** ポップアップで、次の手順に従います。

   ![構成 3](./media/oracle-cloud-tutorial/config03.png)

   1. **[NAME]\(名前\)** ボックスに自分の名前を入力します。

   1. **[DESCRIPTION]\(説明\)** ボックスに説明を入力します。

   1. **[MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) または SAML 2.0 に準拠している ID プロバイダー]\(MICROSOFT ACTIVE DIRECTORY FEDERATION SERVICE (ADFS) または SAML 2.0 に準拠している ID プロバイダー\)** を **[TYPE]\(種類\)** として選択します。

   1. **[Browse]\(参照\)** をクリックし、Azure portal からダウンロードしたフェデレーション メタデータ XML をアップロードします。

   1. **[Continue]\(続行\)** をクリックし、 **[Edit Identity Provider]\(ID プロバイダーの編集\)** セクションで次の手順を実行します。

      ![構成 4](./media/oracle-cloud-tutorial/configure-09.png)

   1. **[IDENTITY PROVIDER GROUP]\(ID プロバイダー グループ\)** では、[Azure AD Group Object ID]\(Azure AD グループ オブジェクト ID\) が選択されている必要があります。 グループ ID は Azure Active Directory のグループの GUID である必要があります。 このグループを、 **[OCI GROUP]\(OCI グループ\)** フィールド内の対応するグループとマップする必要があります。

   1. Azure portal での設定と組織のニーズに応じて複数のグループをマップできます。 **[+ Add mapping]\(+ マッピングの追加\)** をクリックして、必要に応じていくつでもグループを追加してください。

   1. **[送信]** をクリックします。
   
### <a name="create-oracle-cloud-infrastructure-console-test-user"></a>Oracle Cloud Infrastructure Console のテスト ユーザーの作成

 Oracle Cloud Infrastructure Console では、Just-In-Time プロビジョニングがサポートされています。この設定は、既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 アクセスの試行時に新しいユーザーは作成されず、またユーザーを作成する必要もありません。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Oracle Cloud Infrastructure Console] タイルを選択すると、Oracle Cloud Infrastructure Console のサインイン ページにリダイレクトされます。 次のようにドロップダウン メニューから **[IDENTITY PROVIDER]\(ID プロバイダー\)** を選択し、 **[Continue]\(続行\)** をクリックしてサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

![構成](./media/oracle-cloud-tutorial/config10.png)

## <a name="next-steps"></a>次の手順

Oracle Cloud Infrastructure Console を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。