---
title: 'チュートリアル: Azure Active Directory と Adobe Experience Manager の統合 | Microsoft Docs'
description: Azure Active Directory と Adobe Experience Manager の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/17/2021
ms.author: jeedes
ms.openlocfilehash: 1a0340b9d8971446113862465c67143674bd5668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653356"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>チュートリアル: Azure Active Directory と Adobe Experience Manager の統合

このチュートリアルでは、Adobe Experience Manager と Azure Active Directory (Azure AD) を統合する方法について説明します。 Adobe Experience Manager を Azure AD と統合すると、次のことが可能になります。

* Adobe Experience Manager にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Adobe Experience Manager に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な Adobe Experience Manager サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Adobe Experience Manager では、**SP と IDP** によって開始される SSO がサポートされます

* Adobe Experience Manager では、**Just-In-Time** ユーザー プロビジョニングがサポートされます

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>ギャラリーから Adobe Experience Manager を追加する

Azure AD と Adobe Experience Manager の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Adobe Experience Manager を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Adobe Experience Manager**」と入力します。
1. 結果パネルから **[Adobe Experience Manager]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-adobe-experience-manager"></a>Adobe Experience Manager の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Adobe Experience Manager に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと Adobe Experience Manager の関連ユーザーとの間にリンク関係を確立する必要があります。

Adobe Experience Manager に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[Adobe Experience Manager の SSO の構成](#configure-adobe-experience-manager-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Adobe Experience Manager のテスト ユーザーの作成](#create-adobe-experience-manager-test-user)** - Adobe Experience Manager で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクします。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Adobe Experience Manager** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、AEM のサーバーにも定義する一意の値を入力します。

    b. **[応答 URL]** ボックスに、`https://<AEM Server Url>/saml_login` のパターンを使用して URL を入力します

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 応答 URL 値を実際の応答 URL で更新します。 この値を取得するには、[Adobe Experience Manager クライアント サポート チーム](https://helpx.adobe.com/support/experience-manager.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、Adobe Experience Manager サーバーの URL を入力します。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[Adobe Experience Manager のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Adobe Experience Manager へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Adobe Experience Manager]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-adobe-experience-manager-sso"></a>Adobe Experience Manager の SSO の構成

1. 別のブラウザー ウィンドウで **Adobe Experience Manager** の管理ポータルを開きます。

2. **[設定]**  >  **[セキュリティ]**  >  **[ユーザー]** を選択します。

    ![Adobe Experience Manager の [ユーザー] タイルを示すスクリーンショット。](./media/adobe-experience-manager-tutorial/user-1.png)

3. **[管理者]** またはその他の関連するユーザーを選択します。

    ![管理者ユーザーが強調表示されているスクリーンショット。](./media/adobe-experience-manager-tutorial/tutorial-admin-6.png)

4. **[アカウント設定]**  >  **[Manage TrustStore]\(トラストストアの管理\)** を選択します。

    ![[アカウント設定] の下の [Manage TrustStore]\(トラストストアの管理\) を示すスクリーンショット。](./media/adobe-experience-manager-tutorial/manage-trust.png)

5. **[Add Certificate from CER file]\(CER ファイルから証明書を追加\)** で、 **[証明書ファイルの選択]** をクリックします。 Azure Portal から既にダウンロードしている証明書ファイルを参照し選択します。

    ![[Select Certificate File]\(証明書ファイルの選択\) ボタンが強調表示されているスクリーンショット。](./media/adobe-experience-manager-tutorial/user-2.png)

6. 証明書がトラストストアに追加されます。 証明書の別名に注意してください。

    ![証明書がトラストストアに追加されていることを示すスクリーンショット。](./media/adobe-experience-manager-tutorial/tutorial-admin-7.png)

7. **[ユーザー]** ページで、 **[認証サービス]** を選択します。

    ![画面で認証サービスが強調表示されているスクリーンショット。](./media/adobe-experience-manager-tutorial/tutorial-admin-8.png)

8. **[アカウント設定]**  >  **[Create/Manage TrustStore]\(キーストアの作成および管理\)** を選択します。 パスワードを入力して、キーストアを作成します。

    ![[Manage KeyStore]\(キーストアの管理\) が強調表示されているスクリーンショット。](./media/adobe-experience-manager-tutorial/tutorial-admin-9.png)

9. 管理画面に戻ります。 **[設定]**  >  **[Operations]\(操作\)**  >  **[Web Console]\(Web コンソール\)** を選択します。

    ![[設定] セクションにある [Operations]\(操作\) の下の [Web Console]\(Web コンソール\) が強調表示されているスクリーンショット。](./media/adobe-experience-manager-tutorial/tutorial-admin-1.png)

    [構成] ページが開きます。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobe-experience-manager-tutorial/tutorial-admin-2.png)

10. 「**Adobe Granite SAML 2.0 Authentication Handler**」で検索します。 次に、 **[追加]** アイコンを選択します。

    ![[Adobe Granite SAML 2.0 Authentication Handler]\(Adobe Granite SAML 2.0 認証ハンドラー\) が強調表示されているスクリーンショット。](./media/adobe-experience-manager-tutorial/tutorial-admin-3.png)

11. このページで、次の操作を実行します。

    ![[シングル サインオンの構成] の [保存] ボタン](./media/adobe-experience-manager-tutorial/tutorial-admin-4.png)

    a. **[パス]** ボックスに、「 **/** 」と入力します。

    b. **[IDP URL]** ボックスに、Azure portal からコピーした **ログイン URL** の値を入力します。

    c. **[IDP Certificate Alias]\(IDP 証明書の別名\)** ボックスに、トラストストアに追加した **証明書の別名** の値を入力します。

    d. **[Security Provided Entity ID]\(セキュリティ指定されたエンティティ ID\)** ボックスに、Azure portal で構成した **[Azure AD 識別子]** の一意の値を入力します。

    e. **[Assertion Consumer Service URL]** ボックスに、Azure Portal で構成した **[応答 URL]** の値を入力します。

    f. **[キーストアのパスワード]** ボックスに、キーストアに設定した **[パスワード]** を入力します。

    g. **[User Attribute ID]\(ユーザー属性 ID\)** ボックスに、 **[名前 ID]** または関連するその他のユーザー ID を入力します。

    h. **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** を選択します。

    i. **[Logout URL]\(ログアウト URL\)** ボックスに、Azure portal から取得した一意の **ログアウト URL** の値を入力します。

    j. **[保存]** を選択します。

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager のテスト ユーザーの作成

このセクションでは、Adobe Experience Manager で Britta Simon というユーザーを作成します。 **[Autocreate CRX Users]\(CRX ユーザーの自動作成\)** オプションを選択した場合、認証成功後にユーザーが自動的に作成されます。

ユーザーを手動で作成する場合は、[Adobe Experience Manager サポート チーム](https://helpx.adobe.com/support/experience-manager.html)に連絡して、Adobe Experience Manager プラットフォームにユーザーを追加してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Adobe Experience Manager のサインオン URL にリダイレクトされます。  

* Adobe Experience Manager のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した Adobe Experience Manager に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [Adobe Experience Manager] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した Adobe Experience Manager に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

Adobe Experience Manager を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。