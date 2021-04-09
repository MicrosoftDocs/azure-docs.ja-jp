---
title: チュートリアル:myPolicies を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを myPolicies に自動的にプロビジョニングまたはプロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 221f63ab9a7eb3f71a4c730a11565dda64c9edc9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353587"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>チュートリアル:myPolicies を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、myPolicies に対するユーザーまたはグループのプロビジョニングまたはプロビジョニング解除を自動的に実行するように Azure Active Directory (Azure AD) を構成するために、myPolicies と Azure AD で実行される手順を示すことです。

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。
>
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* Azure AD テナント。
* [myPolicies テナント](https://mypolicies.com/index.html#section10)。
* Admin アクセス許可がある myPolicies のユーザー アカウント。

## <a name="assigning-users-to-mypolicies"></a>myPolicies へのユーザーの割り当て

Azure Active Directory では、選択されたアプリへのアクセスが付与されるユーザーを決定する際に "*割り当て*" という概念が使用されます。 自動ユーザー プロビジョニングのコンテキストでは、Azure AD 内のアプリケーションに割り当て済みのユーザーとグループのみが同期されます。

自動ユーザー プロビジョニングを構成して有効にする前に、myPolicies へのアクセスが必要な Azure AD 内のユーザーやグループを決定しておく必要があります。 決定したら、次の手順に従って、これらのユーザーやグループを myPolicies に割り当てることができます。
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>ユーザーを myPolicies に割り当てる際の重要なヒント

* 単一の Azure AD ユーザーを myPolicies に割り当てて、自動ユーザー プロビジョニングの構成をテストすることをお勧めします。 後でユーザーやグループを追加で割り当てられます。

* myPolicies にユーザーを割り当てるときは、アプリケーション固有の有効なロール (使用可能な場合) を割り当てダイアログで選択する必要があります。 **既定のアクセス** ロールのユーザーは、プロビジョニングから除外されます。

## <a name="setup-mypolicies-for-provisioning"></a>プロビジョニングのために myPolicies を設定する

Azure AD での自動ユーザー プロビジョニング用に myPolicies を構成する前に、myPolicies 上で SCIM プロビジョニングを有効にする必要があります。

1. myPolicies の担当者 ( **support@mypolicies.com** ) に連絡して、SCIM のプロビジョニングを構成するために必要なシークレット トークンを入手します。

2.  myPolicies 担当者から提供されたトークン値を保存します。 この値を、Azure portal の myPolicies アプリケーションの [プロビジョニング] タブにある **[シークレット トークン]** フィールドに入力します。

## <a name="add-mypolicies-from-the-gallery"></a>ギャラリーから myPolicies を追加する

Azure AD で自動ユーザー プロビジョニング用に myPolicies を構成するには、Azure AD アプリケーション ギャラリーから myPolicies をマネージド SaaS アプリケーションの一覧に追加する必要があります。

**Azure AD アプリケーション ギャラリーから myPolicies を追加するには、以下の手順を行います。**

1. **[Azure portal](https://portal.azure.com)** の左側のナビゲーション パネルで、 **[Azure Active Directory]** を選択します。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ウィンドウの上部にある **[新しいアプリケーション]** ボタンを選びます。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに「**myPolicies**」と入力し、結果パネルで **[myPolicies]** を選択してから、 **[追加]** ボタンをクリックしてアプリケーションを追加します。

    ![結果一覧の myPolicies](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>myPolicies への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて myPolicies でのユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!TIP]
> myPolicies では SAML ベースのシングル サインオンを有効にすることもできます。これを行うには、[myPolicies シングル サインオンのチュートリアル](mypolicies-tutorial.md)で説明されている手順に従ってください。 シングル サインオンは自動ユーザー プロビジョニングとは別に構成できますが、これらの 2 つの機能は相補的な関係にあります。

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Azure AD で myPolicies に対する自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[myPolicies]** を選択します。

    ![アプリケーションの一覧の myPolicies のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナントの URL]** に「`https://<myPoliciesCustomDomain>.mypolicies.com/scim`」と入力します。`<myPoliciesCustomDomain>` は自分の myPolicies のカスタム ドメインです。 myPolicies のカスタム ドメインは URL から取得できます
(例: `<demo0-qa>`.mypolicies.com)。

6. **[シークレット トークン]** に、先ほど取得したトークン値を入力します。 **[Test Connection]\(テスト接続\)** をクリックして、Azure AD から myPolicies に接続できることを確認します。 接続できない場合は、自分の myPolicies アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL + トークン](common/provisioning-testconnection-tenanturltoken.png)

7. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

8. **[保存]** をクリックします。

9. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to myPolicies]\(Azure Active Directory ユーザーを myPolicies に同期する\)** を選択します。

    :::image type="content" source="media/mypolicies-provisioning-tutorial/usermapping.png" alt-text="[マッピング] セクションのスクリーンショット。[名前] の下に、[Synchronize Azure Active Directory Users to customappsso]\(Azure Active Directory ユーザーを customappsso に同期する\) が表示されています。" border="false":::

10. **[属性マッピング]** セクションで、Azure AD から myPolicies に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で myPolicies のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|
   |addresses[type eq "work"].country|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|


11. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

12. myPolicies に対して Azure AD プロビジョニング サービスを有効にするには、**[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

13. **[設定]** セクションの **[スコープ]** で目的の値を選択して、myPolicies にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

14. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 **[同期の詳細]** セクションを使用すると、進行状況を監視できるほか、リンクをクリックしてプロビジョニング アクティビティ レポートを取得できます。このレポートには、Azure AD プロビジョニング サービスによって myPolicies に対して実行されたすべてのアクションが記載されています。

Azure AD プロビジョニング ログの読み取りの詳細については、「[自動ユーザー アカウント プロビジョニングについてのレポート](../app-provisioning/check-status-user-account-provisioning.md)」をご覧ください。

## <a name="connector-limitations"></a>コネクタの制限事項

* myPolicies では常に **userName**、**email**、**externalId** が必須です。
* myPolicies では、ユーザー属性の物理的な削除はサポートされていません。

## <a name="change-log"></a>ログの変更

* 2020 年 9 月 15 日 - Users に "country" 属性のサポートを追加しました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
