---
title: 'チュートリアル: Azure Active Directory と MobileIron の統合 | Microsoft Docs'
description: Azure Active Directory と MobileIron の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2021
ms.author: jeedes
ms.openlocfilehash: c47092b1488a79805db69308bcb9a8efde1c0d58
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653051"
---
# <a name="tutorial-azure-active-directory-integration-with-mobileiron"></a>チュートリアル: Azure Active Directory と MobileIron の統合

 このチュートリアルでは、MobileIron と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と MobileIron を統合すると、次のことができます。

* MobileIron にアクセスする Azure AD ユーザーを制御する。
* ユーザーが自分の Azure AD アカウントを使用して MobileIron に自動的にサインインできるように設定する。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* MobileIron でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* MobileIron では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます。

## <a name="add-mobileiron-from-the-gallery"></a>ギャラリーからの MobileIron の追加

Azure AD への MobileIron の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に MobileIron を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左側のペインで、 **[Azure Active Directory]** を選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**MobileIron**」と入力します。
1. 結果から **[MobileIron]** を選択し、そのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-mobileiron"></a>MobileIron の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、MobileIron に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと MobileIron の関連ユーザーとの間に、リンクされた関係を確立する必要があります。

MobileIron に対する Azure AD SSO を構成してテストするには、次の手順を行います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
     1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
2. **[MobileIron の SSO の構成](#configure-mobileiron-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[MobileIron のテスト ユーザーの作成](#create-mobileiron-test-user)** - MobileIron で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

このセクションでは、Azure portal で Azure AD の SSO を有効にします。
 
1. Azure portal の **MobileIron** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンを選択して設定を編集します。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、**IDP** 開始モードでアプリケーションを構成する場合は、次の手順に従います。

    a. **[識別子]** ボックスに、`https://www.MobileIron.com/<key>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<host>.MobileIron.com/saml/SSO/alias/<key>` のパターンを使用して URL を入力します

    c. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

     **[サインオン URL]** ボックスに、`https://<host>.MobileIron.com/user/login.html` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 このチュートリアルで後ほど説明する MobileIron の管理ポータルからキーとホストの値を取得します。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** を選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[Show password]\(パスワードの表示\)** チェック ボックスをオンにし、パスワードを書き留めます。
   1. **［作成］** を選択します

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に MobileIron へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で、 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。
1. アプリケーションの一覧で **[MobileIron]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択します。 次に、 **[割り当ての追加]** ダイアログ ボックスで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログ ボックスで、ユーザーの一覧から **[B.Simon]** を選択します。 次に、画面の下部にある **[選択]** を選択します。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログ ボックスで **[割り当て]** を選びます。

## <a name="configure-mobileiron-sso"></a>MobileIron の SSO の構成

1. 別の Web ブラウザー ウィンドウで、MobileIron 企業サイトに管理者としてログインします。

2. **[Admin]\(管理\)** > **[Identity]\(ID\)** に移動し、**[Info on Cloud IDP Setup]\(Cloud IDP セットアップの情報\)** フィールドの **[AAD]** オプションを選択します。

    ![スクリーンショットは、[Identity]\(ID\) が選択された MobileIron サイトの [Admin]\(管理\) タブを示しています。](./media/MobileIron-tutorial/tutorial_MobileIron_admin.png)

3. **キー** と **ホスト** の値をコピーし、Azure portal の **[基本的な SAML 構成]** セクションに貼り付けて URL を完成させます。

    ![スクリーンショットは、キーとホストの値を含む、[Setting Up SAML]\(SAML の設定\) オプションを示しています。](./media/MobileIron-tutorial/key.png)

4. **[Export metadata file from AAD and import to MobileIron Cloud Field]\(AAD からメタデータ ファイルをエクスポートして MobileIron Cloud Field にインポートする\)** で、 **[ファイルの選択]** をクリックして、Azure Portal からダウンロードしたメタデータをアップロードします。 アップロードしたら、 **[完了]** をクリックします。

    ![[シングル サインオン管理者メタデータの構成] ボタン](./media/MobileIron-tutorial/tutorial_MobileIron_adminmetadata.png)


### <a name="create-mobileiron-test-user"></a>MobileIron のテスト ユーザーの作成

Azure AD ユーザーが MobileIron にログインできるようにするには、ユーザーを MobileIron にプロビジョニングする必要があります。  
MobileIron の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. MobileIron 企業サイトに管理者としてログインします。

1. **[ユーザー]** に移動して、 **[追加]**  >  **[単一ユーザー]** とクリックします。

    ![[シングル サインオン ユーザーの構成] ボタン](./media/MobileIron-tutorial/tutorial_MobileIron_user.png)

1. **[単一ユーザー]** ダイアログ ページで、次の手順を実行します。

    ![[シングル サインオン ユーザー追加の構成] ボタン](./media/MobileIron-tutorial/tutorial_MobileIron_useradd.png)

    a. **[電子メール アドレス]** ボックスに、ユーザーの電子メール アドレスを入力します (この例では brittasimon@contoso.com)。

    b. **[名]** ボックスに、ユーザーの名を入力します (例: Britta)。

    c. **[姓]** ボックスに、ユーザーの姓を入力します (例: Simon)。

    d. **[Done]** をクリックします。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる MobileIron のサインオン URL にリダイレクトされます。  

* MobileIron のサインオン URL に直接移動し、そこからログイン フローを開始します。

### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した MobileIron に自動的にサインインされます。

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [MobileIron] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した MobileIron に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

MobileIron を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。
