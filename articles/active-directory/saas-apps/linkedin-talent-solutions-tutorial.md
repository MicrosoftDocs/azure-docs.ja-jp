---
title: チュートリアル:LinkedIn Talent Solutions と Azure Active Directory のシングル サインオン (SSO) の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Talent Solutions の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: jeedes
ms.openlocfilehash: 160c7514b095a330a52dd1607dca6f2eb87215d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727343"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-talent-solutions"></a>チュートリアル:LinkedIn Talent Solutions と Azure Active Directory のシングル サインオン (SSO) の統合

このチュートリアルでは、LinkedIn Talent Solutions と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と LinkedIn Talent Solutions を統合すると、次のことができます。

* LinkedIn Talent Solutions にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LinkedIn Talent Solutions に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LinkedIn Talent Solutions ダッシュボードでのアカウント センターへのアクセス

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* LinkedIn Talent Solutions では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* LinkedIn Talent Solutions では、**Just-In-Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-linkedin-talent-solutions-from-the-gallery"></a>ギャラリーからの LinkedIn Talent Solutions の追加

Azure AD への LinkedIn Talent Solutions の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に LinkedIn Talent Solutions を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LinkedIn Talent Solutions**」と入力します。
1. 結果のパネルから **LinkedIn Talent Solutions** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-linkedin-talent-solutions"></a>LinkedIn Talent Solutions の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、LinkedIn Talent Solutions に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと LinkedIn Talent Solutions の関連ユーザーとの間にリンク関係を確立する必要があります。

LinkedIn Talent Solutions に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LinkedIn Talent Solutions の SSO の構成](#configure-linkedin-talent-solutions-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LinkedIn Talent Solutions のテスト ユーザーの作成](#create-linkedin-talent-solutions-test-user)** - LinkedIn Talent Solutions で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **LinkedIn Talent Solutions** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![image1](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![Image2](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    ![image3](common/idp-intiated.png)

    > [!Note]
    > **識別子** と **返信 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** テキスト ボックスに、URL として「`https://www.linkedin.com/talent/`」と入力します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[LinkedIn Talent Solutions のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に LinkedIn Talent Solutions へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LinkedIn Talent Solutions]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-linkedin-talent-solutions-sso"></a>LinkedIn Talent Solutions の SSO の構成

1. LinkedIn Talent Solutions の Web サイトに管理者としてサインインします。
1. **アカウント センター** に移動します。 
1. ナビゲーション バーから **[設定]** タブを選択します。

    ![設定ページ](./media/linkedin-talent-solutions-tutorial/settings.png)

1. **[Single Sign-On (SSO)]\(シングル サインオン (SSO)\)** セクションを展開します。 

1. **[Download]\(ダウンロード\)** ボタンをクリックして **メタデータ ファイル** をダウンロードするか、 **[or click here to load and copy individual fields from the form]\(または、ここをクリックして個々のフィールドをフォームから読み込み、コピーする\)** リンクをクリックして構成データを明らかにします。

    ![ 構成データ](./media/linkedin-talent-solutions-tutorial/sso-settings.png)

1. 次の手順に従って、フォームから個々のフィールドをコピーします。

    ![ 入力データでの構成](./media/linkedin-talent-solutions-tutorial/configuration.png)

    a. **[Entity ID]\(エンティティ ID\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[Azure AD Identifier]\(Azure AD 識別子\)** ボックスに貼り付けます。

    b. **[ACS URL]** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** ボックスに貼り付けます。

    c. **[SP X.509 Certificate(signing)]\(SP X.509 証明書 (署名)\)** ボックスの内容をメモ帳にコピーして、お使いのコンピューターに保存します。

1. **[Upload XML file]\(XML ファイルのアップロード\)** をクリックして、Azure portal からコピーした **フェデレーション メタデータ XML** ファイルをアップロードします。

    ![ XML ファイルをアップロードする](./media/linkedin-talent-solutions-tutorial/xml-file.png)

### <a name="create-linkedin-talent-solutions-test-user"></a>LinkedIn Talent Solutions のテスト ユーザーの作成

このセクションでは、Britta Simon というユーザーを LinkedIn Talent Solutions に作成します。 LinkedIn Talent Solutions では、Just-In-Time ユーザー プロビジョニングがサポートされています。この設定は既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 LinkedIn Talent Solutions にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる LinkedIn Talent Solutions のサインオン URL にリダイレクトされます。  

* LinkedIn Talent Solutions のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した LinkedIn Talent Solutions に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [LinkedIn Talent Solutions] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した LinkedIn Talent Solutions に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

LinkedIn Talent Solutions を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。