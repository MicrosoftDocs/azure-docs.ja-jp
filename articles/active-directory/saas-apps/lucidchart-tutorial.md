---
title: チュートリアル:Azure Active Directory と Lucidchart の統合 | Microsoft Docs
description: Azure Active Directory と Lucidchart の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: d2a63110a3527bd6b164f6ec72b99ee66c76564b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88550305"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Lucidchart の統合

このチュートリアルでは、Lucidchart と Azure Active Directory (Azure AD) を統合する方法について説明します。 Lucidchart を Azure AD と統合すると、次のことができます。

* Lucidchart にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Lucidchart に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Lucidchart でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Lucidchart では、**SP** によって開始される SSO がサポートされます
* Lucidchart では、**Just In Time** ユーザー プロビジョニングがサポートされます
* Lucidchart を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-lucidchart-from-the-gallery"></a>ギャラリーからの Lucidchart の追加

Azure AD への Lucidchart の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lucidchart を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Lucidchart**」と入力します。
1. 結果のパネルから **[Lucidchart]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-lucidchart"></a>Lucidchart の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Lucidchart に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Lucidchart の関連ユーザーとの間にリンク関係を確立する必要があります。

Lucidchart で Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Lucidchart の SSO の構成](#configure-lucidchart-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Lucidchart のテスト ユーザーの作成](#create-lucidchart-test-user)** - Lucidchart で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Lucidchart** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

   **[サインオン URL]** テキスト ボックスに、「`https://chart2.office.lucidchart.com/saml/sso/azure`」と入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Lucidchart のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

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

このセクションでは、B.Simon に Lucidchart へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Lucidchart]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-lucidchart-sso"></a>Lucidchart の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Lucidchart の企業サイトに管理者としてログインします。

2. 上部のメニューで **[チーム]** をクリックします。

    ![チーム](./media/lucidchart-tutorial/ic791190.png "チーム")

3. **[アプリケーション] \> [SAML の管理]** をクリックします。

    ![SAML の管理](./media/lucidchart-tutorial/ic791191.png "SAML の管理")

4. **[SAML Authentication Settings]** ダイアログ ページで、次の手順に従います。

    a. **[SAML 認証を有効にする]** を選んで、 **[オプション]** をクリックします。

    ![[SAML Authentication Settings]](./media/lucidchart-tutorial/ic791192.png "SAML 認証設定")

    b. **[ドメイン]** ボックスで、使用するドメインを入力して、 **[証明書の変更]** をクリックします。

    ![[証明書の変更]](./media/lucidchart-tutorial/ic791193.png "証明書の変更")

    c. ダウンロードしたメタデータ ファイルを開いて内容をコピーし、 **[Upload Metadata (メタデータのアップロード)]** ボックスに貼り付けます。

    ![[Upload Metadata (メタデータのアップロード)]](./media/lucidchart-tutorial/ic791194.png "メタデータのアップロード")

    d. **[Automatically Add new users to the team]\(新しいユーザーを自動的にチームに追加する\)** を選んで、 **[Save changes]\(変更を保存\)** をクリックします。

    ![[変更の保存]](./media/lucidchart-tutorial/ic791195.png "[変更の保存]")

### <a name="create-lucidchart-test-user"></a>Lucidchart のテスト ユーザーの作成

Lucidchart へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  割り当て済みユーザーがアクセス パネルを使用して Lucidchart にログインしようとすると、そのユーザーが存在するかどうかが Lucidchart によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lucidchart により自動的に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Lucidchart] タイルをクリックすると、SSO を設定した Lucidchart に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD で Lucidchart を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Lucidchart を保護する方法](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
