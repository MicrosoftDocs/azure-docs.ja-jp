---
title: 'チュートリアル: Azure Active Directory と FreshDesk の統合 | Microsoft Docs'
description: Azure Active Directory と FreshDesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/20/2021
ms.author: jeedes
ms.openlocfilehash: e1394eafdfd733b5d69a4d4abbb6b218b4c8c10d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651906"
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>チュートリアル: Azure Active Directory と FreshDesk の統合

このチュートリアルでは、FreshDesk と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と FreshDesk を統合すると、次のことができます。

* FreshDesk にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して FreshDesk に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。
 
* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* FreshDesk でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* FreshDesk では、**SP** によって開始される SSO がサポートされます

## <a name="add-freshdesk-from-the-gallery"></a>ギャラリーからの FreshDesk の追加

Azure AD への FreshDesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に FreshDesk を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**FreshDesk**」と入力します。
1. 結果のパネルから **FreshDesk** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-freshdesk"></a>FreshDesk の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、FreshDesk に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと FreshDesk の関連ユーザーとの間にリンク関係を確立する必要があります。

FreshDesk に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[FreshDesk の SSO の構成](#configure-freshdesk-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[FreshDesk テスト ユーザーの作成](#create-freshdesk-test-user)** - FreshDesk で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

1. Azure portal の **FreshDesk** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。

    b. **[Identifier (Entity ID)]\(ID (エンティティ ID)\)** ボックスに、`https://<tenant-name>.freshdesk.com` のパターン、または FreshDesk から示されたその他の値を使用して URL を入力します。
     
    c. **[応答 URL]** ボックスに、`https://<tenant-name>.freshdesk.com/login/saml` のパターンを使用して URL を入力します
    
    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、識別子、および応答 URL で更新してください。 これらの値を取得するには、[FreshDesk サポート チーム](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. FreshDesk アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示したものです。 **[一意のユーザー ID]** は **user.userprincipalname** にマップされていますが、FreshDesk ではこの要求は **user.mail** にマップされている必要があるため、[編集] アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで **[ダウンロード]** をクリックして、要件に適した特定のオプションの **証明書 (Base64)** をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[FreshDesk の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に FreshDesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[FreshDesk]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-freshdesk-sso"></a>FreshDesk の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Freshdesk 企業サイトに管理者としてログインします。

2. **[Security]\(セキュリティ\)** アイコンを選択し、 **[Security]\(セキュリティ\)** セクションで次の手順のようにします。

    ![シングル サインオン](./media/freshdesk-tutorial/configure-1.png "シングル サインオン")
  
    a. **[Single Sign On]\(シングル サインオン\)** で **[On]\(オン\)** を選択します。

    b. **[Login Method]\(ログイン方法\)** で、 **[SAML SSO]** を選択します。

    c. **[Entity ID provided by the IdP]\(IdP によって提供されたエンティティ ID\)** テキスト ボックスに、Azure portal からコピーした **エンティティ ID** の値を貼り付けます。

    d. **[SAML SSO URL]** テキストボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    e. **[Signing Options]\(署名オプション\)** で、ドロップダウンから **[Only Signed Assertions]\(署名されたアサーションのみ\)** を選択します。

    f. **[ログアウト URL]** テキストボックスに、Azure portal からコピーした **ログアウト URL** の値を貼り付けます。

    g. **[Security Certificate]\(セキュリティ証明書\)** テキスト ボックスに、前に取得した **証明書 (Base64)** の値を貼り付けます。
  
    h. **[保存]** をクリックします。

## <a name="create-freshdesk-test-user"></a>FreshDesk のテスト ユーザーの作成

Azure AD ユーザーが FreshDesk にログインできるようにするには、そのユーザーを FreshDesk にプロビジョニングする必要があります。  
FreshDesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Freshdesk** テナントにログインします。

1. 左側のメニューで **[Admin]\(管理者\)** をクリックし、 **[General Settings]\(全般設定\)** タブで **[Agents]\(エージェント\)** をクリックします。
  
    ![[エージェント]](./media/freshdesk-tutorial/create-user-1.png "エージェント")

1. **[新しいエージェント]** をクリックします。

    ![新しいエージェント](./media/freshdesk-tutorial/create-user-2.png "[新しいエージェント]")

1. [Agent Information]\(エージェント情報\) ダイアログで、必要なフィールドを入力し、 **[Create agent]\(エージェントの作成\)** をクリックします。

    ![エージェント情報](./media/freshdesk-tutorial/create-user-3.png "エージェント情報")

    >[!NOTE]
    >Azure AD のアカウント所有者には、そのアカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。
    >
    >[!NOTE]
    >その他の Freshdesk ユーザー アカウント作成ツールや、Freshdesk から提供されている API を使用して、Azure AD ユーザー アカウントを Freshdesk にプロビジョニングできます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる FreshDesk のサインオン URL にリダイレクトされます。 

* FreshDesk のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [FreshDesk] タイルをクリックすると、SSO を設定した FreshDesk に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

FreshDesk を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。