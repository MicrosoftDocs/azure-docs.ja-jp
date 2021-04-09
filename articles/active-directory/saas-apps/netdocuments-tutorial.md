---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と NetDocuments の統合 | Microsoft Docs
description: Azure Active Directory と NetDocuments の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 48ba2810c0aaf304042580cdf6579df54fd9ccd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101645676"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と NetDocuments の統合

このチュートリアルでは、NetDocuments と Azure Active Directory (Azure AD) を統合する方法について説明します。 NetDocuments と Azure AD を統合すると、次のことができます。

* NetDocuments にアクセスできるユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して NetDocuments に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* NetDocuments でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* NetDocuments では、**SP** によって開始される SSO がサポートされます

## <a name="adding-netdocuments-from-the-gallery"></a>ギャラリーからの NetDocuments の追加

Azure AD への NetDocuments の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に NetDocuments を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、Azure portal にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**NetDocuments**」と入力します。
1. 結果のパネルから **[NetDocuments]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-sso-for-netdocuments"></a>NetDocuments の Azure AD SSO の構成とテスト

**B.Simon** というテスト ユーザーを使用して、NetDocuments に対する Azure AD SSO を構成してテストします。 SSO を機能させるために、Azure AD ユーザーと NetDocuments の関連ユーザーとの間にリンク関係を確立する必要があります。

NetDocuments での Azure AD SSO を構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[NetDocuments の SSO の構成](#configure-netdocuments-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[NetDocuments のテスト ユーザーの作成](#create-netdocuments-test-user)** - NetDocuments で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. Azure portal の **NetDocuments** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションで、次のフィールドの値を入力します。

    a. **[サインオン URL]** ボックスに、次のいずれかの URL パターンを入力します。

    |サインオン URL|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    b. **[識別子 (エンティティ ID)]** ボックスに、いずれかの URL を入力します。

    |識別子|
    |-----------|
    |`http://netdocuments.com/VAULT`|
    |`http://netdocuments.com/EU`|
    |`http://netdocuments.com/AU`|
    |`http://netdocuments.com/DE`|
    |

    c. **[応答 URL]** ボックスに、次のいずれかの URL パターンを入力します。

    |[応答 URL]|
    |-----------|
    |`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://eu.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://de.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |`https://au.netdocuments.com/neWeb2/docCent.aspx?whr=<Repository ID>`|
    |

    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を、実際のサインオン URL および応答 URL で更新してください。 リポジトリ ID は、**CA-** で始まり、その後に NetDocuments リポジトリに関連付けられている 8 文字のコードが続く値です。 詳細については、[NetDocuments Federated Identity サポート ドキュメント](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login)を参照してください。 また、上の情報を使用して構成することが難しい場合は、[NetDocuments クライアント サポート チーム](https://support.netdocuments.com/hc/)に連絡して、これらの値を取得してください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

1. NetDocuments アプリケーションでは、特定の形式の SAML アサーションを使用するため、カスタム属性マッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットは、既定の属性の一覧を示しています。ここで、**nameidentifier** は **user.userprincipalname** にマップされています。 NetDocuments アプリケーションでは、**nameidentifier** が **ObjectID** にマップされるか、または実際の組織の **nameidentifier** に該当する他の要求にマップされると想定されているため、 **[編集]** アイコンをクリックして属性マッピングを編集し、属性マッピングを変更する必要があります。

    ![image](common/edit-attribute.png)

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[アプリのフェデレーション メタデータ URL]** を検索し、URL をコピーします。

    ![証明書のダウンロードのリンク](common/copy-metadataurl.png)

1. **[NetDocuments のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

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

このセクションでは、B.Simon に NetDocuments へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[NetDocuments]\(NetDocuments\)** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。
1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. ユーザーにロールが割り当てられることが想定される場合は、 **[ロールの選択]** ドロップダウンからそれを選択できます。 このアプリに対してロールが設定されていない場合は、[既定のアクセス] ロールが選択されていることを確認します。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-netdocuments-sso"></a>NetDocuments の SSO の構成

1. 別の Web ブラウザーのウィンドウで、管理者として NetDocuments 企業サイトにサインインします。

2. 右上隅から自分の名前を選択し、 **[Admin]\(管理者\)** を選択します。

3. **[セキュリティ センター]** を選択します。
   
    ![リポジトリ](./media/netdocuments-tutorial/security-center.png "Security Center")

4. **[Advanced Authentication]\(高度な認証\)** を選択します。
    
    ![[Configure advanced authentication options]\(認証オプションの詳細な構成\)](./media/netdocuments-tutorial/advance-authentication.png "[認証オプションの詳細な構成]")

5.  **[フェデレーション ID]** タブで次の手順に従います。   
   
    [ ![フェデレーション ID](./media/netdocuments-tutorial/federated-id.png "フェデレーション ID")](./media/netdocuments-tutorial/federated-id.png#lightbox)
   
    a. **[フェデレーション ID のサーバーの種類]** で、 **[Windows Azure Active Directory]** を選択します。
    
    b.  **[ファイルの選択]** を選択して、Azure portal からダウンロードしたメタデータ ファイルをアップロードします。
    
    c.  **[SAVE]\(保存\)** を選択します。

### <a name="create-netdocuments-test-user"></a>NetDocuments のテスト ユーザーの作成

Azure AD ユーザーが NetDocuments にサインインできるようにするには、そのユーザーを NetDocuments にプロビジョニングする必要があります。 NetDocuments の場合、プロビジョニングは手動で行います。

**ユーザー アカウントをプロビジョニングするには、次の手順に従います。**

1. **NetDocuments** 企業サイトに管理者としてサインオンします。

2. 右上隅から自分の名前を選択し、 **[Admin]\(管理者\)** を選択します。
   
    ![管理者](./media/netdocuments-tutorial/user-admin.png "[Admin]")

3. **[ユーザーとグループ]** を選択します。
   
    ![ユーザーとグループ](./media/netdocuments-tutorial/users-groups.png "リポジトリ")

4. **[電子メール アドレス]** テキストボックスに、プロビジョニングする有効な Azure Active Directory アカウントの電子メール アドレスを入力して、 **[ユーザーの追加]** をクリックします。
   
    ![メール アドレス](./media/netdocuments-tutorial/user-mail.png "電子メール アドレス")
   
    > [!NOTE]
    > Azure Active Directory のアカウント所有者には、アカウントがアクティブになる前に、アカウント確認用のリンクを含む電子メールが送信されます。 他の NetDocuments ユーザー アカウントの作成ツールまたは NetDocuments から提供されている API を使用して、Azure Active Directory ユーザー アカウントをプロビジョニングできます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、次のオプションを使用して Azure AD のシングル サインオン構成をテストします。 

* Azure portal で **[このアプリケーションをテストします]** をクリックします。 これにより、ログイン フローを開始できる NetDocuments のサインオン URL にリダイレクトされます。 

* NetDocuments のサインオン URL に直接移動し、そこからログイン フローを開始します。

* Microsoft マイ アプリを使用することができます。 マイ アプリで [NetDocuments] タイルをクリックすると、SSO を設定した NetDocuments に自動的にサインインします。 マイ アプリの詳細については、[マイ アプリの概要](../user-help/my-apps-portal-end-user-access.md)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

NetDocuments を構成したら、ご自分の組織の機密データの流出と侵入をリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。