---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と CloudPassage の統合 | Microsoft Docs
description: Azure Active Directory と CloudPassage の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.openlocfilehash: b720f7e49fc0679de5c3f430122bab05d5b706f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455761"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cloudpassage"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と CloudPassage の統合

このチュートリアルでは、CloudPassage と Azure Active Directory (Azure AD) を統合する方法について説明します。 CloudPassage を Azure AD と統合すると、次のことができます。

* CloudPassage にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して CloudPassage に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* CloudPassage でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* CloudPassage では、**SP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-cloudpassage-from-the-gallery"></a>ギャラリーからの CloudPassage の追加

Azure AD への CloudPassage の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に CloudPassage を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**CloudPassage**」と入力します。
1. 結果のパネルから **CloudPassage** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-cloudpassage"></a>CloudPassage の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、CloudPassage に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと CloudPassage の関連ユーザーとの間でリンク関係を確立する必要があります。

CloudPassage に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[CloudPassage の SSO の構成](#configure-cloudpassage-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[CloudPassage のテスト ユーザーの作成](#create-cloudpassage-test-user)** - CloudPassage で B.Simon に対応するユーザーを作成し、Azure AD のこのユーザーにリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **CloudPassage** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

     a. **[サインオン URL]** ボックスに、`https://portal.cloudpassage.com/saml/init/accountid` という形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://portal.cloudpassage.com/saml/consume/accountid` のパターンを使用して URL を入力します。 この属性の値は、CloudPassage ポータルの **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで **[SSO Setup documentation (SSO セットアップのドキュメント)]** をクリックすることで取得できます。

    ![このスクリーンショットは、[S S O Setup Documentation]\(S S O セットアップのドキュメント\) リンクがコールアウトされた状態の CloudPassage ポータルを示しています。](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と応答 URL でこれらの値を更新してください。 これらの値を取得するには、[CloudPassage クライアント サポート チーム](https://www.cloudpassage.com/company/contact/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. CloudPassage アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)

1. その他に、CloudPassage アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | firstname |User.givenname |
    | lastname |User.surname |
    | email |User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[CloudPassage のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に CloudPassage へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[CloudPassage]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-cloudpassage-sso"></a>CloudPassage の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として CloudPassage 企業サイトにサインオンします。

1. 上部のメニューの **[Settings (設定)]** をクリックし、 **[Site Administration (サイトの管理)]** をクリックします。 
   
    ![このスクリーンショットは、[Site Administration]\(サイトの管理\) が選択された状態の CloudPassage サイトを示しています。][12]

1. **[Authentication Settings (認証設定)]** タブをクリックします。 
   
    ![このスクリーンショットは、[Authentication Settings]\(認証設定\) タブが選択された状態の CloudPassage サイトを示しています。][13]

1. **[Single Sign-on Settings (シングル サインオンの設定)]** セクションで、次の手順に従います。 
   
    ![このスクリーンショットは、[Single Sign-on Settings]\(シングル サインオンの設定\) セクションを示しています。ここで、この手順の情報を入力します。][14]

    a. **[Enable Single sign-on(SSO)(SSO Setup Documentation)]\(シングル サインオン (SSO)(SSO セットアップ ドキュメント) を有効にする\)** チェックボックスをオンにします。
    
    b. **Azure AD の識別子** を **[SAML issuer URL]\(SAML 発行者 URL\)** ボックスに貼り付けます。
  
    c. **ログイン URL** を **[SAML endpoint URL]\(SAML エンドポイント URL\)** ボックスに貼り付けます。
  
    d. **ログアウト URL** を **[Logout landing page]\(ログアウト ランディング ページ\)** ボックスに貼り付けます。
  
    e. ダウンロードした証明書をメモ帳で開き、ダウンロードした証明書の内容をクリップボードにコピーして、 **[x 509 certificate]\(x 509 証明書\)** ボックスに貼り付けます。
  
    f. **[保存]** をクリックします。

### <a name="create-cloudpassage-test-user"></a>CloudPassage テスト ユーザーの作成

このセクションの目的は、CloudPassage で B.Simon というユーザーを作成することです。

**CloudPassage で B.Simon というユーザーを作成するには、次の手順に従います。**

1. **CloudPassage** 企業サイトに管理者としてサインオンします。 

1. 上部にあるツールバーの **[Settings (設定)]** をクリックし、 **[Site Administration (サイトの管理)]** をクリックします。 
   
    ![このスクリーンショットは、[Site Administration]\(サイトの管理\) が選択された状態の CloudPassage を示しています。][22] 

1. **[Users (ユーザー)]** タブをクリックし、 **[Add New User (新しいユーザーの追加)]** をクリックします。 
   
    ![このスクリーンショットは、[Users]\(ユーザー\) タブと [Add New User]\(新しいユーザーの追加\) オプションが選択された状態の CloudPassage の [Site Administration]\(サイトの管理\) を示しています。][23]

1. **[新しいユーザーの追加]** セクションで、次の手順を実行します。 
   
    ![このスクリーンショットは、[Add New User]\(新しいユーザーの追加\) セクションを示しています。ここで、ユーザー情報を指定できます。][24]
    
    a. **[名]** ボックスに「Britta」と入力します。 
  
    b. **[姓]** ボックスに「Simon」と入力します。
  
    c. **[Username]** 、 **[Email]** 、 **[Retype Email]** の各ボックスに、Britta の Azure AD でのユーザー名を入力します。
  
    d. **[Access Type]** で **[Enable Halo Portal Access]** を選択します。
  
    e. **[追加]** をクリックします。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [CloudPassage] タイルをクリックすると、SSO を設定した CloudPassage に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で CloudPassage を試す](https://aad.portal.azure.com/)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png