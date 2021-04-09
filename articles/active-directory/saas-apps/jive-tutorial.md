---
title: チュートリアル:Azure Active Directory と Jive の統合 | Microsoft Docs
description: Azure Active Directory と Jive の間でシングル サインオンを構成する方法について説明します。
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
ms.openlocfilehash: bcfc2996daeb34f357625572d39661638982ae42
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Jive の統合

このチュートリアルでは、Jive と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Jive を統合すると、次のことができます。

* Jive にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Jive に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Jive でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Jive では、**SP** Initiated SSO がサポートされます。
* Jive では、[**自動化された** ユーザー プロビジョニング](jive-provisioning-tutorial.md)がサポートされます。

## <a name="add-jive-from-the-gallery"></a>ギャラリーからの Jive の追加

Azure AD への Jive の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Jive を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**Jive**」と入力します。
1. 結果のパネルから **[Jive]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Jive の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、Jive に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと Jive の関連ユーザーとの間にリンク関係を確立する必要があります。

Jive に対して Azure AD SSO を構成してテストするには、次の手順を実行します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Jive の SSO の構成](#configure-jive-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Jive のテスト ユーザーの作成](#create-jive-test-user)** - Jive で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **Jive** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML によるシングル サインオンのセットアップ]** ページで、 **[基本的な SAML 構成]** の鉛筆アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

   a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<instance name>.jivecustom.com`

   b. **[識別子 (エンティティ ID)]** テキスト ボックスに、次のパターンで URL を入力します。
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > これらは実際の値ではありません。 実際のサインオン URL と識別子でこれらの値を更新します。 これらの値を取得するには、[Jive クライアント サポート チーム](https://www.jivesoftware.com/services-support/)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして、要件のとおりに指定したオプションから **フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

6. **[Jive のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

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

このセクションでは、B.Simon に Jive へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Jive]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-jive-sso"></a>Jive の SSO の構成

1. **Jive** 側でシングル サインオンを構成するには、管理者として Jive テナントにサインオンします。

1. 上部のメニューで、 **[SAML]** をクリックします。

    ![[Enabled]\(有効\) が選択されている [SAML] タブを示すスクリーンショット。](./media/jive-tutorial/jive-2.png)

    a. **[General]\(全般\)** タブで **[Enabled]\(有効\)** を選択します。

    b. **[SAVE ALL SAML SETTINGS]\(すべての SAML 設定を保存\)** ボタンをクリックします。

1. **[IDP METADATA]\(IDP メタデータ\)** タブに移動します。

    ![[I D P METADATA]\(I D P メタデータ\) が選択されている [SAML] タブを示すスクリーンショット。](./media/jive-tutorial/jive-3.png)

    a. ダウンロードしたメタデータ XML ファイルの内容をコピーし、 **[Identity Provider (IDP) Metadata (ID プロバイダー (IDP) のメタデータ)]** ボックスに貼り付けます。

    b. **[SAVE ALL SAML SETTINGS]\(すべての SAML 設定を保存\)** ボタンをクリックします。

1. **[USER ATTRIBUTE MAPPING]\(ユーザー属性のマッピング\)** タブを選択します。

    ![[USER ATTRIBUTE MAPPING]\(ユーザー属性のマッピング\) が選択されている [SAML] タブを示すスクリーンショット。](./media/jive-tutorial/jive-4.png)

    a. **[Email (電子メール)]** ボックスに、**mail** 値の属性名をコピーして貼り付けます。

    b. **[First Name (名)]** ボックスに、**givenname** 値の属性名をコピーして貼り付けます。

    c. **[Last Name (姓)]** ボックスに、**surname** 値の属性名をコピーして貼り付けます。

### <a name="create-jive-test-user"></a>Jive のテスト ユーザーの作成

このセクションの目的は、Jive で Britta Simon というユーザーを作成することです。 Jive では、自動ユーザー プロビジョニングがサポートされています。この設定は、既定で有効になっています。 自動ユーザー プロビジョニングの構成方法について詳しくは、[こちら](jive-provisioning-tutorial.md)をご覧ください。

ユーザーを手動で作成する必要がある場合、[Jive クライアント サポート チーム](https://www.jivesoftware.com/services-support/)と連携し、Jive プラットフォームにユーザーを追加してください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる Jive のサインオン URL にリダイレクトされます。 

* Jive のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [Jive] タイルをクリックすると、Jive のサインオン URL にリダイレクトされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Jive を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。