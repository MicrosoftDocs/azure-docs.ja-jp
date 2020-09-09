---
title: チュートリアル:Salesforce を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory と Salesforce の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 66bba0d4825300a6cead2453a67518af631234b9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543506"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>チュートリアル:Salesforce を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、Azure AD から Salesforce にユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除するうえで Salesforce と Azure AD で実行する必要がある手順について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の項目があることを前提としています。

* Azure Active Directory テナント
* Salesforce.com テナント

> [!IMPORTANT]
> Salesforce.com の試用アカウントを使用している場合は、自動化されたユーザー プロビジョニングを構成できません。 試用アカウントの場合、アカウントを購入するまでは、必要な API にアクセスできません。 [無料の開発者アカウント](https://developer.salesforce.com/signup) を使用してこのチュートリアルを完了することで、この制限を回避できます。

Salesforce Sandbox 環境を使用している場合は、 [Salesforce Sandbox の統合に関するチュートリアル](https://go.microsoft.com/fwLink/?LinkID=521879)を参照してください。

## <a name="assigning-users-to-salesforce"></a>Salesforce へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "割り当て" という概念が使用されます。 自動ユーザー アカウント プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに "割り当て済み" のユーザーとグループのみが同期されます。

プロビジョニング サービスを構成して有効にする前に、Salesforce アプリへのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 これを決定したら、[エンタープライズ アプリへのユーザーまたはグループの割り当て](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)に関するページの手順に従って、これらのユーザーを Salesforce アプリに割り当てることができます。

### <a name="important-tips-for-assigning-users-to-salesforce"></a>ユーザーを Salesforce に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを Salesforce に割り当てて、プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* Salesforce にユーザーを割り当てるときに、有効なユーザー ロールを選択する必要があります。 "既定のアクセス" ロールはプロビジョニングでは使えません。

    > [!NOTE]
    > このアプリにより、プロビジョニング プロセスの一環として Salesforce からプロファイルがインポートされます。顧客は Azure AD でユーザーを割り当てるとき、ロールを選択します。 Salesforce からインポートされたプロファイルは、Azure AD ではロールとして表示されることに注意してください。

## <a name="enable-automated-user-provisioning"></a>自動化されたユーザー プロビジョニングを有効にする

このセクションでは、Azure AD を [Salesforce のユーザー アカウント プロビジョニング API - v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm) に接続する手順のほか、プロビジョニング サービスを構成して、Azure AD のユーザーとグループの割り当てに基づいて割り当て済みのユーザー アカウントを Salesforce で作成、更新、無効化する手順を説明します。

> [!Tip]
> Salesforce では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[Azure Portal](https://portal.azure.com) で説明されている手順に従ってください。 シングル サインオンは自動プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="configure-automatic-user-account-provisioning"></a>自動ユーザー アカウント プロビジョニングを構成する

このセクションでは、Active Directory のユーザー アカウントのユーザー プロビジョニングを Salesforce に対して有効にする方法について説明します。

1. [Azure Portal](https://portal.azure.com) で、 **[Azure Active Directory]、[エンタープライズ アプリ]、[すべてのアプリケーション]** セクションの順に移動します。

2. シングル サインオンのために Salesforce を既に構成している場合は、検索フィールドで Salesforce のインスタンスを検索します。 構成していない場合は、 **[追加]** を選択してアプリケーション ギャラリーで **Salesforce** を検索します。 検索結果から Salesforce を選択してアプリケーションの一覧に追加します。

3. Salesforce のインスタンスを選択してから、 **[プロビジョニング]** タブを選択します。

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![プロビジョニング](./media/salesforce-provisioning-tutorial/provisioning.png)

5. **[管理者資格情報]** セクションに次の構成設定を指定します。

    a. **[管理ユーザー名]** ボックスに、Salesforce.com で**システム管理者**プロファイルが割り当てられている Salesforce アカウント名を入力します。

    b. **[管理パスワード]** テキストボックスに、このアカウントのパスワードを入力します。

6. Salesforce のセキュリティ トークンを取得するには、新しいタブを開き、同じ Salesforce の管理者アカウントにサインインします。 ページの右上にある自分の名前をクリックし、 **[Settings]\(設定\)** をクリックします。

    ![自動ユーザー プロビジョニングの有効化](./media/salesforce-provisioning-tutorial/sf-my-settings.png "自動ユーザー プロビジョニングの有効化")

7. 左側のナビゲーション ウィンドウで **[私の個人情報]** をクリックして関連するセクションを展開し、 **[私のセキュリティ トークンのリセット]** をクリックします。
  
    ![自動ユーザー プロビジョニングの有効化](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "自動ユーザー プロビジョニングの有効化")

8. **[Reset Security Token]\(セキュリティ トークンのリセット\)** ページで、 **[Reset Security Token]\(セキュリティ トークンのリセット\)** ボタンをクリックします。

    ![自動ユーザー プロビジョニングの有効化](./media/salesforce-provisioning-tutorial/sf-reset-token.png "自動ユーザー プロビジョニングの有効化")

9. この管理アカウントに関連付けられている電子メールの受信トレイを確認します。 新しいセキュリティ トークンが記載された Salesforce.com からの電子メールを探します。

10. トークンをコピーして Azure AD のウィンドウに移動し、 **[シークレット トークン]** フィールドに貼り付けます。

11. **テナント URL** は、Salesforce のインスタンスが Salesforce Government クラウドにある場合にのみ入力する必要があります。 それ以外の場合は省略可能です。 テナント URL は、"https://\<your-instance\>.my.salesforce.com" 形式で入力します。\<your-instance\> は、ご利用の Salesforce のインスタンスの名前に置き換えてください。

12. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が Salesforce アプリに接続できることを確認します。

13. プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを **[通知用メール]** フィールドに入力して、下のチェック ボックスをオンにします。

14. **[保存]** をクリックします。  

15. [マッピング] セクションの **[Azure Active Directory ユーザーを Salesforce に同期する]** を選択します。

16. **[属性マッピング]** セクションで、Azure AD から Salesforce に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Salesforce のユーザー アカウントとの照合に使用されることに注意してください。 [保存] ボタンをクリックして変更をコミットします。

17. Salesforce に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

18. **[保存]** をクリックします。

> [!NOTE]
> ユーザーが Salesforce アプリケーションでプロビジョニングされたら、管理者は言語固有の設定を構成する必要があります。 言語の構成の詳細については、[こちら](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)の記事を参照してください。

これで、[ユーザーとグループ] セクションで Salesforce に割り当てたユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかることに注意してください。後続の同期は、サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ ログを取得できます。このログには、プロビジョニング サービスによって Salesforce アプリに対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="common-issues"></a>一般的な問題
* Salesforce へのアクセスの承認で問題が発生している場合は、次のことを確認してください。
    * 使用する資格情報に、Salesforce への管理者アクセス権がある。
    * 使用している Salesforce のバージョンで、Web アクセスがサポートされている (たとえば、Salesforce の Developer、Enterprise、Sandbox、および Unlimited のエディション)。
    * Web API アクセスがユーザーに対して有効になっている。
* Azure AD プロビジョニング サービスでは、ユーザーのプロビジョニング言語、ロケール、およびタイムゾーンがサポートされています。 これらの属性は既定の属性マッピングに含まれていますが、既定のソース属性を保持していません。 既定のソース属性を選択し、そのソース属性が SalesForce で想定されている形式となるようにしてください。 たとえば、英語 (米国) の localeSidKey は、en_US です。 [こちら](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)に記載されているガイダンスを確認して、適切な localeSidKey 形式を把握してください。 languageLocaleKey の形式は、[こちら](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5)に記載されています。 この形式が正しいことを確認するだけでなく、[こちら](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5)に説明されているように、ユーザーに対して言語が有効になっていることを確認することが必要な場合もあります。 
* **SalesforceLicenseLimitExceeded:** このユーザーが使用できるライセンスがないため、ターゲット アプリケーションでユーザーを作成できませんでした。 ターゲット アプリケーションの追加ライセンスを購入するか、ユーザーの割り当てと属性マッピングの構成を調べて、正しい属性で正しいユーザーが割り当てられていることを確認します。
* **SalesforceDuplicateUserName:** 別の Salesforce.com テナント内に重複している Salesforce.com の 'Username' があるため、ユーザーをプロビジョニングできません。  Salesforce.com では、'Username' 属性の値は、すべての Salesforce.com テナントにわたって一意である必要があります。  既定では、Azure Active Directory のユーザーの userPrincipalName は、Salesforce.com でのそのユーザーの 'Username' になります。   この場合、2 つの選択肢があります。  1 つ目のオプションは、他の Salesforce.com テナントも管理する場合に、その他のテナントの重複する 'Username' を持つユーザーを探して、名前を変更することです。  2 つ目のオプションは、Azure Active Directory ユーザーから、ディレクトリが統合されている Salesforce.com テナントへのアクセス権を削除することです。 次回の同期の試行時に、この操作を再試行します。 
* **SalesforceRequiredFieldMissing:** Salesforce では、ユーザーを正常に作成または更新するために、特定の属性がユーザーに存在する必要があります。 このユーザーには、必須の属性の 1 つがありません。 Salesforce にプロビジョニングするすべてのユーザーに、email や alias などの属性が設定されていることを確認してください。 [属性ベースのスコープ フィルター](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts)を使用して、これらの属性を持たないユーザーを対象外にすることができます。 
* Salesforce へのプロビジョニング用の既定の属性マッピングには、Azure AD の appRoleAssignments を Salesforce の ProfileName にマップするための SingleAppRoleAssignments 式が含まれています。 属性マッピングでは 1 つのロールのプロビジョニングのみがサポートされているため、ユーザーが Azure AD で複数のアプリ ロールの割り当てを持たないようにしてください。 
* Salesforce では、メールを変更する前に、その更新を手動で承認する必要があります。 そのため、メールの変更が承認されるまでは、プロビジョニングのログに、ユーザーのメールを更新するエントリが複数表示されることがあります。


## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](tutorial-list.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [シングル サインオンの構成](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
