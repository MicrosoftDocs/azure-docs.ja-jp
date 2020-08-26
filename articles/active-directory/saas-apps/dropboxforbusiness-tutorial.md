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
ms.date: 06/23/2020
ms.author: jeedes
ms.openlocfilehash: 3c1ff9de1057a5ef551ad941965f64907e4243e5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555741"
---
# <a name="tutorial-integrate-dropbox-business-with-azure-active-directory"></a>チュートリアル:Dropbox Business と Azure Active Directory の統合

このチュートリアルでは、Dropbox Business と Azure Active Directory (Azure AD) を統合する方法について説明します。 Dropbox Business を Azure AD に統合すると、次のことを実行できます。

* Dropbox Business にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Dropbox Business に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションをお持ちでない場合は、[ここ](https://azure.microsoft.com/pricing/free-trial/)から 1 か月間の無料試用版を入手できます。
* Dropbox Business でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> この統合は、Azure AD 米国政府クラウド環境から利用することもできます。 このアプリケーションは、Azure AD 米国政府クラウドのアプリケーション ギャラリーにあります。パブリック クラウドの場合と同じように構成してください。

## <a name="scenario-description"></a>シナリオの説明

* このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 Dropbox Business では、**SP** Initiated SSO がサポートされます

* Dropbox Business では、[自動化されたユーザー プロビジョニングとプロビジョニング解除](dropboxforbusiness-tutorial.md)がサポートされます
* Dropbox を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を適用する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)をご覧ください。

## <a name="adding-dropbox-business-from-the-gallery"></a>ギャラリーからの Dropbox Business の追加

Azure AD への Dropbox Business の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に Dropbox Business を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Dropbox Business**」と入力します。
1. 結果のパネルから **[Dropbox Business]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**Britta Simon** というテスト ユーザーを使用して、Dropbox Business で Azure AD の SSO を構成し、テストします。 SSO が機能するために、Azure AD ユーザーと Dropbox Business の関連ユーザーとの間にリンク関係を確立する必要があります。

Dropbox Business で Azure AD の SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。    
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Dropbox Business の SSO の構成](#configure-dropbox-business-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Dropbox Business のテスト ユーザーの作成](#create-dropbox-business-test-user)** - Dropbox Business で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Dropbox Business** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** ページで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://www.dropbox.com/sso/<id>`

    b. **[識別子 (エンティティ ID)]** ボックスに、`Dropbox` という値を入力します。

    > [!NOTE]
    > 上記のサインオン URL の値は、実際の値ではありません。 実際のサインオン URL に値を置き換えます。実際の値については後で説明します。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの**証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Dropbox Business のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL


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

このセクションでは、Britta Simon に Dropbox Business へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Dropbox Business]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **Britta Simon** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-dropbox-business-sso"></a>Dropbox Business の SSO の構成

1. Dropbox Business 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして**マイ アプリによるセキュリティで保護されたサインイン拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

2. ブラウザーに拡張機能を追加した後、 **[Dropbox Business のセットアップ]** をクリックすると、Dropbox Business アプリケーションに移動します。 そこから、管理者資格情報を提供して Dropbox Business にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 ～ 8 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

3. Dropbox Business を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、Dropbox Business テナントに移動して、Dropbox Business テナントにサインオンします。 さらに次の手順に従います。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/ic769509.png "Configure single sign-on")

4. **ユーザー アイコン**をクリックし、 **[Settings]\(設定\)** タブを選択します。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure1.png "Configure single sign-on")

5. 左側のナビゲーション ウィンドウで、 **[Admin console]\(管理コンソール\)** をクリックします。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure2.png "Configure single sign-on")

6. **[Admin console]\(管理コンソール\)** の左側のナビゲーション ウィンドウで、 **[Settings]\(設定\)** をクリックします。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure3.png "Configure single sign-on")

7. **[Authentication]\(認証\)** セクションの **[Single sign-on]\(シングル サインオン\)** オプションを選択します。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure4.png "Configure single sign-on")

8. **[Single sign-on]\(シングル サインオン\)** セクションで、次の手順を実行します。  

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure5.png "Configure single sign-on")

    a. **[Single sign-on]\(シングル サインオン\)** のドロップ ダウンからオプションとして **[Required]\(必須\)** を選択します。

    b. **[Add sign-in URL]\(サインイン URL の追加\)** をクリックし、 **[Identity provider sign-in URL]\(ID プロバイダーのサインイン URL\)** ボックスに Azure portal からコピーした**ログイン URL** の値を貼り付け、 **[完了]** を選択します。

    ![シングル サインオンの構成](./media/dropboxforbusiness-tutorial/configure6.png "Configure single sign-on")

    c. **[Upload certificate]\(証明書のアップロード\)** をクリックし、Azure portal からダウンロードした **Base64 でエンコードされた証明書ファイル**を参照します。

    d. **[Copy link]\(リンクのコピー\)** をクリックし、コピーした値を Azure portal の **[Dropbox Business のドメインと URL]** セクションの **[サインオン URL]** テキストボックスに貼り付けます。

    e. **[保存]** をクリックします。

### <a name="create-dropbox-business-test-user"></a>Dropbox Business のテスト ユーザーの作成

このセクションでは、B. Simon というユーザーを Dropbox Business 内に作成します。 Dropbox Business では、Just-In-Time ユーザー プロビジョニングがサポートされています。これは既定で有効になっています。 このセクションでは、ユーザー側で必要な操作はありません。 Dropbox Business にユーザーがまだ存在していない場合は、認証後に新規に作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Dropbox Business クライアント サポート チーム](https://www.dropbox.com/business/contact)にお問い合わせください。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [Dropbox Business] タイルを選択すると、SSO を設定した Dropbox Business に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Azure AD で Dropbox Business を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)