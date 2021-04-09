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
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 5d5b07e761d5ed38cb2083054708265189bdd72f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651580"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Lucidchart の統合

このチュートリアルでは、Lucidchart と Azure Active Directory (Azure AD) を統合する方法について説明します。 Lucidchart を Azure AD と統合すると、次のことができます。

* Lucidchart にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Lucidchart に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Lucidchart でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Lucidchart では、**SP** によって開始される SSO がサポートされます
* Lucidchart では、**Just In Time** ユーザー プロビジョニングがサポートされます

## <a name="add-lucidchart-from-the-gallery"></a>ギャラリーからの Lucidchart の追加

Azure AD への Lucidchart の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Lucidchart を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Lucidchart**」と入力します。
1. 結果のパネルから **[Lucidchart]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Lucidchart の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Lucidchart に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Lucidchart の関連ユーザーとの間にリンク関係を確立する必要があります。

Lucidchart に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Lucidchart の SSO の構成](#configure-lucidchart-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Lucidchart のテスト ユーザーの作成](#create-lucidchart-test-user)** - Lucidchart で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Lucidchart** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

   **[サインオン URL]** ボックスに、URL として「`https://chart2.office.lucidchart.com/saml/sso/azure`」と入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Lucidchart のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Lucidchart へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Lucidchart]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-lucidchart-sso"></a>Lucidchart の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Lucidchart の企業サイトに管理者としてログインします。

2. 上部のメニューで、**[Team]** をクリックします。

    ![チーム](./media/lucidchart-tutorial/ic791190.png "チーム")

3. **[アプリケーション] \> [SAML の管理]** をクリックします。

    ![SAML の管理](./media/lucidchart-tutorial/ic791191.png "SAML の管理")

4. **[SAML 認証設定]** ダイアログ ページで、次の手順を実行します。

    a. **[SAML 認証]** を選択し、**[オプション]** をクリックします。

    ![[SAML Authentication Settings]](./media/lucidchart-tutorial/ic791192.png "SAML 認証設定")

    b. **[ドメイン]** ボックスにドメインを入力して **[証明書を変更]** をクリックします。

    ![[証明書の変更]](./media/lucidchart-tutorial/ic791193.png "証明書の変更")

    c. ダウンロードしたメタデータ ファイルを開いて内容をコピーし、**[メタデータをアップロード]** ボックスに貼り付けます。

    ![[Upload Metadata (メタデータのアップロード)]](./media/lucidchart-tutorial/ic791194.png "メタデータのアップロード")

    d. **[Automatically Add new users to the team]\(新しいユーザーを自動的にチームに追加する\)** を選んで、**[Save changes]\(変更を保存\)** をクリックします。

    ![[変更の保存]](./media/lucidchart-tutorial/ic791195.png "[変更の保存]")

### <a name="create-lucidchart-test-user"></a>Lucidchart のテスト ユーザーの作成

Lucidchart へのユーザー プロビジョニングの構成にあたって必要な操作はありません。  割り当て済みユーザーがアクセス パネルを使用して Lucidchart にログインしようとすると、そのユーザーが存在するかどうかが Lucidchart によって確認されます。  

使用可能なユーザー アカウントがない場合、ユーザー アカウントは Lucidchart により自動的に作成されます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Lucidchart のサインオン URL にリダイレクトされます。 

* Lucidchart のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Lucidchart] タイルをクリックすると、SSO を設定した Lucidchart に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

 Lucidchart を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](/cloud-app-security/proxy-deployment-aad)をご覧ください。