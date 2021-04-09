---
title: 'チュートリアル: Azure Active Directory と Greenhouse の統合 | Microsoft Docs'
description: Azure Active Directory と Greenhouse の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/25/2020
ms.author: jeedes
ms.openlocfilehash: 77f72d6c63231f0854b58470f86c65ffc81c9775
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731922"
---
# <a name="tutorial-azure-active-directory-integration-with-greenhouse"></a>チュートリアル: Azure Active Directory と Greenhouse の統合

このチュートリアルでは、Greenhouse と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Greenhouse を統合すると、次のことができます。

* Greenhouse にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Greenhouse に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Greenhouse でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE] 
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。 

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Greenhouse では、**SP** Initiated SSO がサポートされます

## <a name="adding-greenhouse-from-the-gallery"></a>ギャラリーから Greenhouse を追加する

Azure AD への Greenhouse の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Greenhouse を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Greenhouse**」と入力します。
1. 結果のパネルから **[Greenhouse]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-greenhouse"></a>Greenhouse の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Greenhouse に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Greenhouse の関連ユーザーとの間にリンク関係を確立する必要があります。

Greenhouse に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Greenhouse の SSO の構成](#configure-greenhouse-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Greenhouse テスト ユーザーの作成](#create-greenhouse-test-user)** - Greenhouse で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Greenhouse** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.greenhouse.io`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<companyname>.greenhouse.io`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Greenhouse クライアント サポート チーム](https://www.greenhouse.io/contact)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Greenhouse のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、Greenhouse へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Greenhouse]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-greenhouse-sso"></a>Greenhouse の SSO の構成

1. 別の Web ブラウザー ウィンドウで、管理者として Greenhouse Web サイトにサインインします。

1. **[Configure]\(構成\) > [Dev Center]\(デベロッパー センター\) > [Single Sign-On]\(シングル サインオン\)** に移動します。

    ![SSO ページのスクリーンショット](./media/greenhouse-tutorial/configure.png)

1. [Single Sign-On]\(シングル サインオン\) ページで、以下の手順を実行します。

    ![SSO 構成ページのスクリーンショット](./media/greenhouse-tutorial/sso-page.png)

    a. **[SSO Assertion Consumer URL]\(SSO アサーション コンシューマー URL\)** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションの **[応答 URL]** テキスト ボックスに貼り付けます。

    b. **[Entity ID/Issuer]\(エンティティ ID/発行者\)** ボックスに、Azure portal からコピーした **Azure AD ID** の値を貼り付けます。

    c. **[Single Sign-On URL]\(シングル サインオン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    d. Azure portal からダウンロードした **フェデレーション メタデータ XML** をメモ帳で開き、その内容を **[IdP Certificate Fingerprint]\(IdP 証明書フィンガープリント\)** ボックスに貼り付けます。

    e. ドロップダウンから **[Name Identifier Format]\(名前識別子形式\)** の値を選択します。

    f. **[Begin Testing]\(テストの開始\)** をクリックします。

    >[!NOTE]
    >または、 **[Choose File]\(ファイルの選択\)** オプションをクリックして、**フェデレーション メタデータ XML** ファイルをアップロードすることもできます。

### <a name="create-greenhouse-test-user"></a>Greenhouse のテスト ユーザーの作成

Azure AD ユーザーが Greenhouse にログインできるようにするには、ユーザーを Greenhouse にプロビジョニングする必要があります。 Greenhouse の場合、プロビジョニングは手動で行います。

>[!NOTE]
>他の Greenhouse ユーザー アカウント作成ツールや、Greenhouse から提供されている API を使用して、Azure AD ユーザー アカウントをプロビジョニングできます。 

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **Greenhouse** の企業サイトに管理者としてログインします。

2. **[Configure]\(構成\) > [Users]\(ユーザー\) > [New Users]\(新しいユーザー\)** に移動します
   
    ![ユーザー](./media/greenhouse-tutorial/create-user-1.png "ユーザー")

4. [**新規ユーザーを追加する**] セクションで、次の手順を実行します。
   
    ![[新しいユーザーの追加]](./media/greenhouse-tutorial/create-user-2.png "[新しいユーザーの追加]")

    a. [**Enter user emails**] ボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール アドレスを入力します。

    b. **[保存]** をクリックします。    
   
      >[!NOTE]
      >Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Greenhouse のサインオン URL にリダイレクトされます。 

* Greenhouse のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Greenhouse] タイルをクリックすると、Greenhouse のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Greenhouse を構成したら、組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。