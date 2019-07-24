---
title: チュートリアル:Azure Active Directory とアマゾン ウェブ サービス (AWS) の統合 | Microsoft Docs
description: Azure Active Directory と Amazon Web Services (AWS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f58c8c3ea76cf44730a54897e6b77ef6e648a49
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57901663"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>チュートリアル:Azure Active Directory とアマゾン ウェブ サービス (AWS) の統合

このチュートリアルでは、Amazon Web Services (AWS) と Azure Active Directory (Azure AD) を統合する方法について説明します。
Amazon Web Services (AWS) と Azure AD の統合には、次の利点があります。

* Amazon Web Services (AWS) にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントでアマゾン ウェブ サービス (AWS) に自動的にサインイン (シングル サインオン) できるようにすることができます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

![アマゾン ウェブ サービス (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

次のように、複数のインスタンスに対し複数の識別子を構成できます。

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Azure AD はこれらの値から **#** の値を削除し、正しい値 `https://signin.aws.amazon.com/saml` を SAML トークンの Audience URL として送信します。

**次の理由により、このアプローチを使用することをお勧めします。**

a. それぞれのアプリケーションは一意の X509 証明書を提供するので、各インスタンスの証明書の有効期限は異なることがあり、これを個々の AWS アカウントごとに管理できます。 この場合、全体的な証明書のロールオーバーは簡単です。

b. Azure AD での AWS アプリによるユーザー プロビジョニングを有効にでき、続いてその AWS アカウントからすべてのロールを Microsoft のサービスがフェッチします。 アプリでの AWS ロールの追加や更新は手動で行う必要はありません。

c. Azure AD で直接アプリを管理できるアプリ所有者をアプリに対して個別に割り当てられます。

> [!Note]
> 必ずギャラリー アプリだけを使用してください

## <a name="prerequisites"></a>前提条件

Amazon Web Services (AWS) と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* アマゾン ウェブ サービス (AWS) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

> [!Note]
> シングル サインオンのために複数の AWS アカウントを 1 つの Azure アカウントに統合したい場合は、[この](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial)記事を参照してください。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* アマゾン ウェブ サービス (AWS) では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>ギャラリーからの Amazon Web Services (AWS) の追加

Azure AD への Amazon Web Services (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Services (AWS) を追加する必要があります。

**ギャラリーから Amazon Web Services (AWS) を追加するには、次の手順を実行します:** 

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、**[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、**[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**Amazon Web Services (AWS)**」と入力して結果パネルから **[Amazon Web Services (AWS)]** を選択し、**[追加]** をクリックしてアプリケーションを追加します。

     ![結果の一覧の Amazon Web Services (AWS)](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、アマゾン ウェブ サービス (AWS) で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーとアマゾン ウェブ サービス (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Amazon Web Services (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[アマゾン ウェブ サービス (AWS) シングル サインオンの構成](#configure-amazon-web-services-aws-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[アマゾン ウェブ サービス (AWS) テスト ユーザーの作成](#create-amazon-web-services-aws-test-user)** - アマゾン ウェブ サービス (AWS) で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

アマゾン ウェブ サービス (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Microsoft Azure portal](https://portal.azure.com/) の**アマゾン ウェブ サービス (AWS)** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 構成を保存するには、**[保存]** ボタンをクリックします。

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. 複数のインスタンスを構成している場合は、識別子の値を指定してください。 2 番目以降のインスタンスからは、次の形式で識別子の値を指定してください。 一意の SPN 値を指定するには、**#** 記号を使用してください。

    `https://signin.aws.amazon.com/saml#2`

    ![[Amazon Web Services (AWS) のドメインと URL] のシングル サインオン情報](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. アマゾン ウェブ サービス (AWS) アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

7. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

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

8. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして、要件のとおりに指定したオプションから**フェデレーション メタデータ XML** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

9. **[アマゾン ウェブ サービス (AWS) のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>アマゾン ウェブ サービス (AWS) のシングル サインオンの構成

1. 別の Web ブラウザーのウィンドウで、管理者として Amazon Web Services (AWS) 企業サイトにサインオンします。

2. **[AWS Home]** をクリックします。

    ![シングル サインオン ホームの構成][11]

3. **[Identity and Access Management]** をクリックします。

    ![シングル サインオン ID の構成][12]

4. **[Identity Providers]**、**[Create Provider]** の順にクリックします。

    ![シングル サインオン プロバイダーの構成][13]

5. **[Configure Provider]** ダイアログ ページで、次の手順を実行します。

    ![シングル サインオンの構成ダイアログ][14]

    a. **[Provider Type]** として **[SAML]** を選択します。

    b. **[Provider Name]\(プロバイダー名\)** ボックスにプロバイダー名を入力します (例: *WAAD*)。

    c. Azure Portal からダウンロードした**メタデータ ファイル**をアップロードするには、**[ファイルの選択]** をクリックします。

    d. ページの下部にある **[Next Step]**」を参照してください。

6. **[Verify Provider Information]** ダイアログ ボックスで、**[Create]** をクリックします。

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

12. **[Services]** -> **[Security, Identity& Compliance]** -> **[IAM]** をクリックします。

    ![AWS アカウントからのロールのフェッチ](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. [IAM] セクションで **[Policies]** タブを選択します。

    ![AWS アカウントからのロールのフェッチ](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. AWS アカウントからロールを取得するために、Azure AD User Provisioning で **[Create policy]** をクリックして新しいポリシーを作成します。

    ![新しいポリシーの作成](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. 以下の手順を実行して、AWS アカウントからすべてのロールをフェッチする独自のポリシーを作成します。

    ![新しいポリシーの作成](./media/amazon-web-service-tutorial/policy1.png)

    a. **[ポリシーの作成]** セクションで、**[JSON]** タブをクリックします。

    b. ポリシー ドキュメントで、次の JSON を追加します。

    ```

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

    c. **ポリシー**を選択し、**[Next: Review]\(次へ: 確認\)** をクリックします。

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

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、**[Azure Active Directory]**、**[ユーザー]**、**[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[User name]\(ユーザー名\)** フィールドに「**brittasimon\@yourcompanydomain.extension**」と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon にアマゾン ウェブ サービス (AWS) へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、**[すべてのアプリケーション]**、**[アマゾン ウェブ サービス (AWS)]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、「**アマゾン ウェブ サービス (AWS)**」と入力し、選択します。

    ![アプリケーションの一覧の [Amazon Web Services (AWS)] リンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、**[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、**[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、**[割り当て]** ボタンをクリックします。

### <a name="create-amazon-web-services-aws-test-user"></a>アマゾン ウェブ サービス (AWS) テスト ユーザーの作成

このセクションの目的は、Amazon Web Services (AWS) で Britta Simon というユーザーを作成することです。 アマゾン ウェブ サービス (AWS) では、SSO 用にユーザーをシステムに作成する必要がないため、ここで操作を実行する必要はありません。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [アマゾン ウェブ サービス (AWS)] タイルをクリックすると、SSO を設定した Amazon Web Services (AWS) アプリケーションに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

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
