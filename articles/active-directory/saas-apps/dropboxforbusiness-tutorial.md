---
title: チュートリアル:Azure Active Directory と Dropbox Business の統合 | Microsoft Docs
description: Azure Active Directory と Dropbox Business の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 41f6db8cf2454c224addac525e9d039954a95712
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601501"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>チュートリアル:Dropbox Business と Azure Active Directory の統合

このチュートリアルでは、Dropbox Business と Azure Active Directory (Azure AD) を統合する方法について説明します。 Dropbox Business を Azure AD に統合すると、次のことを実行できます。

* Dropbox Business にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Dropbox Business に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Dropbox Business でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

* このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Dropbox Business では、**SP** Initiated SSO がサポートされます。

* Dropbox Business では、[自動化されたユーザー プロビジョニングとプロビジョニング解除](dropboxforbusiness-tutorial.md)がサポートされます。

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="add-dropbox-business-from-the-gallery"></a>ギャラリーからの Dropbox Business の追加

Azure AD への Dropbox Business の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Dropbox Business を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Dropbox Business**」と入力します。
1. 結果のパネルから **[Dropbox Business]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-dropbox-business"></a>Dropbox Business の Azure AD SSO の構成とテスト

**Britta Simon** というテスト ユーザーを使用して、Dropbox Business で Azure AD の SSO を構成し、テストします。 SSO が機能するために、Azure AD ユーザーと Dropbox Business の関連ユーザーとの間にリンク関係を確立する必要があります。

Dropbox Business に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。    
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Dropbox Business の SSO の構成](#configure-dropbox-business-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Dropbox Business のテスト ユーザーの作成](#create-dropbox-business-test-user)** - Dropbox Business で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Dropbox Business** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://www.dropbox.com/sso/<id>`
    
     b. **[識別子 (エンティティ ID)]** ボックスに、`Dropbox` という値を入力します。
    
    > [!NOTE]
    > **Dropbox サイン SSO ID** は、Dropbox サイトの [Dropbox] > [Admin console]\(管理コンソール\) > [Settings]\(設定\) > [Single sign-on]\(シングル サインオン\) > [SSO sign-in URL]\(SSO サインイン URL\) で確認できます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Dropbox Business のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)


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

このセクションでは、B.Simon に Dropbox Business へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Dropbox Business]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-dropbox-business-sso"></a>Dropbox Business の SSO の構成

1. Dropbox Business 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **マイ アプリによるセキュリティで保護されたサインイン拡張機能** をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Dropbox Business のセットアップ]** をクリックすると、Dropbox Business アプリケーションに移動します。 そこから、管理者資格情報を提供して Dropbox Business にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 8 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Dropbox Business を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、Dropbox Business テナントに移動して、Dropbox Business テナントにサインオンします。 さらに次の手順に従います。

    ![[Dropbox Business Sign in]\(Dropbox Business サインイン\) ページを示すスクリーンショット。](./media/dropboxforbusiness-tutorial/account.png "Configure single sign-on")

4. **ユーザー アイコン** をクリックし、 **[Settings]\(設定\)** タブを選択します。

    ![[ユーザー アイコン] アクションと [Settings]\(設定\) が選択されていることを示すスクリーンショット。](./media/dropboxforbusiness-tutorial/configure-1.png "Configure single sign-on")

5. 左側のナビゲーション ウィンドウで、 **[Admin console]\(管理コンソール\)** をクリックします。

    ![[Admin console]\(管理コンソール\) が選択されていることを示すスクリーンショット。](./media/dropboxforbusiness-tutorial/configure-2.png "Configure single sign-on")

6. **[Admin console]\(管理コンソール\)** の左側のナビゲーション ウィンドウで、 **[Settings]\(設定\)** をクリックします。

    ![[Settings]\(設定\) が選択されていることを示すスクリーンショット。](./media/dropboxforbusiness-tutorial/configure-3.png "Configure single sign-on")

7. **[Authentication]\(認証\)** セクションの **[Single sign-on]\(シングル サインオン\)** オプションを選択します。

    ![[Single sign-on]\(シングル サインオン\) が選択されている [Authentication]\(認証\) セクションを示すスクリーンショット。](./media/dropboxforbusiness-tutorial/configure-4.png "Configure single sign-on")

8. **[Single sign-on]\(シングル サインオン\)** セクションで、次の手順を実行します。  

    ![[Single sign-on]\(シングル サインオン\) の構成設定を示すスクリーンショット。](./media/dropboxforbusiness-tutorial/configure-5.png "Configure single sign-on")

    a. **[Single sign-on]\(シングル サインオン\)** のドロップ ダウンからオプションとして **[Required]\(必須\)** を選択します。

    b. **[Add sign-in URL]\(サインイン URL の追加\)** をクリックし、 **[Identity provider sign-in URL]\(ID プロバイダーのサインイン URL\)** ボックスに Azure portal からコピーした **ログイン URL** の値を貼り付け、 **[完了]** を選択します。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/sso.png "Configure single sign-on")

    c. **[Upload certificate]\(証明書のアップロード\)** をクリックし、Azure portal からダウンロードした **Base64 でエンコードされた証明書ファイル** を参照します。

    d. **[Copy link]\(リンクのコピー\)** をクリックし、コピーした値を Azure portal の **[Dropbox Business のドメインと URL]** セクションの **[サインオン URL]** テキストボックスに貼り付けます。

    e. **[保存]** をクリックします。

### <a name="create-dropbox-business-test-user"></a>Dropbox Business のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Dropbox Business 内に作成します。 Dropbox Business では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Dropbox Business にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Dropbox Business クライアント サポート チーム](https://www.dropbox.com/business/contact)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Dropbox Business のサインオン URL にリダイレクトされます。 

* Dropbox Business のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Dropbox Business] タイルをクリックすると、Dropbox Business のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Dropbox Business を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。