---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) とアマゾン ウェブ サービス (AWS) の統合 | Microsoft Docs
description: Azure Active Directory と Amazon Web Services (AWS) の間でシングル サインオンを構成する方法について説明します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed1f1b6973d96fdc0bce560877720adfc5e33081
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170887"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amazon-web-services-aws"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) とアマゾン ウェブ サービス (AWS) の統合

このチュートリアルでは、Azure Active Directory (Azure AD) とアマゾン ウェブ サービス (AWS) を統合する方法について説明します。 Azure AD とアマゾン ウェブ サービス (AWS) を統合すると、以下を実行できます。

* アマゾン ウェブ サービス (AWS) にアクセスできるユーザーを Azure AD で制御する。
* ユーザーが自分の Azure AD アカウントを使用して自動的にアマゾン ウェブ サービス (AWS) にサインインできるようにする。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)」を参照してください。

![Azure AD と AWS の関係の図](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

複数のインスタンスに対して複数の識別子を構成できます。 例:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Azure AD ではこれらの値から **#** の値を削除し、正しい値 `https://signin.aws.amazon.com/saml` を SAML トークンの対象 URL として送信します。

次の理由により、このアプローチをお勧めします。

- アプリケーションごとに一意の X509 証明書が提供されます。 AWS アプリのインスタンスごとに異なる証明書の有効期限を設定でき、それらを個別の AWS アカウントに基づいて管理できます。 この場合、証明書全体のロールオーバーが容易になります。

- Azure AD での AWS アプリによるユーザー プロビジョニングを有効にでき、続いてその AWS アカウントからすべてのロールが Microsoft のサービスによってフェッチされます。 アプリでの AWS ロールの追加や更新は手動で行う必要はありません。

- アプリに対してアプリ所有者を個別に割り当てることができます。 このユーザーは、Azure AD で直接アプリを管理できます。

> [!Note]
> 必ずギャラリー アプリケーションのみを使用してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* AWS シングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* アマゾン ウェブ サービス (AWS) では、**SP と IDP** によって開始される SSO がサポートされます

> [!NOTE]
> このアプリケーションの識別子は固定文字列値であるため、1 つのテナントで構成できるインスタンスは 1 つだけです。

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>ギャラリーからの Amazon Web Services (AWS) の追加

Azure AD への Amazon Web Services (AWS) の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に Amazon Web Services (AWS) を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに「**アマゾン ウェブ サービス (AWS)** 」と入力します。
1. 結果パネルから **[アマゾン ウェブ サービス (AWS)]** を選択してそのアプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。


## <a name="configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws"></a>アマゾン ウェブ サービス (AWS) の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、アマゾン ウェブ サービス (AWS) に対する Azure AD SSO を構成してテストします。 SSO が機能するために、Azure AD ユーザーとアマゾン ウェブ サービス (AWS) の関連ユーザーの間で、リンク関係を確立する必要があります。

アマゾン ウェブ サービス (AWS) との Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[アマゾン ウェブ サービス (AWS) の SSO の構成](#configure-amazon-web-services-aws-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[アマゾン ウェブ サービス (AWS) のテスト ユーザーの作成](#create-amazon-web-services-aws-test-user)** - アマゾン ウェブ サービス (AWS) で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の**アマゾン ウェブ サービス (AWS)** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[基本的な SAML 構成]** セクションでは、アプリケーションは事前に構成されており、必要な URL は既に Azure で事前に設定されています。 ユーザーは、 **[保存]** を選択して構成を保存する必要があります。

1. 複数のインスタンスを構成している場合は、識別子の値を指定します。 2 番目のインスタンス以降は、次の形式を使用します。これには、一意の SPN 値を指定するための **#** 符号が含まれます。

    `https://signin.aws.amazon.com/saml#2`

1. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[フェデレーション メタデータ XML]** を探して **[ダウンロード]** を選択し、証明書をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/metadataxml.png)

1. **[アマゾン ウェブ サービス (AWS) のセットアップ]** セクションで、要件に基づく適切な URL をコピーします。

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

## <a name="configure-amazon-web-services-aws-sso"></a>アマゾン ウェブ サービス (AWS) の SSO の構成

1. 別のブラウザー ウィンドウで、管理者として AWS 企業サイトにサインオンします。

2. **AWS ホーム**を選択します。

    ![AWS ホーム アイコンが強調表示された AWS 企業サイトのスクリーンショット][11]

3. **[Identity and Access Management]\(ID とアクセス管理\)** を選択します。

    ![IAM が強調表示された AWS サービス ページのスクリーンショット][12]

4. **[ID プロバイダー]**  >  **[プロバイダーの作成]** を選択します。

    ![[ID プロバイダー] と [プロバイダーの作成] が強調表示された IAM ページのスクリーンショット][13]

5. **[プロバイダーの設定]** ページで、次の手順を行います。

    ![[プロバイダーの設定] のスクリーンショット][14]

    a. **[Provider Type]\(プロバイダーの種類\)** で **[SAML]** を選択します。

    b. **[プロバイダ名]** にプロバイダー名を入力します (例: *WAAD*)。

    c. Azure Portal からダウンロードした**メタデータ ファイル**をアップロードするには、 **[Choose File]\(ファイルの選択\)** を選択します。

    d. **[Next Step]\(次のステップ\)** を選択します。

6. **[プロバイダー情報の検証]** ページで **[作成]** を選択します。

    ![[作成] が強調表示された [プロバイダー情報の検証] のスクリーンショット][15]

7. **[ロール]**  >  **[ロールの作成]** を選択します。

    ![[ロール] ページのスクリーンショット][16]

8. **[Create role]** ページで、以下の手順を実行します。  

    ![[ロールの作成] ページのスクリーンショット][19]

    a. **[信頼されたエンティティの種類を選択]** で、 **[SAML 2.0 フェデレーション]** を選択します。

    b. **[SAML 2.0 プロバイダーを選択]** で、先ほど作成した **SAML プロバイダー**を選択します (例: *WAAD*)。

    c. **[Allow programmatic and AWS Management Console access]** を選択します。
  
    d. **[次へ:Permissions]\(次へ: アクセス許可\)** をクリックします。

9. **[Attach アクセス権限ポリシー]** ダイアログ ボックスで、組織の規定に準拠した適切なポリシーをアタッチします。 次に、**次のステップ: 確認\)** をクリックします。  

    ![アクセス権限ポリシーをアタッチするダイアログ ボックスのスクリーンショット][33]

