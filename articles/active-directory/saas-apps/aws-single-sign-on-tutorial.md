---
title: 'チュートリアル: Azure Active Directory シングル サインオン (SSO) と AWS Single Sign-on の統合 | Microsoft Docs'
description: Azure Active Directory と AWS Single Sign-on の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: jeedes
ms.openlocfilehash: 906c7e00cba1e0feb85289e8f2a46e74924dc0c3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658725"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>チュートリアル: Azure Active Directory シングル サインオン (SSO) と AWS Single Sign-on の統合

このチュートリアルでは、AWS Single Sign-on と Azure Active Directory (Azure AD) を統合する方法について説明します。 AWS Single Sign-on を Azure AD と統合すると、次のことができます。

* AWS Single Sign-on にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントで AWS Single Sign-on に自動的にサインインするように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AWS Single Sign-on でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* AWS Single Sign-on では、**SP Initiated SSO と IDP Initiated SSO** がサポートされます

* AWS Single Sign-on では、[**自動化されたユーザー プロビジョニング**](https://docs.microsoft.com/azure/active-directory/saas-apps/aws-single-sign-on-provisioning-tutorial)がサポートされます。

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>ギャラリーからの AWS Single Sign-on の追加

Azure AD への AWS Single Sign-on の統合を構成するには、ギャラリーからマネージド SaaS アプリの一覧に AWS Single Sign-on を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**AWS Single Sign-on**」と入力します。
1. 結果のパネルから **AWS Single Sign-on** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>AWS Single Sign-on の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、AWS Single Sign-on に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと AWS Single Sign-on の関連ユーザーとの間にリンク関係を確立する必要があります。

AWS Single Sign-on で Azure AD SSO を構成してテストするには、次の手順に従います。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[AWS Single Sign-on の SSO の構成](#configure-aws-single-sign-on-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[AWS Single Sign-on のテスト ユーザーの作成](#create-aws-single-sign-on-test-user)** - AWS Single Sign-on で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **AWS Single Sign-on** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **サービス プロバイダーのメタデータ ファイル** がある場合は、 **[基本的な SAML 構成]** セクションで次の手順に従います。

    a. **[メタデータ ファイルをアップロードします]** をクリックします。

    ![image1](common/upload-metadata.png)

    b. **フォルダー ロゴ** をクリックしてメタデータ ファイルを選択し、 **[アップロード]** をクリックします。

    ![Image2](common/browse-upload-metadata.png)

    c. メタデータ ファイルが正常にアップロードされると、**識別子** と **応答 URL** の値が、[基本的な SAML 構成] セクションに自動的に設定されます。

    ![image3](common/idp-intiated.png)

    > [!Note]
    > **識別子** と **返信 URL** の値が自動的に設定されない場合は、要件に応じて手動で値を入力してください。

1. **サービス プロバイダーのメタデータ ファイル** がない場合、 **[基本的な SAML 構成]** セクションで次の手順を実行します。アプリケーションを **IDP** Initiated モードで構成する場合は、次のフィールドの値を入力します。

    a. **[識別子]** ボックスに、`https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>` のパターンを使用して URL を入力します

1. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    **[サインオン URL]** ボックスに、`https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[AWS Single Sign-on クライアント サポート チーム](mailto:aws-sso-partners@amazon.com)に連絡してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. AWS Single Sign-on アプリケーションは、特定の形式の SAML アサーションを使用するため、カスタム属性のマッピングを SAML トークンの属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > AWS SSO で ABAC が有効になっている場合、追加の属性をセッション タグとして AWS アカウントに直接渡すことができます。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[証明書 (Base64)]** を見つけて、 **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[AWS Single Sign-on のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に AWS Single Sign-on へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[AWS Single Sign-on]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-aws-single-sign-on-sso"></a>AWS Single Sign-on の SSO の構成

1. **AWS SSO コンソール** を開きます。 
2. 左側のナビゲーション ペインで、 **[Settings]\(設定\)** を選択します。
3. **[Settings]\(設定\)** ページで、 **[Identity source]\( ID ソース\)** を見つけ、 **[Change]\(変更\)** を選択します。
4. [Change directory]\(ディレクトリの変更\) ページで、 **[External identity provider]\(外部 ID プロバイダー\)** を選択します。
5. **[Service provider metadata]\(サービス プロバイダーのメタ データ\)** セクションで、 **[AWS SSO SAML metadata]\(AWS SSO SAML メタデータ\)** を見つけ、 **[Download metadata file]\(メタデータ ファイルのダウンロード\)** を選択して、メタデータ ファイルをダウンロードし、コンピューターに保存します。
6. **[Identity provider metadata]\(ID プロバイダーのメタデータ\)** セクションで、 **[Browse]\(参照\)** をクリックして、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。
7. **[Next: Review]\(次へ: 確認\)** を選択します。
8. テキスト ボックスに「**CONFIRM**」と入力し、ディレクトリの変更を確認します。
9. **[完了]** を選択します。

### <a name="create-aws-single-sign-on-test-user"></a>AWS Single Sign-on のテスト ユーザーの作成

1. **AWS SSO コンソール** を開きます。

2. 左側のナビゲーション ペインで、 **[Users]\(ユーザー\)** を選択します。

3. [Users]\(ユーザー\) ページで、 **[Add user]\(ユーザーの追加\)** を選択します。

4. [Add user]\(ユーザーの追加\) ページで、これらの手順に従います。

    a. **[Username]\(ユーザー名\)** フィールドに「B.Simon」と入力します。

    b. **[Email address]\(メール アドレス\)** フィールドに、「`username@companydomain.extension`」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。

    c. **[Confirm email address]\(メール アドレスの確認\)** フィールドに、前の手順で指定したメール アドレスを再入力します。

    d. [First name]\(名\) フィールドに、「`Jane`」と入力します。

    e. [Last name]\(姓\) フィールドに、「`Doe`」と入力します。

    f. [Display name]\(表示名\) フィールドに、「`Jane Doe`」と入力します。

    g. **[Next: Groups]\(次へ: グループ\)** を選択します。

    > [!NOTE]
    > AWS SSO に入力したユーザー名が、ユーザーの Azure AD サインイン名と一致していることを確認します。 これにより、認証の問題を回避することができます。

5. **[ユーザーの追加]** を選択します。
6. 次に、ユーザーを AWS アカウントに割り当てます。 これを行うには、AWS SSO コンソールの左側のナビゲーション ペインで、 **[AWS accounts]\(AWS アカウント\)** を選択します。
7. [AWS accounts]\(AWS アカウント\) ページで、[AWS organization]\(AWS 組織\) タブを選択し、ユーザーに割り当てる AWS アカウントの横にあるチェック ボックスをオンにします。 次に、 **[Assign users]\(ユーザーの割り当て\)** を選択します。
8. [Assign users]\(ユーザーの割り当て\) ページで、ユーザー B. Simon の横にあるチェック ボックス見つけてをオンにします。 次に、 **[Next: Permission sets]\(次へ: アクセス許可セット\)** を選択します。
9. アクセス許可セットの選択セクションで、ユーザー B.Simon に割り当てるアクセス許可セットの横にあるチェック ボックスをオンにします。 既存のアクセス許可セットがない場合は、 **[Create new permission set]\(新しいアクセス許可セットの作成\)** を選択します。

    > [!NOTE]
    > アクセス許可セットによって、ユーザーとグループが AWS アカウントに対して持つアクセス レベルが定義されます。 アクセス許可セットの詳細については、AWS SSO の **アクセス許可セット** に関するページを参照してください。
10. **[完了]** を選択します。

> [!NOTE]
> AWS Single Sign-on はまた、自動ユーザー プロビジョニングもサポートしています。自動ユーザー プロビジョニングの構成方法の詳細については、[こちら](https://docs.microsoft.com/azure/active-directory/saas-apps/aws-single-sign-on-provisioning-tutorial)をご覧ください。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

#### <a name="sp-initiated"></a>SP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる AWS Single Sign-on のサインオン URL にリダイレクトされます。  

* AWS Single Sign-on のサインオン URL に直接移動し、そこからログイン フローを開始します。

#### <a name="idp-initiated"></a>IDP Initiated:

* Azure portal で **[このアプリケーションをテストします]** をクリックすると、SSO を設定した AWS Single Sign-on に自動的にサインインされます 

また、Microsoft マイ アプリを使用して、任意のモードでアプリケーションをテストすることもできます。 マイ アプリで [AWS Single Sign-on] タイルをクリックすると、SP モードで構成されている場合は、ログイン フローを開始するためのアプリケーション サインオン ページにリダイレクトされます。IDP モードで構成されている場合は、SSO を設定した AWS Single Sign-on に自動的にサインインされます。 マイ アプリの詳細については、[マイ アプリの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

AWS Single Sign-on を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)をご覧ください。


