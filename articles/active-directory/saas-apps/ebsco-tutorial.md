---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と EBSCO の統合 | Microsoft Docs'
description: Azure Active Directory と EBSCO の間にシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/11/2019
ms.author: jeedes
ms.openlocfilehash: 2f7f017d773e7708b2206029769df7977b12e6f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92454339"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ebsco"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と EBSCO の統合

このチュートリアルでは、EBSCO と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と EBSCO を統合すると、次のことができます。

* EBSCO にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して EBSCO に自動的にサインインできるようにすることができます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* EBSCO でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* EBSCO では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます
* EBSCO では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-ebsco-from-the-gallery"></a>ギャラリーからの EBSCO の追加

Azure AD への EBSCO の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に EBSCO を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**EBSCO**」と入力します。
1. 結果のパネルから **[EBSCO]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-ebsco"></a>EBSCO の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、EBSCO に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと EBSCO の関連ユーザーとの間にリンク関係を確立する必要があります。

EBSCO に対する Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[EBSCO SSO の構成](#configure-ebsco-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[EBSCO テスト ユーザーの作成](#create-ebsco-test-user)** - EBSCO で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **EBSCO** アプリケーション統合ページで、**[管理]** セクションを探して、**[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    **[識別子]** テキスト ボックスに、`pingsso.ebscohost.com` という URL を入力します。

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>` という形式で URL を入力します。

    > [!NOTE]
    > サインオン URL は実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 これらの値を取得するには、[EBSCO クライアント サポート チーム](mailto:support@ebsco.com)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

    o   **一意の要素:**  

    o   **[Custid]** = 一意の EBSCO カスタマー ID を入力 

    o   **[プロファイル]** = クライアントはリンクを調整して、ユーザーを (EBSCO から購入したものに基づいて) 特定のプロファイルに転送できます。 特定のプロファイル ID を入力できます。 メインの ID は eds (EBSCO Discovery Service) と ehost (EBSOCOhost データベース) です。 同じことに関する手順が[こちら](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)にあります。

1. EBSCO アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/default-attributes.png)

    > [!Note]
    > **name** 属性は必須であり、EBSCO アプリケーションの **名前識別子の値** とマッピングされます。 これは既定で追加されるため、手動で追加する必要はありません。

1. その他に、EBSCO アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。それらの属性を次に示します。 これらの属性も値が事前に設定されますが、要件に従ってそれらの値を確認することができます。

    | 名前 | ソース属性|
    | ---------------| --------------- |
    | FirstName   | User.givenname |
    | LastName   | User.surname |
    | Email   | User.mail |

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[EBSCO のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に EBSCO へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[EBSCO]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-ebsco-sso"></a>EBSCO SSO の構成

**EBSCO** 側でシングル サインオンを構成するには、ダウンロードした **フェデレーション メタデータ XML** と Azure portal からコピーした適切な URL を [EBSCO サポート チーム](mailto:support@ebsco.com)に送信する必要があります。 サポート チームはこれを設定して、SAML SSO 接続が両方の側で正しく設定されるようにします。

### <a name="create-ebsco-test-user"></a>EBSCO のテスト ユーザーの作成

EBSCO の場合、ユーザー プロビジョニングは自動的に行われます。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

Azure AD によって必要なデータが EBSCO アプリケーションに渡されます。 EBSCO のユーザー プロビジョニングは自動であるか、1 回限りのフォームが要求されます。 これは、個人設定が保存された、多数の既存の EBSCOhost アカウントをクライアントが持っているかどうかによって異なります。 実装中、[EBSCO サポート チーム](mailto:support@ebsco.com)についても同じことが考えられます。 どちらの場合でも、テスト前にクライアントが EBSCOhost アカウントを作成する必要はありません。

   > [!Note]
   > EBSCOhost ユーザーのプロビジョニング/パーソナル化を自動化できます。 Just-In-Time ユーザー プロビジョニングについて、[EBSCO サポート チーム](mailto:support@ebsco.com)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

1. アクセス パネルで EBSCO のタイルをクリックすると、EBSCO アプリケーションに自動的にサインオンします。
アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

1. アプリケーションにログインしたら、右上隅の **[サインイン]** ボタンをクリックします。

    ![アプリケーションの一覧の EBSCO サインイン](./media/ebsco-tutorial/tutorial_ebsco_signin.png)

1. 組織ログインまたは SAML ログインを、**[Link your existing MyEBSCOhost account to your institution account now]\(今すぐ既存の MyEBSCOhost アカウントを組織アカウントにリンクする\)** または **[Create a new MyEBSCOhost account and link it to your institution account]\(新しい MyEBSCOhost アカウントを作成して組織アカウントにリンクする\)** に関連付けることを求める 1 回限りのプロンプトが表示されます。 アカウントは、EBSCOhost アプリケーションのパーソナル化に使用されます。 **[新しいアカウントの作成]** オプションを選択すると、次のスクリーンショットのように、SAML 応答の値が事前入力されたパーソナル化用のフォームが表示されます。 **[続行]** をクリックしてこの選択を保存します。
    
     ![アプリケーションの一覧の EBSCO ユーザー](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. 以上の設定を完了したら、Cookie/キャッシュをクリアして再びログインします。 これで再び手動でサインインする必要はなくなり、パーソナル化の設定は保持されます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で EBSCO を試す](https://aad.portal.azure.com/)