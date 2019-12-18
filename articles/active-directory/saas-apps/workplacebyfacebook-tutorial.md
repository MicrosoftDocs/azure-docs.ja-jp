---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workplace by Facebook の統合 | Microsoft Docs
description: Azure Active Directory と Workplace by Facebook の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc55130bd840de3960a44ddc1bd0617af185148
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969686"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Workplace by Facebook の統合

このチュートリアルでは、Workplace by Facebook と Azure Active Directory (Azure AD) を統合する方法について説明します。 Workplace by Facebook を Azure AD と統合すると、次のことができます。

* Workplace by Facebook にアクセスできるユーザーを Azure AD で制御します。
* ユーザーが自分の Azure AD アカウントを使用して自動的に Workplace by Facebook にサインインできるようにします。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Workplace by Facebook でのシングル サインオン (SSO) が有効なサブスクリプション。

> [!NOTE]
> Facebook には、Workplace Standard (無料) と Workplace Premium (有料) の 2 つの製品があります。 すべての Workplace Premium テナントは、他のコストやライセンスを必要とせずに、SCIM と SSO の統合を構成できます。 SSO と SCIM は、Workplace Standard インスタンスでは利用できません。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Workplace by Facebook では、**SP** によって開始される SSO がサポートされます
* Workplace by Facebook では、 **[自動化されたユーザー プロビジョニングとプロビジョニング解除 (推奨)](workplacebyfacebook-provisioning-tutorial.md)** がサポートされます
* Workplace by Facebook では、**Just-In-Time プロビジョニング**がサポートされます
* Workplace by Facebook Mobile アプリケーションを Azure AD と共に構成して SSO を有効にできるようになりました。 このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>ギャラリーからの Workplace by Facebook の追加

Azure AD への Workplace by Facebook の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Workplace by Facebook を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Workplace by Facebook**」と入力します。
1. 結果ウィンドウで **[Workplace by Facebook]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Workplace by Facebook の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Workplace by Facebook に対する Azure AD SSO を構成してテストします。 SSO を機能させるためには、Azure AD ユーザーと Workplace by Facebook の関連ユーザーとの間にリンク関係を確立する必要があります。

Workplace by Facebook で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    * **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    * **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Workplace by Facebook の SSO の構成](#configure-workplace-by-facebook-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    * **[Workplace by Facebook のテスト ユーザーの作成](#create-workplace-by-facebook-test-user)** - Workplace by Facebook で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
3. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Workplace by Facebook** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<instancename>.facebook.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、`https://www.facebook.com/company/<instanceID>` という形式で URL を入力します。

    > [!NOTE] 
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新してください。 Workplace コミュニティの適切な値については、Workplace Company Dashboard の認証ページを参照してください。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Workplace by Facebook のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、Workplace by Facebook へのアクセスを許可することで、B.Simon が Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Workplace by Facebook]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-workplace-by-facebook-sso"></a>Workplace by Facebook の SSO の構成

1. Workplace by Facebook 内での構成を自動化するには、 **[拡張機能のインストール]** をクリックして **My Apps Secure Sign-in ブラウザー拡張機能**をインストールする必要があります。

    ![マイ アプリの拡張機能](common/install-myappssecure-extension.png)

1. ブラウザーに拡張機能を追加した後、 **[Workplace by Facebook のセットアップ]** をクリックすると、Workplace by Facebook アプリケーションに誘導されます。 そこから、管理者の資格情報を入力して Workplace by Facebook にサインインします。 ブラウザー拡張機能によりアプリケーションが自動的に構成され、手順 3 から 5 が自動化されます。

    ![セットアップの構成](common/setup-sso.png)

1. Workplace by Facebook を手動でセットアップする場合は、新しい Web ブラウザー ウィンドウを開き、管理者として Workplace by Facebook 企業サイトにサインインして、次の手順を実行します。

    > [!NOTE]
    > SAML 認証プロセスの一環として、Azure AD にパラメーターを渡すために Workplace が最大サイズ 2.5 KBのクエリ文字列を使用する可能性があります。

