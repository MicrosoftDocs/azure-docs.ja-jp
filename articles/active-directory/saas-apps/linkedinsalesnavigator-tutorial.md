---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と LinkedIn Sales Navigator の統合 | Microsoft Docs
description: Azure Active Directory と LinkedIn Sales Navigator の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.openlocfilehash: 3f4d4d1e0fc44b9fb031ce5e3e45219fa8169562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92458508"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と LinkedIn Sales Navigator の統合

このチュートリアルでは、LinkedIn Sales Navigator と Azure Active Directory (Azure AD) を統合する方法について説明します。 LinkedIn Sales Navigator を Azure AD と統合すると、次のことができます。

* LinkedIn Sales Navigator にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して LinkedIn Sales Navigator に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* LinkedIn Sales Navigator でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* LinkedIn Sales Navigator では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* LinkedIn Sales Navigator では、**Just-In-Time** ユーザー プロビジョニングがサポートされます
* LinkedIn Sales Navigator では、[**自動化された** ユーザー プロビジョニング](linkedinsalesnavigator-provisioning-tutorial.md)がサポートされます

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>ギャラリーからの LinkedIn Sales Navigator ナビゲーターの追加

Azure AD への LinkedIn Sales Navigator の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に LinkedIn Sales Navigator を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**LinkedIn Sales Navigator**」と入力します。
1. 結果のパネルから **[LinkedIn Sales Navigator]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>LinkedIn Sales Navigator の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、LinkedIn Sales Navigator に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと LinkedIn Sales Navigator の関連ユーザーとの間にリンク関係を確立する必要があります。

LinkedIn Sales Navigator で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[LinkedIn Sales Navigator の SSO の構成](#configure-linkedin-sales-navigator-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[LinkedIn Sales Navigator のテスト ユーザーの作成](#create-linkedin-sales-navigator-test-user)** - LinkedIn Sales Navigatorで B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **LinkedIn Sales Navigator** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、**エンティティ ID** の値を入力します。エンティティ ID の値は、このチュートリアルで後述する Linkedin Portal からコピーします。

    b. **[応答 URL]** ボックスに、**Assertion Consumer Access (ACS) URL** の値を入力します。Assertion Consumer Access (ACS) URL の値は、このチュートリアルで後述する LinkedIn Portal からコピーします。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator` という形式で URL を入力します。

1. LinkedIn Sales Navigator アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングをSAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

1. その他に、LinkedIn Sales Navigator アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | --- | --- |
    | email| User.mail |
    | department| user.department |
    | firstname| User.givenname |
    | lastname| User.surname |
    | 一意のユーザー ID | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[LinkedIn Sales Navigator のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に LinkedIn Sales Navigator へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[LinkedIn Sales Navigator]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-linkedin-sales-navigator-sso"></a>LinkedIn Sales Navigator の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として **LinkedIn Sales Navigator** テナントにサインオンします。

1. **[Account Center (アカウント センター)]** で、 **[Settings (設定)]** の下の **[Global Settings (グローバル設定)]** をクリックします。 さらに、ドロップダウン リストから **[Sales Navigator]** を選択します。

    ![[Sales Navigator] を選択できる [Application Settings]\(アプリケーションの設定\) を示すスクリーンショット。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. **[OR Click Here to load and copy individual fields from the form (または、ここをクリックしてフォームから個々のフィールドを読み込み、コピーする)]** をクリックして、次の手順を実行します。

    ![説明されている値を入力できる [Single Sign-On]\(シングル サインオン\) を示すスクリーンショット。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. **[Entity ID]\(エンティティ ID\)** をコピーして、Azure portal の **[基本的な SAML 構成]** にある **[識別子]** ボックスに貼り付けます。

    b. **Assertion Consumer Access (ACS) URL** をコピーし、Azure portal で **[基本的な SAML 構成]** の **[応答 URL]** ボックスに貼り付けます。

1. **[LinkedIn Admin Settings (LinkedIn 管理者設定)]** セクションに移動します。 **[Upload XML file]\(XML ファイルのアップロード\)** オプションをクリックして、Azure portal からダウンロードした XML ファイルをアップロードします。

    ![X M L ファイルをアップロードできる [Configure the LinkedIn service provider S S O settings]\(LinkedIn サービス プロバイダーの S S O 設定の構成\) を示すスクリーンショット。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. **[ON (オン)]** をクリックして SSO を有効にします。 SSO の状態が **[Not Connected (未接続)]** から **[Connected (接続済み)]** に変更されます

    ![[Authenticate users with S S O]\(S S O を使用してユーザーを認証する\) を有効にできる [Single Sign-On]\(シングル サインオン\) を示すスクリーンショット。](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>LinkedIn Sales Navigator のテスト ユーザーの作成

Linked Sales Navigator アプリケーションでは、ジャストインタイム (JIT) ユーザー プロビジョニングがサポートされ、認証後にユーザーがアプリケーション内に自動的に作成されます。 **[Automatically assign licenses (ライセンスを自動的に割り当てる)]** をアクティブ化して、ユーザーにライセンスを割り当てます。

   ![Azure AD のテスト ユーザーの作成](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [LinkedIn Sales Navigator] タイルをクリックすると、SSO を設定した LinkedIn Sales Navigator に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で LinkedIn Sales Navigator を試す](https://aad.portal.azure.com/)