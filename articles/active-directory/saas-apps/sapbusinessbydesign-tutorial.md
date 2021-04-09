---
title: 'チュートリアル: Azure Active Directory と SAP Business ByDesign の統合 | Microsoft Docs'
description: Azure Active Directory と SAP Business ByDesign の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 6db863f43deb6eb2787cda60650a267a62076aad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101654342"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>チュートリアル: Azure Active Directory と SAP Business ByDesign の統合

このチュートリアルでは、SAP Business ByDesign と Azure Active Directory (Azure AD) を統合する方法について説明します。 SAP Business ByDesign を Azure AD と統合すると、次のことが可能になります。

* SAP Business ByDesign にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して SAP Business ByDesign に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* シングル サインオン (SSO) が有効な SAP Business ByDesign サブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* SAP Business ByDesign では、**SP** によって開始される SSO がサポートされます

## <a name="add-sap-business-bydesign-from-the-gallery"></a>ギャラリーからの SAP Business ByDesign の追加

Azure AD への SAP Business ByDesign の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SAP Business ByDesign を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**SAP Business ByDesign**」と入力します。
1. 結果パネルから **[SAP Business ByDesign]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、SAP Business ByDesign に対する Azure AD SSO を構成してテストします。 SSO を機能させるには、Azure AD ユーザーと SAP Business ByDesign の関連ユーザーとの間にリンク関係を確立する必要があります。

SAP Business ByDesign に対する Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[SAP Business ByDesign の SSO の構成](#configure-sap-business-bydesign-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[SAP Business ByDesign テスト ユーザーの作成](#create-sap-business-bydesign-test-user)** - SAP Business ByDesign で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **SAP Business ByDesign** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<servername>.sapbydesign.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<servername>.sapbydesign.com`

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[SAP Business ByDesign クライアント サポート チーム](https://www.sap.com/products/cloud-analytics.support.html)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. SAP Business ByDesign アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image1](common/edit-attribute.png)

6. **[編集]** アイコンをクリックして、 **[名前識別子の値]** を編集します。

    ![Image2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. **[ユーザー要求の管理]** セクションで、以下の手順を実行します。

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. **[ソース]** として **[変換]** を選択します。

    b. **[変換]** ドロップダウン リストで、 **[ExtractMailPrefix()]** を選択します。

    > [!NOTE]
    > 既定では、ByD はユーザー マッピングに **指定されていない** NameID の形式を使用します。 ByD は、SAML アサーションの NameID を ByD ユーザー エイリアスにマップします。 さらに、ByD では、名前 ID の形式として **emailAddress** がサポートされています。 この場合、ByD は SAM アサーションの NameID を、ByD 従業員の連絡先データの ByD ユーザー メール アドレスにマップします。 詳細については、[この SAP ブログ](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/)をご覧ください。

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[SAP Business ByDesign のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に SAP Business ByDesign へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[SAP Business ByDesign]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。

1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-sap-business-bydesign-sso"></a>SAP Business ByDesign の SSO の構成

1. SAP Business ByDesign ポータルに管理者権限でサインオンします。

2. **[Application and User Management Common Task (アプリケーションとユーザー管理の共通タスク)]** に移動し、 **[ID プロバイダー]** タブをクリックします。

3. **[New Identity Provider]** をクリックし、Azure Portal からダウンロードしたメタデータの XML ファイルを選択します。 メタデータをインポートすることによって、必要な署名証明書と暗号化証明書が自動的にアップロードされます。

    ![シングル サインオンの構成 1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. **Assertion Consumer Service URL** を SAML 要求に追加するには、 **[Include Assertion Consumer Service URL (Assertion Consumer Service URL を含める)]** を選択します。

5. **[Activate Single Sign-On (シングル サインオンを有効にする)]** をクリックします。

6. 変更を保存します。

7. **[My System (自分のシステム)]** タブをクリックします。

    ![シングル サインオンの構成 2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. **[Azure AD Sign On URL]\(Azure AD サインオン URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    ![シングル サインオンの構成 3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. ユーザー ID とパスワードでログオンするか、SSO でログオンするかを従業員が手動で選択できるかどうかを、 **[Manual Identity Provider Selection (ID プロバイダーの手動選択)]** を選択して指定します。

10. **[SSO URL]** セクションには、従業員がシステムへのサインオンに使用する URL を指定します。
    [URL Sent to Employee (従業員に送信する URL)] ボックスの一覧で、次のオプションを選択できます。

    **[Non-SSO URL (非 SSO URL)]**

    従業員に送信されるのは、システムの通常の URL のみです。 従業員のサインオンに SSO は使用できず、パスワードまたは証明書を使用する必要があります。

    **[SSO URL (SSO の URL)]**

    従業員に送信されるのは、SSO の URL のみです。 従業員は SSO を使用してサインオンすることができます。 認証要求は IdP を介してリダイレクトされます。

    **[Automatic Selection (自動選択)]**

    SSO が有効ではない場合、システムの通常の URL が従業員に送信されます。 SSO が有効である場合は、従業員がパスワードを持っているかどうかがシステムによってチェックされます。 パスワードを持っていた場合は、SSO の URL と非 SSO の URL の両方が従業員に送信されます。 一方、従業員がパスワードを持っていない場合は、SSO の URL だけが従業員に送信されます。

11. 変更を保存します。

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business ByDesign テスト ユーザーの作成

このセクションでは、SAP Business ByDesign で Britta Simon というユーザーを作成します。 SAP Business ByDesign プラットフォームにユーザーを追加する方法についてご不明な点がある場合は、[SAP Business ByDesign クライアント サポート チーム](https://www.sap.com/products/cloud-analytics.support.html)にお問い合わせください。 

> [!NOTE]
> NameID 値は必ず、SAP Business ByDesign プラットフォームのユーザー名フィールドと一致させてください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

1. Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる SAP Business ByDesign のサインオン URL にリダイレクトされます。 

2. SAP Business ByDesign のサインオン URL に直接移動し、そこからログイン フローを開始します。

3. Microsoft マイ アプリを使用することができます。 マイ アプリで [SAP Business ByDesign] タイルをクリックすると、SAP Business ByDesign のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* SAP Business ByDesign を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用できます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。