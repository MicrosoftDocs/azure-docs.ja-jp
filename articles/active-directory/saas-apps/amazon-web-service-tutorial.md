---
title: チュートリアル:Azure Active Directory とアマゾン ウェブ サービス (AWS) の統合 | Microsoft Docs
description: Azure Active Directory と Amazon Web Services (AWS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f897653442a3e1b2d6098b3be60c85e75ca54f9a
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67551491"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>チュートリアル:Azure Active Directory とアマゾン ウェブ サービス (AWS) の統合

このチュートリアルでは、Azure Active Directory (Azure AD) とアマゾン ウェブ サービス (AWS) を統合する方法について説明します。 Azure AD とアマゾン ウェブ サービス (AWS) を統合すると、以下を実行できます。

* アマゾン ウェブ サービス (AWS) にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して自動的にアマゾン ウェブ サービス (AWS) にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

![アマゾン ウェブ サービス (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

次のように、複数のインスタンスに対し複数の識別子を構成できます。

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Azure AD はこれらの値から **#** の値を削除し、正しい値 `https://signin.aws.amazon.com/saml` を SAML トークンの Audience URL として送信します。

**次の理由により、このアプローチを使用することをお勧めします。**

a. アプリケーションごとに独自の X509 証明書が提供されます。 AWS アプリのインスタンスごとに異なる証明書の有効期限を設定でき、それらを個別の AWS アカウントに基づいて管理できます。 この場合、証明書全体のロールオーバーが容易になります。

b. Azure AD での AWS アプリによるユーザー プロビジョニングを有効にでき、続いてその AWS アカウントからすべてのロールを Microsoft のサービスがフェッチします。 アプリでの AWS ロールの追加や更新は手動で行う必要はありません。

c. Azure AD で直接アプリを管理できるアプリ所有者をアプリに対して個別に割り当てられます。

> [!Note]
> 必ずギャラリー アプリだけを使用してください

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* アマゾン ウェブ サービス (AWS) シングル サインオン (SSO) 対応のサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。 アマゾン ウェブ サービス (AWS) では、**SP initiated SSO と IDP initiated SSO** がサポートされます。

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>ギャラリーからの Amazon Web Services (AWS) の追加

Azure AD への Amazon Web Services (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Services (AWS) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**アマゾン ウェブ サービス (AWS)** 」と入力します。
1. 結果パネルから **[アマゾン ウェブ サービス (AWS)]** を選択してそのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、アマゾン ウェブ サービス (AWS) に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーとアマゾン ウェブ サービス (AWS) の関連ユーザーの間で、リンク関係を確立する必要があります。

アマゾン ウェブ サービス (AWS) との Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
2. **[アマゾン ウェブ サービス (AWS) の構成](#configure-amazon-web-services-aws)** - アプリケーション側で SSO 設定を構成します。
3. **[Azure AD テスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[アマゾン ウェブ サービス (AWS) テスト ユーザーの作成](#create-amazon-web-services-aws-test-user)** - アマゾン ウェブ サービス (AWS) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
6. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の**アマゾン ウェブ サービス (AWS)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集/ペン アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、 **[保存]** ボタンをクリックします。

5. 複数のインスタンスを構成している場合は、識別子の値を指定してください。 2 番目以降のインスタンスからは、次の形式で識別子の値を指定してください。 一意の SPN 値を指定するには、 **#** 記号を使用してください。

    `https://signin.aws.amazon.com/saml#2`

6. アマゾン ウェブ サービス (AWS) アプリケーションでは、特定の形式の SAML アサーションが使用されるため、カスタム属性のマッピングを SAML トークン属性の構成に追加する必要があります。 次のスクリーンショットには、既定の属性一覧が示されています。  **[編集]**   アイコンをクリックして、[ユーザー属性] ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. その他に、アマゾン ウェブ サービス (AWS) アプリケーションでは、いくつかの属性が SAML 応答で返されることが想定されています。 **[ユーザー属性]** ダイアログの **[ユーザー要求]** セクションで、以下の手順を実行して、以下の表のように SAML トークン属性を追加します。

    | Name  | ソース属性  | 名前空間 |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | 「900 秒 (15 分) から43200 秒 (12 時間) の値を指定してください」 |  https://aws.amazon.com/SAML/Attributes |

    a. **[新しい要求の追加]** をクリックして **[ユーザー要求の管理]** ダイアログを開きます。

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. **[名前]** ボックスに、その行に対して表示される属性名を入力します。

    c. **[名前空間]** ボックスに、その行に表示される名前空間の値を入力します。

    d. [ソース] として **[属性]** を選択します。

    e. **[ソース属性]** の一覧から、その行に表示される属性値を入力します。

    f. **[OK]** をクリックします。

    g. **[Save]** をクリックします。

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードしてコンピューターに保存します。

   ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[アマゾン ウェブ サービス (AWS) のセットアップ]** セクションで、要件に基づく適切な URL をコピーします。

   ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>アマゾン ウェブ サービス (AWS) を構成する

1. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Services (AWS) 企業サイトにサインオンします。

2. **[AWS Home]** をクリックします。

    ![シングル サインオン ホームの構成][11]

3. **[Identity and Access Management]** をクリックします。

    ![シングル サインオン ID の構成][12]

4. **[Identity Providers]** 、 **[Create Provider]** の順にクリックします。

    ![シングル サインオン プロバイダーの構成][13]

5. **[Configure Provider]** ダイアログ ページで、次の手順を実行します。

    ![シングル サインオンの構成ダイアログ][14]

    a. **[Provider Type]** として **[SAML]** を選択します。

    b. **[Provider Name]\(プロバイダー名\)** ボックスにプロバイダー名を入力します (例: *WAAD*)。

    c. Azure Portal からダウンロードした**メタデータ ファイル**をアップロードするには、 **[ファイルの選択]** をクリックします。

    d. ページの下部にある **[Next Step]** 」を参照してください。

6. **[Verify Provider Information]** ダイアログ ボックスで、 **[Create]** をクリックします。

    ![シングル サインオンの検証の構成][15]

7. **[Roles]** をクリックしてから **[Create role]** をクリックします。

    ![シングル サインオン ロールの構成][16]

8. **[Create role]** ページで、以下の手順を実行します。  

    ![シングル サインオンの信頼の構成][19]

    a. **[Select type of trusted entity]** の **[SAML 2.0 federation]** を選択します。

    b. **[Choose a SAML 2.0 Provider]\(SAML 2.0 プロバイダーの選択\)** セクションで、先ほど作成した **SAML プロバイダー**を選択します (例: *WAAD*)

    c. **[Allow programmatic and AWS Management Console access]** を選択します。
  
    d. **[次へ: Permissions]\(次へ: アクセス許可\)** をクリックします。

9. **[Attach Permissions Policies]\(アクセス許可ポリシーのアタッチ\)** ダイアログで、組織の規定に準拠した適切なポリシーを添付します。 **次へ: 確認\)** をクリックします。  

    ![シングル サインオン ポリシーの構成][33]

10. **[Review]** ダイアログで、次の手順を実行します。

    ![シングル サインオンの構成の確認][34]

    a. **[Role name]** テキストボックスに自分のロール名を入力します。

    b. **[Role description]** ボックスに説明を入力します

    c. **[Create Role]** をクリックします。

    d. 必要な数の役割ロールを作成し、それらを ID プロバイダーにマップします。

11. Azure AD ユーザー プロビジョニングの AWS アカウントからロールをフェッチするには、AWS サービス アカウントの資格情報を使用します。 そのためには、AWS コンソール ホームを開きます。

12. **[Services]**  ->  **[Security, Identity& Compliance]**  ->  **[IAM]** をクリックします。

    ![AWS アカウントからのロールのフェッチ](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. [IAM] セクションで **[Policies]** タブを選択します。

    ![AWS アカウントからのロールのフェッチ](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. AWS アカウントからロールを取得するために、Azure AD User Provisioning で **[Create policy]** をクリックして新しいポリシーを作成します。

    ![新しいポリシーの作成](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. 以下の手順を実行して、AWS アカウントからすべてのロールをフェッチする独自のポリシーを作成します。

    ![新しいポリシーの作成](./media/amazon-web-service-tutorial/policy1.png)

    a. **[ポリシーの作成]** セクションで、 **[JSON]** タブをクリックします。

    b. ポリシー ドキュメントで、次の JSON を追加します。

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. **[ポリシーの確認] ボタン**をクリックして、ポリシーを検証します。

    ![新しいポリシーの定義](./media/amazon-web-service-tutorial/policy5.png)

16. 以下の手順を実行して**新しいポリシー**を定義します。

    ![新しいポリシーの定義](./media/amazon-web-service-tutorial/policy2.png)

    a. **[Policy Name]** として **AzureAD_SSOUserRole_Policy** を指定します。

    b. ポリシーの **[Description]** には、「**This policy will allow to fetch the roles from AWS accounts**」を指定できます。

    c. **[ポリシーの作成]** ボタンをクリックします。

17. 次の手順を実行して、AWS IAM サービスで新しいユーザー アカウントを作成します。

    a. AWS IAM コンソールで **[Users]** ナビゲーションをクリックします。

    ![新しいポリシーの定義](./media/amazon-web-service-tutorial/policy3.png)

    b. **[Add user]** をクリックして新しいユーザーを作成します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/policy4.png)

    c. **[Add user]** セクションで、次の手順を実行します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser1.png)

    * ユーザー名として「**AzureADRoleManager**」を入力します。

    * [Access type] で **[Programmatic access]** オプションを選択します。 こうすると、ユーザーは API を呼び出し、AWS アカウントからロールをフェッチできます。

    * 右下隅の **[Next Permissions]** をクリックします。

18. ここで、次の手順を実行して、このユーザーの新しいポリシーを作成します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser2.png)

    a. **[Attach existing policies directly]** をクリックします。

    b. [フィルター] セクションで、新しく作成されたポリシー **AzureAD_SSOUserRole_Policy** を検索します。

    c. **ポリシー**を選択し、 **[Next: Review]\(次へ: 確認\)** をクリックします。

19. 次の手順を実行して、接続されたユーザーに対するポリシーを確認します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser3.png)

    a. ユーザー名、アクセスの種類、ユーザーにマップされているポリシーを確認します。

    b. 右下隅にある **[Create user]** をクリックしてユーザーを作成します。

20. 次の手順を実行して、ユーザーのユーザー資格情報をダウンロードします。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/adduser4.png)

    a. ユーザーの **[Access key ID]** と **[Secret access key]** をコピーします。

    b. これらの資格情報を Azure AD の [ユーザー プロビジョニング] セクションに入力して、AWS コンソールからロールをフェッチします。

    c. 下部にある **[Close]** をクリックします。

21. Azure AD 管理ポータルでアマゾン ウェブ サービス アプリの **[ユーザー プロビジョニング]** セクションに移動します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/provisioning.png)

22. **アクセス キー**と**シークレット**をそれぞれ **[クライアント シークレット]** フィールドと **[シークレット トークン]** フィールドに入力します。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/provisioning1.png)

    a. AWS ユーザーのアクセス キーを **[clientsecret]/(clientsecret/)** フィールドに入力します。

    b. AWS ユーザー シークレットを **[シークレット トークン]** フィールドに入力します。

    c. **[テスト接続]** をクリックすると、この接続を正常にテストできます。

    d. 上部にある **[保存]** をクリックして、設定を保存します。

23. スイッチをオンにしてから上部の **[保存]** をクリックして、[設定] セクションで [プロビジョニングの状態] を **[オン]** にします。

    ![ユーザーの追加](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon にアマゾン ウェブ サービス (AWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で、 **[Amazon Web Services (AWS)]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

### <a name="create-amazon-web-services-aws-test-user"></a>アマゾン ウェブ サービス (AWS) テスト ユーザーの作成

このセクションの目的は、アマゾン ウェブ サービス (AWS) で B.Simon というユーザーを作成することです。 アマゾン ウェブ サービス (AWS) では、SSO 用にユーザーをシステムに作成する必要がないため、ここで操作を実行する必要はありません。

### <a name="test-sso"></a>SSO のテスト

アクセス パネルで [アマゾン ウェブ サービス (AWS)] タイルを選択すると、SSO を設定したアマゾン ウェブ サービス (AWS) アプリケーションに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="known-issues"></a>既知の問題

 * **[準備中]** セクションの **[マッピング]** サブセクションには、"読み込み中..." というメッセージが表示され、属性マッピングは表示されません。 現在サポートされている唯一のプロビジョニング ワークフローは、ユーザー/グループ割り当て時の選択のために、AWS から Azure AD にロールをインポートすることです。 このための属性マッピングは事前に決定されており、構成はできません。
 
 * **[準備中]** セクションでは、1 つの AWS テナントに対して、一度に 1 セットの資格情報の入力だけがサポートされています。 インポートされたすべてのロールは、AWS テナントの Azure AD [servicePrincipal オブジェクト](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)の appRoles プロパティに書き込まれます。 プロビジョニングのために、ギャラリーから Azure AD に複数の AWS テナント (servicePrincipals で表される) を追加できますが、プロビジョニングのために使用される複数の AWS servicePrincipals からインポートされたすべてのロールを、シングル サインオンに使用される単一の servicePrincipal に自動的に書き込むことができないという既知の問題があります。 回避策として、[Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) を使用して、プロビジョニングが構成されている各 AWS servicePrincipal にインポートされたすべての appRole を抽出できます。 これらのロール文字列は、後で、シングル サインオンが構成されている AWS servicePrincipal に追加できます。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
