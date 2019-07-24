---
title: チュートリアル:Azure Active Directory と Amazon Web Services (AWS) を統合して複数のアカウントを接続する | Microsoft Docs
description: Azure AD と Amazon Web Services (AWS) の複数のアカウントの間でシングルサインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 322615203d188581dd04aadeff2a08307b733d06
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450568"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>チュートリアル:Azure Active Directory と複数の Amazon Web Services (AWS) アカウントの統合

このチュートリアルでは、Azure Active Directory (Azure AD) と Amazon Web Services (AWS) の複数のアカウントを統合する方法を説明します。

Amazon Web Services (AWS) と Azure AD の統合には、次の利点があります。

- Amazon Web Services (AWS) にアクセスする Azure AD ユーザーを制御できます。
- ユーザーが自分の Azure AD アカウントで自動的に Amazon Web Services (AWS) にサインオンできるようにします (シングル サインオン)。
- 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」をご覧ください。

![結果の一覧の Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>1 つの AWS アプリをすべての AWS アカウントに接続するアプローチはお勧めしないことに注意してください。 代わりに、[この](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)アプローチを使用して、Azure AD で AWS アプリの複数のインスタンスに AWS アカウントの複数のインスタンスを構成することをお勧めします。

**次の理由からこのアプローチの使用をお勧めしていません。**

* アプリのすべてのロールにパッチを適用するには、Graph エクスプローラー アプローチを使用する必要があります。 マニフェスト ファイル アプローチの使用はお勧めしません。

* 単一の AWS アプリに対し最高 1200 のアプリ ロールを追加した後にアプリ上で操作すると、サイズに関連したエラーがスローされ始めたと報告するお客様をこれまで見てきました。 アプリケーション オブジェクトにはサイズのハード制限があります。

* ロールがいずれかのアカウントに追加されたときにロールを手動で更新する必要がありますが、これは残念ながら置換アプローチであって追加ではありません。 また、アカウントが増加した場合、これはアカウントとロールの n x n の関係になります。

* すべての AWS アカウントは、同じフェデレーション メタデータ XML ファイルを使用しており、証明書のロールオーバー時には、この大量の作業を行って、同時にすべての AWS アカウントで証明書を更新する必要があります

## <a name="prerequisites"></a>前提条件

Amazon Web Services (AWS) と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* アマゾン ウェブ サービス (AWS) でのシングル サインオンが有効なサブスクリプション

> [!NOTE]
> このチュートリアルの手順をテストする場合、運用環境を使用しないことをお勧めします。

このチュートリアルの手順をテストするには、次の推奨事項に従ってください。

- 必要な場合を除き、運用環境は使用しないでください。
- Azure AD の評価環境がない場合は、[1 か月の評価版を入手できます](https://azure.microsoft.com/pricing/free-trial/)。

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

5. アプリケーションが追加されたら、**[プロパティ]** ページに移動して、**オブジェクト ID** をコピーします

    ![結果の一覧の Amazon Web Services (AWS)](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、"Britta Simon" というテスト ユーザーに基づいて、Amazon Web Services (AWS) で Azure AD のシングル サインオンを構成し、テストします。

シングル サインオンを機能させるには、Azure AD ユーザーに対応する Amazon Web Services (AWS) ユーザーが Azure AD で認識される必要があります。 言い換えると、Azure AD ユーザーと Amazon Web Services (AWS) の関連ユーザーの間で、リンク関係が確立されている必要があります。

Amazon Web Services (AWS) で、Azure AD の **[ユーザー名]** の値を **[Username]** の値として割り当ててリンク関係を確立します。

Amazon Web Services (AWS) との Azure AD シングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[アマゾン ウェブ サービス (AWS) シングル サインオンの構成](#configure-amazon-web-services-aws-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure ポータルで Azure AD のシングル サインオンを有効にし、Amazon Web Services (AWS) アプリケーションへのシングル サインオンを構成します。

**Amazon Web Services (AWS) との Azure AD シングル サインオンを構成するには、次の手順を実行します。**

1. [Microsoft Azure portal](https://portal.azure.com/) の**アマゾン ウェブ サービス (AWS)** アプリケーション統合ページで、**[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、**[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. アプリは Azure と事前に統合済みであるため、**[基本的な SAML 構成]** セクションで実行が必要な手順はありません。

    ![image](common/preintegrated.png)

5. アマゾン ウェブ サービス (AWS) アプリケーションは、特定の形式で構成された SAML アサーションを受け入れます。 このアプリケーションには、次の要求を構成します。 これらの属性の値は、アプリケーション統合ページの **[ユーザー属性と要求]** セクションで管理できます。 **[SAML でシングル サインオンをセットアップします]** ページで、**[編集]** ボタンをクリックして **[ユーザー属性と要求]** ダイアログを開きます。

    ![image](common/edit-attribute.png)

6. **[ユーザー属性]** ダイアログの **[ユーザーの要求]** セクションで、上の図のように SAML トークン属性を構成し、次の手順を実行します。

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

7. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、**[ダウンロード]** をクリックして**フェデレーション メタデータ XML** をダウンロードし、コンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

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

11. 現在の AWS アカウントからサインアウトし、Azure AD によるシングル サインオンを構成する他のアカウントでログインします。

12. 手順 2 から 10 を実行して、このアカウントにセットアップする複数のロールを作成します。 3 つ以上のアカウントがある場合は、それらのロールを作成するすべてのアカウントについて同じ手順を実行してください。

13. アカウントですべてのロールが作成されると、それらのアカウントの **[Roles]** 一覧にそれらが表示されます。

    ![ロールのセットアップ](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. すべてのアカウントのすべてのロールのロール ARN と信頼済みエンティティを取得する必要があります。それらは、Azure AD アプリケーションと手動でマッピングするために必要です。

15. ロールをクリックして、**[Role ARN]** と **[Trusted Entities]** の値をコピーします。 Azure AD で作成する必要があるすべてのロールにこれらの値が必要です。

    ![ロールのセットアップ](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. すべてのアカウントのすべてのロールに対して上記の手順を実行し、**ロール ARN,信頼済みエンティティ**の形式でメモ帳に記録します。

17. 別のウィンドウで [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を開きます。

    a. テナントのグローバル管理者/共同管理者の資格情報を使用して、Graph Explorer サイトにサインインします。

    b. ロールを作成するための十分なアクセス許可が必要です。 **[アクセス許可の変更]** をクリックして、必要なアクセス許可を取得します。

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. 一覧から次のアクセス許可を選択して (まだない場合)、[アクセス許可の変更] をクリックします。 

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. ここで、再度ログインし、同意を受け入れるよう求められます。 同意を受け入れると、Graph Explorer に再度ログインされます。

    e. バージョン ドロップダウンを**ベータ**に変更します。 テナントからすべてのサービス プリンシパルを取得するには、次のクエリを使用します。

    `https://graph.microsoft.com/beta/servicePrincipals`

    複数のディレクトリを使用している場合、次のパターンを使用できます。プライマリ ドメインは `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` にあります。

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. 取得したサービス プリンシパルの一覧から、変更する必要があるものを取得します。 Ctrl キーを押しながら F キーを押して、一覧に示されたすべての ServicePrincipals からアプリケーションを検索することもできます。 Azure AD プロパティ ページからコピーした **オブジェクト ID** を使用して、次のクエリを使用し、対応するサービス プリンシパルを取得することができます。

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. サービス プリンシパル オブジェクトから appRoles プロパティを抽出します。

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. ここで、アプリケーションの新しいロールを生成する必要があります。 

    i. 以下の JSON は、appRoles オブジェクトの例です。 同様のオブジェクトを作成して、アプリケーションに必要なロールを追加します。

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > パッチ操作では、**msiam_access** の後にのみ、新しいロールを追加できます。 また、組織のニーズごとに必要な数のロールを追加することができます。 Azure AD では、SAML 応答の要求値として、これらのロールの**値**を送信します。

    j. Graph Explorer に戻り、メソッドを **GET** から **PATCH** に変更します。 上の例に示されているように appRoles プロパティを更新して、必要なロールを持つようにサービス プリンシパル オブジェクトを修正します。 **[クエリの実行]** をクリックして、パッチ操作を実行します。 成功のメッセージで、Amazon Web Services アプリケーションのロールの作成を確認します。

    ![Graph Explorer のダイアログ ボックス](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. より多くのロールでサービス プリンシパルを修正したら、対応するロールにユーザー/グループを割り当てることができます。 これは、ポータルに移動し、Amazon Web Services アプリケーションに移動することで実行できます。 上部の **[ユーザーとグループ]** タブをクリックします。

19. グループで特定のロールを割り当てることができるように、すべての AWS ロール用に新しいグループを作成することをお勧めします。 これは 1 つのグループが 1 つのロールに対応する 1 対 1 のマッピングであることに注意してください。 そのグループに属するメンバーを追加できます。

20. グループが作成されたら、グループを選択し、アプリケーションに割り当てます。

    ![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > グループを割り当てるときに、入れ子になったグループはサポートされていません。

21. グループにロールを割り当てるには、ロールを選択し、ページの下部にある **[割り当て]** ボタンをクリックします。

    ![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > 新しいロールを表示するには、Azure Portal でセッションを更新する必要があることに注意してください。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [Amazon Web Services (AWS)] タイルをクリックすると、Amazon Web Services (AWS) アプリケーション ページが表示され、ロールを選択するオプションが表示されます。

![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

SAML 応答を確認し、クレームとして渡されるロールを参照することもできます。

![シングル サインオンの構成の追加](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

アクセス パネルの詳細については、[アクセス パネルの概要](../active-directory-saas-access-panel-introduction.md)に関するページを参照してください。

## <a name="additional-resources"></a>その他のリソース

* [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
