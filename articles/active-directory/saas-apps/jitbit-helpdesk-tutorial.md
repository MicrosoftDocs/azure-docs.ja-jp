---
title: チュートリアル:Azure Active Directory と Jitbit Helpdesk の統合 | Microsoft Docs
description: Azure Active Directory と Jitbit Helpdesk の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: b3fbf73ab51092f614a416355477fc552a404fd3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581808"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>チュートリアル:Azure Active Directory と Jitbit Helpdesk の統合

このチュートリアルでは、Jitbit Helpdesk と Azure Active Directory (Azure AD) を統合する方法について説明します。 Jitbit Helpdesk と Azure AD を統合すると、次のことができます。

* Jitbit Helpdesk にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して Jitbit Helpdesk に自動的にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Jitbit Helpdesk でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* Jitbit Helpdesk では、**SP** Initiated SSO がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-jitbit-helpdesk-from-the-gallery"></a>ギャラリーからの Jitbit Helpdesk の追加

Azure AD への Jitbit Helpdesk の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jitbit Helpdesk を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Jitbit Helpdesk**」と入力します。
1. 結果のパネルから **[Jitbit Helpdesk]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-jitbit-helpdesk"></a>Jitbit Helpdesk の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Jitbit Helpdesk に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Jitbit Helpdesk の関連ユーザーとの間にリンク関係を確立する必要があります。

Jitbit Helpdesk に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Jitbit Helpdesk SSO の構成](#configure-jitbit-helpdesk-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Jitbit Helpdesk テスト ユーザーの作成](#create-jitbit-helpdesk-test-user)** - Jitbit Helpdesk で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Jitbit Helpdesk** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** テキスト ボックスに、次のパターンを使用していずれかの URL を入力します。
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > これは実際の値ではありません。 実際のサインオン URL でこの値を更新してください。 この値を取得するには、[Jitbit Helpdesk クライアント サポート チーム](https://www.jitbit.com/support/)にお問い合わせください。

    b. **[識別子 (エンティティ ID)]** ボックスに `https://www.jitbit.com/web-helpdesk/` という URL を入力します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[Jitbit Helpdesk の設定]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Jitbit Helpdesk へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Jitbit Helpdesk]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-jitbit-helpdesk-sso"></a>Jitbit Helpdesk SSO の構成

1. 別の Web ブラウザーのウィンドウで、Jitbit Helpdesk の企業サイトに管理者としてサインインします。

1. 上部のツールバーで **[管理]** をクリックします。

    ![管理](./media/jitbit-helpdesk-tutorial/settings.png "管理")

1. **[全般設定]** をクリックします。

    ![[General Settings]\(全般設定\) リンクを示すスクリーンショット。](./media/jitbit-helpdesk-tutorial/general.png "ユーザー、会社、およびアクセス許可")

1. **[認証設定]** 構成セクションで、次の手順を実行します。

    ![[Authentication settings]\(認証設定\)](./media/jitbit-helpdesk-tutorial/authentication.png "[認証設定]")

    a. **[Enable SAML 2.0 single sign on]\(SAML 2.0 シングル サインオンを有効にする\)** を選択して、**OneLogin** でシングル サインオン (SSO) を使用してサインインします。

    b. **[EndPoint URL]\(EndPoint の URL\)** ボックスに、Azure portal からコピーした **[ログイン URL]** の値を貼り付けます。

    c. **base-64** でエンコードされた証明書をメモ帳で開き、その内容をクリップボードにコピーして、 **[X.509 証明書]** ボックスに貼り付けます。

    d. **[変更を保存]** をクリックします。

### <a name="create-jitbit-helpdesk-test-user"></a>Jitbit Helpdesk テスト ユーザーの作成

Azure AD ユーザーが Jitbit Helpdesk にサインインできるようにするには、そのユーザーを Jitbit Helpdesk にプロビジョニングする必要があります。 Jitbit Helpdesk の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Jitbit Helpdesk** テナントにサインインします。

1. 上部のメニューで **[管理]** をクリックします。

    ![管理](./media/jitbit-helpdesk-tutorial/settings.png "管理")

1. **[ユーザー、会社、およびアクセス許可]** をクリックします。

    ![[Users, companies, and permissions]\(ユーザー、会社、およびアクセス許可\)](./media/jitbit-helpdesk-tutorial/users.png "ユーザー、会社、およびアクセス許可")

1. **[ユーザーの追加]** をクリックします。

    ![ユーザーの追加](./media/jitbit-helpdesk-tutorial/add.png "ユーザーの追加")

1. [作成] セクションで、プロビジョニングする Azure AD アカウントのデータを次のように入力します。

    ![作成](./media/jitbit-helpdesk-tutorial/create-section.png "作成")

   a. **[Username]\(ユーザー名\)** ボックスに、ユーザーのユーザー名を入力します (この例では **BrittaSimon**)。

   b. **[電子メール]** ボックスに、ユーザーの電子メール ( **BrittaSimon@contoso.com** など) を入力します。

   c. **[名]** ボックスに、ユーザーの名を入力します (この例では **Britta**)。

   d. **[姓]** ボックスに、ユーザーの姓を入力します (この例では **Simon**)。

   e. **Create** をクリックしてください。

> [!NOTE]
> Jitbit Helpdesk から提供されている他の Jitbit Helpdesk ユーザー アカウント作成ツールまたは API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Jitbit Helpdesk のサインオン URL にリダイレクトされます。 

* Jitbit Helpdesk のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Jitbit Helpdesk] タイルをクリックすると、Jitbit Helpdesk のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Jitbit Helpdesk を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。