1. 左側のナビゲーション パネルで **[Security]\(セキュリティ\)**  >  **[Authentication]\(認証\)** タブに移動します。

    ![管理パネル](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. **[Single-sign on (SSO)]\(シングル サインオン (SSO)\)** オプションをオンにします。
    
    b. **[+Add new SSO Provider]\(+ 新しい SSO プロバイダーの追加\)** をクリックします。

1. **[認証]** タブで **[シングル サインオン (SSO)]** を選択し、次の手順を実行します。

    ![[認証] タブ](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. **[Name of the SSO Provider]\(SSO プロバイダーの名前\)** に、SSO インスタンスの名前を入力します (例: Azureadsso)。

    b. **[SAML URL]** ボックスに、Azure portal からコピーした**ログイン URL** の値を貼り付けます。

    c. **[SAML Issuer URL]\(SAML 発行者の URL\)** ボックスに、Azure portal からコピーした **Azure AD 識別子**の値を貼り付けます。

    d. Azure Portal からダウンロードした **Base-64 でエンコードされた証明書**をメモ帳で開き、その内容をクリップボードにコピーしてから、それを **[SAML Certificate]\(SAML 証明書\)** ボックスに貼り付けます。

    e. インスタンスの **[Audience URL]\(対象 URL\)** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[識別子 (エンティティ ID)]** ボックスに貼り付けます。

    f. インスタンスの **[Recipient URL]\(受信者 URL\)** をコピーして、Azure portal の **[基本的な SAML 構成]** セクションの **[サインオン URL]** ボックスに貼り付けます。

    g. セクションの下部にスクロールし、 **[Test SSO]\(SSO のテスト\)** をクリックします。 これにより、Azure AD ログイン ページで表示されるポップアップ ウィンドウが表示されます。 通常どおり資格情報を入力して認証を行います。

    **トラブルシューティング:** Azure AD から返された電子メール アドレスが、ログインに使用した Workplace アカウントと同じであることを確認してください。

    h. テストが正常に完了したら、ページの下部にスクロールして **[保存]** をクリックします。

    i. Workplace を使用しているすべてのユーザーに、認証用の Azure AD ログイン ページが表示されるようになりました。

1. **SAML ログアウト リダイレクト (オプション)**  -

    必要に応じて、Azure AD のログアウト ページの指定に使用される SAML ログアウト URL を構成できます。 この設定が有効に構成されている場合は、ユーザーに対して Workplace ログアウト ページが表示されなくなります。 代わりに、SAML ログアウト リダイレクトの設定に追加された URL にユーザーがリダイレクトされるようになります。

### <a name="configuring-reauthentication-frequency"></a>再認証の頻度の構成

SAML チェックの要求を毎日、3 日ごと、1 週間ごと、2 週間ごと、1 か月ごとに行う、または行わないように、Workplace を構成できます。

> [!NOTE]
> モバイル アプリケーションで設定できる SAML チェック頻度の最小値は 1 週間です。

次のボタンを使用して、すべてのユーザーに対して SAML のリセットを強制することもできます。[Require SAML authentication for all users now]\(今すぐすべてのユーザーの SAML 認証を要求する\)。

### <a name="create-workplace-by-facebook-test-user"></a>Workplace by Facebook のテスト ユーザーの作成

このセクションでは、B.Simon というユーザーを Workplace by Facebook に作成します。 Workplace by Facebook では、Just-In-Time プロビジョニングがサポートされています。この設定は既定で有効になっています。

このセクションでは、ユーザー側で必要な操作はありません。 Workplace by Facebook にユーザーが 1 人もいない場合は、Workplace by Facebook にアクセスしようとしたときに新しいユーザーが作成されます。

>[!Note]
>ユーザーを手動で作成する必要がある場合は、[Workplace by Facebook クライアント サポート チーム](https://workplace.fb.com/faq/)にお問い合わせください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Workplace by Facebook] タイルをクリックすると、SSO を設定した Workplace by Facebook に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>Workplace by Facebook (モバイル) の SSO をテストする

1. Workplace by Facebook Mobile アプリケーションを開きます。 サインイン ページで、 **[ログイン]** をクリックします。

    ![サインイン](./media/workplacebyfacebook-tutorial/test05.png)

2. 法人メールを入力し、 **[CONTINUE]\(続行\)** をクリックします。

    ![メール](./media/workplacebyfacebook-tutorial/test02.png)

3. **[JUST ONCE]\(1 回のみ\)** をクリックします。

    ![1 回のみ](./media/workplacebyfacebook-tutorial/test04.png)

4. **[Allow]\(許可する\)** をクリックします。

    ![許可する](./media/workplacebyfacebook-tutorial/test03.png)

5. サインインに成功すると、アプリケーションのホーム ページが表示されます。    

    ![ホーム ページ](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [[ユーザー プロビジョニングの構成]](workplacebyfacebook-provisioning-tutorial.md)

- [Azure AD で Workplace by Facebook を試す](https://aad.portal.azure.com)
