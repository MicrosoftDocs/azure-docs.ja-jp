---
title: 'チュートリアル: Azure Active Directory と BambooHR の統合 | Microsoft Docs'
description: Azure Active Directory と BambooHR の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/02/2020
ms.author: jeedes
ms.openlocfilehash: 3926e98cb55d1afd80caf4af5d67910b66c09cdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180338"
---
# <a name="tutorial-azure-active-directory-integration-with-bamboohr"></a>チュートリアル: Azure Active Directory と BambooHR の統合

このチュートリアルでは、BambooHR と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と BambooHR を統合すると、次のことができます。

* BambooHR にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して BambooHR に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* BambooHR でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* BambooHR では、**SP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。


## <a name="adding-bamboohr-from-the-gallery"></a>ギャラリーからの BambooHR の追加

BambooHR の Azure AD への統合を構成するには、BambooHR をギャラリーから管理対象 SaaS アプリの一覧に追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**BambooHR**」と入力します。
1. 結果のパネルから **[BambooHR]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-bamboohr"></a>BambooHR の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、BambooHR に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと BambooHR の関連ユーザーとの間にリンク関係を確立する必要があります。

BambooHR に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[BambooHR の SSO の構成](#configure-bamboohr-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[BambooHR テスト ユーザーの作成](#create-bamboohr-test-user)** - BambooHR で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **BambooHR** アプリケーション統合ページで、 **[管理]** セクションを探して、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<company>.bamboohr.com`

    b. **[応答 URL]** ボックスに、次のパターンを使用して URL を入力します。

    | [応答 URL] |
    |-----------|
    | `https://<company>.bamboohr.com/saml/consume.php` |
    | `https://<company>.bamboohr.co.uk/saml/consume.php` |

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、実際のサインオン URL および応答 URL で更新してください。 値を取得するには、[BambooHR クライアント サポート チーム](https://www.bamboohr.com/contact.php)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

4. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

6. **[BambooHR のセットアップ]** セクションで、要件どおりの適切な URL をコピーします。

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

このセクションでは、B.Simon に BambooHR へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、**[BambooHR]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-bamboohr-sso"></a>BambooHR の SSO の構成

1. 新しいウィンドウで、BambooHR ｌ企業サイトに管理者としてサインインします。

2. ホーム ページで、次の操作を行います。
   
    ![BambooHR のシングル サインオン ページ](./media/bamboo-hr-tutorial/ic796691.png "[Single Sign-On]")   

    a. **[アプリ]** を選択します。
   
    b. **[アプリ]** ウィンドウの **[シングル サインオン]** を選択します。
   
    c. **[SAML シングル サインオン]** を選択します。

3. **[SAML シングル サインオン]** ウィンドウで、次の手順を実行します。
   
    ![SAML のシングル サインオン ページ](./media/bamboo-hr-tutorial/IC796692.png "SAML シングル サインオン")
   
    a. **[SSO Login URL]\(SSO ログイン URL\)** ボックスに、手順 6. で Azure portal からコピーした **ログイン URL** を貼り付けます。
      
    b. Azure Portal からダウンロードした Base-64 でエンコードされた証明書をメモ帳で開き、その内容をコピーして **[X.509 証明書]** ボックスに貼り付けます。
   
    c. **[保存]** を選択します。

### <a name="create-bamboohr-test-user"></a>BambooHR テスト ユーザーの作成

Azure AD ユーザーで BambooHR にサインインできるようにするには、次の手順に従い、それらのユーザーを BambooHR に手動で設定します。

1. **BambooHR** サイトに管理者としてサインインします。

2. 上部のツールバーで **[Settings]\(設定\)** を選択します。
   
    ![[設定] ボタン](./media/bamboo-hr-tutorial/IC796694.png "設定")

3. **[概要]** を選択します。

4. 左側のウィンドウで **[Security]\(セキュリティ\)**  >  **[Users]\(ユーザー\)** を選択します。

5. 設定しようとしている有効な Azure AD アカウントのユーザー名、パスワード、メール アドレスを入力します。

6. **[保存]** を選択します。
        
>[!NOTE]
>Azure AD ユーザー アカウントの設定は、BambooHR のユーザー アカウント作成ツールや API を使って行うこともできます。

### <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始する BambooHR のサインオン URL にリダイレクトされます。 

2. BambooHR のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft アクセス パネルを使用することができます。 アクセス パネルで [BambooHR] タイルをクリックすると、BambooHR サインオン URL にリダイレクトされます。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。


## <a name="next-steps"></a>次のステップ

BambooHR を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。