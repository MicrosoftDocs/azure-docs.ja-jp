---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と LinkedIn Learning の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Learning の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: e5c6bf41e1a3bf92c9141c0d3b54dd58ead2bf3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98727302"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と LinkedIn Learning の統合

このチュートリアルでは、LinkedIn Learning と Azure Active Directory (Azure AD) を統合する方法について説明します。 LinkedIn Learning を Azure AD と統合すると、次のことができます。

* LinkedIn Learning にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して LinkedIn Learning に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LinkedIn Learning でのシングル サインオン (SSO) が有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* LinkedIn Learning では、**SP と IDP** によって開始される SSO がサポートされます
* LinkedIn Learning では、**Just-In-Time** ユーザー プロビジョニングがサポートされます


## <a name="adding-linkedin-learning-from-the-gallery"></a>ギャラリーからの LinkedIn Learning の追加

Azure AD への LinkedIn Learning の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Learning を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LinkedIn Learning**」と入力します。
1. 結果のパネルから **[LinkedIn Learning]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>LinkedIn Learning の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、LinkedIn Learning に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと LinkedIn Learning の関連ユーザーとの間にリンク関係を確立する必要があります。

LinkedIn Learning に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LinkedIn Learning の SSO の構成](#configure-linkedin-learning-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[LinkedIn Learning のテスト ユーザーの作成](#create-linkedin-learning-test-user)** - LinkedIn Learning で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **LinkedIn Learning** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

     a. **[識別子]** ボックスに、LinkedIn ポータルからコピーした **エンティティ ID** を入力します。 

    b. **[応答 URL]** ボックスに、LinkedIn ポータルからコピーした **Assertion Consumer Service (ACS) URL** を入力します。

    c. アプリケーションを **SP 開始** モードで構成する場合は、サインオン URL を指定する **[基本的な SAML 構成]** セクションで **[追加の URL を設定します]** オプションをクリックします。 ログイン URL を作成するには、**Assertion Consumer Service (ACS) URL** をコピーして、/saml/ を /login/ で置き換えます。 これが終了すると、サインオン URL は次のパターンになります。

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![[LinkedIn Learning のドメインと URL] のシングル サインオン情報](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > これらの値は実際の値ではありません。 これらの値は、実際の識別子と応答 URL に更新します。これについては、このチュートリアルの「**LinkedIn Learning の SSO の構成**」セクションで後述します。

1. LinkedIn Learning アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 LinkedIn Learning アプリケーションでは、**nameidentifier** が **user.mail** にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[LinkedIn Learning のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に LinkedIn Learning へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LinkedIn Learning]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-linkedin-learning-sso"></a>LinkedIn Learning の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として LinkedIn Learning テナントにサインオンします。

2. **[Account Center (アカウント センター)]** で、 **[Settings (設定)]** の下の **[Global Settings (グローバル設定)]** をクリックします。 また、ドロップダウン リストから **[Learning - Default (ラーニング - 既定)]** を選択します。

    ![[Default]\(既定\) を選択できる [Global Settings]\(グローバル設定\) を示すスクリーンショット。](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. **[OR Click Here to load and copy individual fields from the form]\(または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする\)** をクリックし、 **[エンティティ ID]** と **[Assertion Consumer Service (ACS) URL]** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションに貼り付けます。

    ![説明されている値を入力できる [Single Sign-On]\(シングル サインオン\) を示すスクリーンショット。](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 **[Upload XML file (XML ファイルのアップロード)]** オプションをクリックして、Azure ポータルからダウンロードした XML ファイルをアップロードします。

    ![X M L ファイルをアップロードできる [Configure the LinkedIn service provider S S O settings]\(LinkedIn サービス プロバイダーの S S O 設定の構成\) を示すスクリーンショット。](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[Not Connected (未接続)]** から **[Connected (接続済み)]** に変更されます

    ![[Authenticate users with S S O]\(S S O を使用してユーザーを認証する\) を有効にできる [Single Sign-On]\(シングル サインオン\) を示すスクリーンショット。](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>LinkedIn Learning のテスト ユーザーの作成

LinkedIn Learning アプリケーションでは、ジャストインタイムのユーザーのプロビジョニングがサポートされ、認証後に、ユーザーが自動的に、アプリケーションに作成されます。 LinkedIn Learning ポータルの管理者設定ページで、スイッチ **[Automatically Assign licenses (ライセンスを自動的に割り当てる)]** を切り替えて、ジャストインタイム プロビジョニングを有効にします。これにより、ユーザーにライセンスも割り当てられます。

   ![Azure AD のテスト ユーザーの作成](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる LinkedIn Learning のサインオン URL にリダイレクトされます。  

* LinkedIn Learning のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した LinkedIn Learning に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [LinkedIn Learning] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した LinkedIn Learning に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

LinkedIn Learning を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。