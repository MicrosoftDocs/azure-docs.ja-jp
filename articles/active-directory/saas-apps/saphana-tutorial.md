---
title: 'チュートリアル: Azure Active Directory と SAP HANA の統合 | Microsoft Docs'
description: Azure Active Directory と SAP HANA の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2020
ms.author: jeedes
ms.openlocfilehash: 1a1e155974b66dce9a036a20cdebe19ded81fed5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98727083"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>チュートリアル: Azure Active Directory と SAP HANA の統合

このチュートリアルでは、SAP HANA と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP HANA と Azure AD を統合すると、次のことができます。

* SAP HANA にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP HANA に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

SAP HANA と Azure AD の統合を構成するには、次のものが必要です。

- Azure AD サブスクリプション
- シングル サインオン (SSO) が有効な SAP HANA サブスクリプション
- 任意のパブリック IaaS、オンプレミス、Azure VM、または Azure 内の SAP Large Instances で実行している HANA インスタンス
- HANA インスタンスにインストールされた XSA 管理 Web インターフェイスと HANA Studio

> [!NOTE]
> このチュートリアルの手順をテストする場合、SAP HANA の運用環境を使わないことをお勧めします。 最初にアプリケーションの開発環境またはステージング環境で統合をテストし、そのあとに運用環境を使用してください。

このチュートリアルの手順をテストするには、次の推奨事項に従います。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SAP HANA でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SAP HANA では、**IDP** によって開始される SSO がサポートされます
* SAP HANA では、**Just-In-Time** ユーザー プロビジョニングがサポートされています

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。


## <a name="adding-sap-hana-from-the-gallery"></a>ギャラリーからの SAP HANA の追加

Azure AD への SAP HANA の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP HANA を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**SAP HANA**」と入力します。
1. 結果のパネルから **[SAP HANA]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-sap-hana"></a>SAP HANA の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP HANA に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと SAP HANA の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP HANA に対して Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[SAP HANA の SSO の構成](#configure-sap-hana-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAP HANA テスト ユーザーの作成](#create-sap-hana-test-user)** - SAP HANA で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SAP HANA** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    **[応答 URL]** ボックスに、`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc` のパターンを使用して URL を入力します

    > [!NOTE]
    > 応答 URL 値は、実際の値ではありません。 実際の応答 URL でこの値を更新します。 これらの値を取得するには、[SAP HANA クライアント サポート チーム](https://cloudplatform.sap.com/contact.html)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. SAP HANA アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![[編集] アイコンが選択されている [ユーザー属性] セクションを示すスクリーンショット。](common/edit-attribute.png)

6. **[ユーザー属性とクレーム]** ダイアログの **[ユーザー属性]** セクションで、次の手順を実行します。
 
    a. **[編集]** アイコンをクリックして、 **[ユーザー要求の管理]** ダイアログを開きます。

    ![[編集] アイコンが選択された [ユーザー属性とクレーム] ダイアログを示すスクリーンショット。](./media/saphana-tutorial/tutorial_usermail.png)

    ![image](./media/saphana-tutorial/tutorial_usermailedit.png)

    b. **[変換]** の一覧で、**ExtractMailPrefix()** を選択します。

    c. **[パラメーター 1]** の一覧で、**user.mail** を選択します。

    d. **[保存]** をクリックします。

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

このセクションでは、B.Simon に SAP HANA へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP HANA]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-hana-sso"></a>SAP HANA の SSO の構成

1. SAP HANA 側でシングル サインオンを構成するには、それぞれの HTTPS エンドポイントにアクセスして **HANA XSA Web Console** にログインします。

    > [!NOTE]
    > 既定の構成では、この URL で要求はサインイン画面にリダイレクトされ、認証済みの SAP HANA データベース ユーザーの資格情報を要求されます。 サインインするユーザーには、SAML 管理タスクを実行するアクセス許可が必要です。