10. **[確認]** ダイアログ ボックスで、次の手順を行います。

    ![[確認] ダイアログ ボックスのスクリーンショット][34]

    a. **[ロール名]** に、使用するロール名を入力します。

    b. **[ロールの説明]** に説明を入力します。

    c. **[ロールの作成]** を選択します。

    d. 必要な数のロールを作成し、それらを ID プロバイダーにマップします。

11. Azure AD ユーザー プロビジョニングの際に AWS アカウントからロールをフェッチするには、AWS サービス アカウントの資格情報を使用します。 そのためには、AWS コンソール ホームを開きます。

12. **[サービス]** を選択します。 **[セキュリティ、アイデンティティ、コンプライアンス]** の **[IAM]** を選択します。

    ![[サービス] と [IAM] が強調表示された AWS コンソール ホームのスクリーンショット](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. IAM セクションで **[ポリシー]** を選択します。

    ![[ポリシー] が強調表示された IAM セクションのスクリーンショット](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Azure AD ユーザー プロビジョニングの際に AWS アカウントからロールをフェッチするために、 **[ポリシーの作成]** を選択して新しいポリシーを作成します。

    ![[ポリシーの作成] が強調表示された [ロールの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. AWS アカウントからすべてのロールをフェッチする独自のポリシーを作成します。

    ![[JSON] が強調表示された [ポリシーの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/policy1.png)

    a. **[ポリシーの作成]** セクションで、 **[JSON]** タブを選択します。

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

    c. **[Review policy] ボタン** を選択して、ポリシーを検証します。

    ![[ポリシーの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/policy5.png)

16. 新しいポリシーを定義します。

    ![[名前] と [説明] のフィールドが強調表示された [ポリシーの作成] ページのスクリーンショット](./media/amazon-web-service-tutorial/policy2.png)

    a. **[名前]** に「**AzureAD_SSOUserRole_Policy**」と入力します。

    b. ポリシーの **[説明]** に、「**This policy will allow to fetch the roles from AWS accounts**」と入力します。

    c. **[ポリシーの作成]** を選択します。

17. AWS IAM サービスの新しいユーザー アカウントを作成します。

    a. AWS IAM コンソールで、 **[ユーザー]** を選択します。

    ![[ユーザー] が強調表示された AWS IAM コンソールのスクリーンショット](./media/amazon-web-service-tutorial/policy3.png)

    b. 新しいユーザーを作成するために、 **[ユーザーを追加]** を選択します。

    ![[ユーザーを追加] ボタンのスクリーンショット](./media/amazon-web-service-tutorial/policy4.png)

    c. **[ユーザーを追加]** セクションで、次の手順を行います。

    ![[ユーザー名] と [アクセスの種類] が強調表示された [ユーザーを追加] ページのスクリーンショット](./media/amazon-web-service-tutorial/adduser1.png)

    * ユーザー名として「**AzureADRoleManager**」を入力します。

    * [アクセスの種類] で **[プログラムによるアクセス]** を選択します。 こうすると、ユーザーは API を呼び出し、AWS アカウントからロールをフェッチできます。

    * **[次のステップ: アクセス権限]** を選択します。

18. このユーザー用の新しいポリシーを作成します。

    ![[ユーザーを追加] のスクリーンショット](./media/amazon-web-service-tutorial/adduser2.png)

    a. **[既存のポリシーを直接アタッチ]** を選択します。

    b. [フィルター] セクションで、新しく作成されたポリシー **AzureAD_SSOUserRole_Policy** を検索します。

    c. ポリシーを選択し、**次のステップ: 確認\)** をクリックします。

19. ユーザーにアタッチしたポリシーを確認します。

    ![[ユーザーの作成] が強調表示された [ユーザーを追加] ページのスクリーンショット](./media/amazon-web-service-tutorial/adduser3.png)

    a. ユーザー名、アクセスの種類、ユーザーにマップされているポリシーを確認します。

    b. **[Create user]\(ユーザーの作成\)** を選択します。

20. ユーザーのユーザー資格情報をダウンロードします。

    ![[ユーザーを追加] のスクリーンショット](./media/amazon-web-service-tutorial/adduser4.png)

    a. ユーザーの **[Access key ID]** と **[Secret access key]** をコピーします。

    b. これらの資格情報を Azure AD の [ユーザー プロビジョニング] セクションに入力して、AWS コンソールからロールをフェッチします。

    c. **[閉じる]** を選択します。

21. Azure AD 管理ポータルの AWS アプリで、 **[プロビジョニング]** に移動します。

    ![[プロビジョニング] が強調表示された AWS アプリのスクリーンショット](./media/amazon-web-service-tutorial/provisioning.png)

22. アクセス キーとシークレットをそれぞれ **[クライアント シークレット]** フィールドと **[シークレット トークン]** フィールドに入力します。

    ![[管理者資格情報] ダイアログ ボックスのスクリーンショット](./media/amazon-web-service-tutorial/provisioning1.png)

    a. AWS ユーザーのアクセス キーを **[clientsecret]/(clientsecret/)** フィールドに入力します。

    b. AWS ユーザー シークレットを **[シークレット トークン]** フィールドに入力します。

    c. **[接続テスト]** を選択します。

    d. **[保存]** を選択して設定を保存します。

23. **[設定]** セクションの **[プロビジョニング状態]** で **[オン]** を選択します。 次に、 **[保存]** を選択します。

    ![[オン] が強調表示された [設定] セクションのスクリーンショット](./media/amazon-web-service-tutorial/provisioning2.png)

> [!NOTE]
> プロビジョニング サービスは、AWS から Azure AD にロールをインポートするだけです。 Azure AD のユーザーとグループが、このサービスによって再び AWS にプロビジョニングされることはありません。

### <a name="create-amazon-web-services-aws-test-user"></a>アマゾン ウェブ サービス (AWS) テスト ユーザーの作成

このセクションの目的は、アマゾン ウェブ サービス (AWS) で B.Simon というユーザーを作成することです。 アマゾン ウェブ サービス (AWS) では、SSO 用にユーザーをシステムに作成する必要がないため、ここで操作を実行する必要はありません。

## <a name="test-sso"></a>SSO のテスト

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [アマゾン ウェブ サービス (AWS)] タイルをクリックすると、SSO を設定した Amazon Web Services (AWS) アプリケーションに自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)に関する記事を参照してください。

## <a name="known-issues"></a>既知の問題

 * **[プロビジョニング]** セクションの **[マッピング]** サブセクションには、"読み込み中..." というメッセージが表示され、属性マッピングは表示されません。 現在サポートされている唯一のプロビジョニング ワークフローは、ユーザーまたはグループ割り当て時の選択のために、AWS から Azure AD にロールをインポートすることです。 このための属性マッピングは事前に決定されており、構成はできません。

 * **[準備中]** セクションでは、1 つの AWS テナントに対して、一度に 1 セットの資格情報の入力だけがサポートされています。 インポートされたすべてのロールは、AWS テナントの Azure AD [`servicePrincipal` オブジェクト](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)の `appRoles` プロパティに書き込まれます。

   プロビジョニングのために複数の AWS テナント (`servicePrincipals` によって表される) をギャラリーから Azure AD に追加できます。 ただし、プロビジョニングに使用される複数の AWS `servicePrincipals` からインポートされたすべてのロールを、SSO に使用される単一の `servicePrincipal` に自動的に書き込むことができないという既知の問題があります。

   回避策として、[Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) を使用して、プロビジョニングが構成されている各 AWS `servicePrincipal` にインポートされたすべての `appRoles` を抽出できます。 その後、これらのロール文字列を、SSO が構成されている AWS `servicePrincipal` に追加できます。

* AWS から Azure AD へのインポート対象となるロールは、次の要件を満たす必要があります。

  * ロールには、AWS で SAML プロバイダーが 1 つだけ定義されている必要があります。

  * ロールの ARN とインポートされるロールの SAML プロバイダーの ARN とを組み合わせた長さが 119 文字以下であることが必要です。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory の条件付きアクセスとは](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD でアマゾン ウェブ サービス (AWS) を試す](https://aad.portal.azure.com/)

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