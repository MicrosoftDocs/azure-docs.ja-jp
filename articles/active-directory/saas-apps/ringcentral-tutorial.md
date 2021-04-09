---
title: チュートリアル:Azure Active Directory と RingCentral の統合 | Microsoft Docs
description: Azure Active Directory と RingCentral の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: 3c8126c051457d740d1a5414a130b4ee3fffcd8c
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954124"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>チュートリアル:Azure Active Directory と RingCentral の統合

このチュートリアルでは、RingCentral と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と RingCentral を統合すると、次のことができます。

* RingCentral にアクセスするユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して RingCentral に自動的にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* RingCentral でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* RingCentral では、**IDP** Initiated SSO がサポートされます。

## <a name="add-ringcentral-from-the-gallery"></a>ギャラリーから RingCentral を追加する

Azure AD への RingCentral の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから RingCentral を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**RingCentral**」と入力します。
1. 結果のパネルから **[RingCentral]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>RingCentral の Azure AD SSO の構成とテスト

**Britta Simon** というテスト ユーザーを使用して、RingCentral で Azure AD の SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーと RingCentral の関連ユーザーとの間にリンク関係を確立する必要があります。

RingCentral に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[RingCentral SSO の構成](#configure-ringcentral-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[RingCentral のテスト ユーザーの作成](#create-ringcentral-test-user)** - RingCentral で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **RingCentral** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、**サービス プロバイダー メタデータ ファイル** がある場合は、次の手順に従います。

    1. **[メタデータ ファイルをアップロードします]** をクリックします。
    1. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。
    1. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、 **[基本的な SAML 構成]** セクションに自動的に設定されます。

    > [!Note]
    > RingCentral SSO 構成ページで、**サービス プロバイダー メタデータ ファイル** を取得します。これについては後で説明します。

1. **サービス プロバイダー メタデータ ファイル** がない場合は、次のフィールドに値を入力します。

    a. **[識別子]** ボックスに、URL のいずれかを入力します。
  
    | 識別子 |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. **[応答 URL]** ボックスに、URL のいずれかを入力します。

    | [応答 URL] |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. **Set up Single Sign-On with SAML\(SAML でのシングルサインオンの設定** ページの **SAML 署名証明書** セクションで、コピー ボタンをクリックして **App Federation Metadata Url\(アプリのフェデレーション メタデータ URL)** をコピーして、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で Britta Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`Britta Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `BrittaSimon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に RingCentral へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[RingCentral]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ringcentral-sso"></a>RingCentral SSO の構成

1. RingCentral 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[RingCentral のセットアップ]** をクリックすると、RingCentral アプリケーションに移動します。 そこから、管理者の資格情報を入力して RingCentral にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 7 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. RingCentral を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として RingCentral 企業サイトにサインインして、次の手順を実行します。

1. 上部の **[ツール]** をクリックします。

    ![RingCentral 企業サイトから選択された [ツール] を示すスクリーンショット。](./media/ringcentral-tutorial/ringcentral-1.png)

1. **[シングル サインオン]** に移動します。

    ![[ツール] メニューから選択された [Single Sign-On]\(シングル サインオン\) を示すスクリーンショット。](./media/ringcentral-tutorial/ringcentral-2.png)

1. **[Single Sign-on]\(シングル サインオン\)** ページの **[SSO Configuration]\(SSO 構成\)** セクションで、 **[Step 1]\(ステップ 1\)** の **[Edit]\(編集\)** をクリックして、次の手順を実行します。

    ![[Edit]\(編集\) を選択できる [S S O Configuration]\(S S O 構成\) ページを示すスクリーンショット。](./media/ringcentral-tutorial/ringcentral-3.png)

1. **[Set up Single Sign-on]\(シングル サインオンのセットアップ\)** ページで、次の手順を実行します。

    ![I D P メタデータをアップロードできる [Set up Single Sign-On]\(シングル サインオンのセットアップ\) ページを示すスクリーンショット。](./media/ringcentral-tutorial/ringcentral-4.png)

    a. **[Browse]\(参照\)** をクリックし、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。

    b. メタデータをアップロードすると、 **[SSO General Information]\(SSO の一般情報\)** セクションの値が自動的に設定されます。

    c. **[Attribute Mapping]\(属性マッピング\)** セクションの **[Map Email Attribute to]\(メール属性のマップ先\)** で `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` を選択します

    d. **[保存]** をクリックします。

    e. **[Step 2]\(ステップ 2\)** の **[Download]\(ダウンロード\)** をクリックして **サービス プロバイダー メタデータ ファイル** をダウンロードし、Azure portal 内でそれを **[基本的な SAML 構成]** セクションにアップロードして、 **[識別子]** と **[応答 URL]** を自動的に設定します。

    ![[Download]\(ダウンロード\) を選択できる [S S O Configuration]\(S S O 構成\) ページを示すスクリーンショット。](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. 同じページで **[Enable SSO]\(SSO の有効化\)** セクションに移動し、次の手順を実行します。

    ![構成を完了できる [Enable S S O]\(S S O の有効化\) セクションを示すスクリーンショット。](./media/ringcentral-tutorial/ringcentral-5.png)

    * **[Enable SSO Service]\(SSO サービスを有効にする\)** を選択します。

    * **[Allow users to log in with SSO or RingCentral credential]\(ユーザーが SSO または RingCentral 資格情報でログインできるようにする\)** を選択します。

    * **[保存]** をクリックします。

### <a name="create-ringcentral-test-user"></a>RingCentral のテスト ユーザーの作成

このセクションでは、RingCentral で Britta Simon というユーザーを作成します。 [RingCentral クライアント サポート チーム](https://success.ringcentral.com/RCContactSupp)と連携し、RingCentral プラットフォームにユーザーを追加してください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した RingCentral に自動的にサインインされます。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [RingCentral] タイルをクリックすると、SSO を設定した RingCentral に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

RingCentral を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。