2. XSA Web インターフェイスで、**SAML Identity Provider** に移動します。 ここから画面の下部にある **+** ボタンを選択し、 **[Add Identity Provider Info]\(ID プロバイダー情報の追加\)** ウィンドウを表示します。 その後、次の手順を実行します。

    ![ID プロバイダーの追加](./media/saphana-tutorial/sap1.png)

    a. **[Add Identity Provider Info]\(ID プロバイダーの情報の追加\)** ウィンドウで、Azure Portal からダウンロードしたメタデータ XML の内容を、 **[Metadata]\(メタデータ\)** ボックスに貼り付けます。

    ![[Metadata]\(メタデータ\) と [Name]\(名前\) ボックスが強調表示されている [Add Identity Provider Info]\(ID プロバイダーの情報の追加\) ペインを示すスクリーンショット。](./media/saphana-tutorial/sap2.png)

    b. XML ドキュメントの内容が有効な場合、解析プロセスで、 **[General data]\(全般的なデータ\)** 画面領域の **[Subject, Entity ID, and Issuer]\(件名、エンティティ ID、発行者\)** に必要な情報が抽出されます。 **[Base URL and SingleSignOn URL (*)]\(ベース URL と SingleSignOn URL (*)\)** のフィールドなど、 **[Destination]\(接続先\)** 画面領域に URL フィールドに必要な情報も抽出されます。

    ![ID プロバイダーの設定の追加](./media/saphana-tutorial/sap3.png)

    c. **[General Data]\(全般データ\)** 画面領域の **[Name]\(名前\)** ボックスに、新しい SAML SSO ID プロバイダーの名前を入力します。

    > [!NOTE]
    > SAML IDP の名前は必須です。また、一意にする必要があります。 使用する SAP HANA XS アプリケーションの認証方法として SAML を選択すると表示される使用可能な SAML IDP の一覧に、この名前が表示されます。 たとえば、XS Artifact Administration の **[Authentication]\(認証\)** 画面領域で確認できます。

3. **[Save]\(保存\)** を選択して SAML ID プロバイダーの詳細を保存し、既知の SAML IDP の一覧に新しい SAML IDP を追加します。

    ![[保存] ボタン](./media/saphana-tutorial/sap4.png)

4. HANA Studio の **[Configuration]\(構成\)** タブのシステム プロパティで、**saml** を使用して設定を絞り込みます。 次に、 **[assertion_timeout]** を **[10 sec]\(10 秒\)** から **[120 sec]\(120 秒\)** に調整します。

    ![assertion_timeout の設定](./media/saphana-tutorial/sap7.png)


### <a name="create-sap-hana-test-user"></a>SAP HANA のテスト ユーザーを作成する

SAP HANA への Azure AD ユーザーのサインインを有効にするには、SAP HANA でプロビジョニングする必要があります。
SAP HANA では、**Just-In-Time プロビジョニング** がサポートされています。この設定は、既定で有効になっています。

ユーザーを手動で作成する必要がある場合は、次の手順を実行します。

>[!NOTE]
>ユーザーが使う外部認証を変更することができます。 Kerberos などの外部システムを使用して認証できます。 外部 ID について詳しくは、[ドメイン管理者](https://cloudplatform.sap.com/contact.html)に問い合わせてください。

1. 管理者として [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us) を開き、SAML SSO の DB-User を有効にします。

    ![ユーザーの作成](./media/saphana-tutorial/sap5.png)

2. **[SAML]** の左側にある非表示のチェック ボックスをオンにし、[**Configure]\(構成\)** リンクを選択します。

3. **[Add]\(追加\)** を選択して SAML IDP を追加します。  適切な SAML IDP を選択してから **[OK]** を選択します。

4. **[External Identity]\(外部 ID\)** を追加するか (この例では BrittaSimon)、 **[Any]\(任意\)** を選択します。 **[OK]** をクリックします。

   > [!Note]
   > **[Any]\(任意\)** チェック ボックスがオフの場合、HANA のユーザー名は UPN でドメイン サフィックスの前のユーザーの名前と正確に一致している必要があります (つまり、BrittaSimon@contoso.com は HANA では BrittaSimon になります)。

5. テストの場合は、すべての **XS** ロールをユーザーに割り当てます。

    ![ロールの割り当て](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > ユース ケースに適切なアクセス許可のみを付与する必要があります。

6. ユーザーを保存します。

### <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。

* Azure portal で [このアプリケーションをテストします] をクリックすると、SSO を設定した SAP HANA に自動的にサインインされます

* Microsoft マイ アプリを使用することができます。 マイ アプリで [SAP HANA] タイルをクリックすると、SSO を設定した SAP HANA に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

SAP HANA を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。