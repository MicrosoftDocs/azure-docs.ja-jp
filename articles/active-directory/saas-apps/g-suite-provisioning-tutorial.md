---
title: チュートリアル:G Suite を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から G Suite に対してユーザー アカウントを自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: f6ef2f91d178db01e618c19df55705bc9c1209e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583753"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>チュートリアル:G Suite を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために G Suite と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[G Suite](https://gsuite.google.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 

> [!NOTE]
> このチュートリアルでは、Azure AD ユーザー プロビジョニング サービスの上にビルドされるコネクタについて説明します。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * G Suite でユーザーを作成する
> * アクセスが不要になった場合に G Suite のユーザーを削除する
> * Azure AD と G Suite の間でユーザー属性の同期を維持する
> * G Suite でグループとグループ メンバーシップをプロビジョニングする
> * G Suite への[シングル サインオン](./google-apps-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* [G Suite テナント](https://gsuite.google.com/pricing.html)
* 管理者アクセス許可を持つ G Suite 上のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と G Suite の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように G Suite を構成する

Azure AD での自動ユーザー プロビジョニング用に G Suite を構成する前に、G Suite 上で SCIM プロビジョニングを有効にする必要があります。

1. [G Suite の管理コンソール](https://admin.google.com/)に管理者アカウントでサインインし、 **[セキュリティ]** を選択します。 このリンクが表示されていない場合、画面下部の **[その他の設定]** メニューに隠れていることがあります。

    ![G Suite のセキュリティ](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. **[セキュリティ]** ページで、 **[API リファレンス]** を選択します。

    ![G Suite の API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. **[API アクセスを有効にする]** を選択します。

    ![G Suite の API の有効化](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > G Suite にプロビジョニングしようとしているすべてのユーザーについて、その Azure AD でのユーザー名がカスタム ドメインに関連付けられている **必要があります**。 たとえば G Suite では、bob@contoso.onmicrosoft.com のようなユーザー名は使用できませんが、 bob@contoso.com のようなユーザー名は使用できます。 既存のユーザーのドメインは、[ここ](../fundamentals/add-custom-domain.md)にある手順に従って変更できます。

4. Azure AD で目的のカスタム ドメインを追加して検証したら、それらを G Suite で再度検証する必要があります。 G Suite でドメインを検証するには、次の手順を参照してください。

    a. [G Suite の管理コンソール](https://admin.google.com/)で、 **[ドメイン]** を選択します。

    ![G Suite のドメイン](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. **[ドメインやドメイン エイリアスを追加]** を選択します。

    ![G Suite のドメインの追加](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. **[別のドメインを追加]** を選択し、追加するドメインの名前を入力します。

    ![G Suite の別の追加](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. **[Continue and verify domain ownership]\(続行してドメインの所有権を確認\)** を選択します。 次に、手順に従って、ドメイン名を所有していることを確認します。 Google でドメインを検証する方法に関する包括的な手順については、「[サイトの所有権を確認する](https://support.google.com/webmasters/answer/35179)」を参照してください。

    e. G Suite に追加しようとしているすべての追加のドメインについて、前の手順を繰り返します。

5. 次に、G Suite でユーザー プロビジョニングを管理するためにどの管理者アカウントを使用するかを決定します。 **[Admin Roles] (管理者ロール)** に移動します。

    ![G Suite の管理者](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. そのアカウントの **[Admin role] (管理者ロール)** で、そのロールの **[特権]** を編集します。 このアカウントをプロビジョニングに使用できるように、 **[Admin API Privileges]\(管理 API の権限\)** がすべて有効になっていることを確認します。

    ![G Suite の管理者権限](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから G Suite を追加する

Azure AD アプリケーション ギャラリーから G Suite を追加して、G Suite へのプロビジョニングの管理を開始します。 SSO のために G Suite を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* G Suite にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>手順 5. G Suite への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

> [!NOTE]
> G Suite の Directory API エンドポイントについて詳しくは、[Directory API](https://developers.google.com/admin-sdk/directory) に関するページをご覧ください。

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>Azure AD で G Suite の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。 ユーザーは portal.azure.com にログインする必要があり、aad.portal.azure.com を使用できなくなります

    ![[エンタープライズ アプリケーション] ブレード](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![[すべてのアプリケーション] ブレード](./media/g-suite-provisioning-tutorial/all-applications.png)

2. アプリケーションの一覧で **[G Suite]** を選択します。

    ![アプリケーションの一覧の G Suite のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。 **[開始]** をクリックします。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

      ![[開始] ブレード](./media/g-suite-provisioning-tutorial/get-started.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクション下にある **[承認する]** をクリックします。 ブラウザーの新しいウィンドウで Google 認可ダイアログ ボックスにリダイレクトされます。

      ![G Suite の承認](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Azure AD に G Suite テナントを変更するためのアクセス許可を付与することを確認します。 **[Accept]\(承認\)** を選択します。

     ![G Suite のテナントの承認](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. Azure Portal で、 **[テスト接続]** をクリックして Azure AD が G Suite に接続できることを確認します。 接続できない場合は、使用中の G Suite アカウントに管理者アクセス許可があることを確認してから、もう一度試します。 その後、**承認** 手順を再び試します。

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Provision Azure Active Directory Users]\(Azure Active Directory ユーザーをプロビジョニングする\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から G Suite に同期されるユーザー属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新操作のために G Suite のユーザー アカウントを照合するために使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に G Suite API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |primaryEmail|String|
   |relations.[type eq "manager"].value|String|
   |name.familyName|String|
   |name.givenName|String|
   |suspended|String|
   |externalIds.[type eq "custom"].value|String|
   |externalIds.[type eq "organization"].value|String|
   |addresses.[type eq "work"].country|String|
   |addresses.[type eq "work"].streetAddress|String|
   |addresses.[type eq "work"].region|String|
   |addresses.[type eq "work"].locality|String|
   |addresses.[type eq "work"].postalCode|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |emails.[type eq "work"].address|String|
   |organizations.[type eq "work"].department|String|
   |organizations.[type eq "work"].title|String|
   |phoneNumbers.[type eq "work"].value|String|
   |phoneNumbers.[type eq "mobile"].value|String|
   |phoneNumbers.[type eq "work_fax"].value|String|
   |addresses.[type eq "home"].country|String|
   |addresses.[type eq "home"].formatted|String|
   |addresses.[type eq "home"].locality|String|
   |addresses.[type eq "home"].postalCode|String|
   |addresses.[type eq "home"].region|String|
   |addresses.[type eq "home"].streetAddress|String|
   |addresses.[type eq "other"].country|String|
   |addresses.[type eq "other"].formatted|String|
   |addresses.[type eq "other"].locality|String|
   |addresses.[type eq "other"].postalCode|String|
   |addresses.[type eq "other"].region|String|
   |addresses.[type eq "other"].streetAddress|String|
   |addresses.[type eq "work"].formatted|String|
   |changePasswordAtNextLogin|String|
   |emails.[type eq "home"].address|String|
   |emails.[type eq "other"].address|String|
   |externalIds.[type eq "account"].value|String|
   |externalIds.[type eq "custom"].customType|String|
   |externalIds.[type eq "customer"].value|String|
   |externalIds.[type eq "login_id"].value|String|
   |externalIds.[type eq "network"].value|String|
   |gender.type|String|
   |GeneratedImmutableId|String|
   |識別子|String|
   |ims.[type eq "home"].protocol|String|
   |ims.[type eq "other"].protocol|String|
   |ims.[type eq "work"].protocol|String|
   |includeInGlobalAddressList|String|
   |ipWhitelisted|String|
   |organizations.[type eq "school"].costCenter|String|
   |organizations.[type eq "school"].department|String|
   |organizations.[type eq "school"].domain|String|
   |organizations.[type eq "school"].fullTimeEquivalent|String|
   |organizations.[type eq "school"].location|String|
   |organizations.[type eq "school"].name|String|
   |organizations.[type eq "school"].symbol|String|
   |organizations.[type eq "school"].title|String|
   |organizations.[type eq "work"].costCenter|String|
   |organizations.[type eq "work"].domain|String|
   |organizations.[type eq "work"].fullTimeEquivalent|String|
   |organizations.[type eq "work"].location|String|
   |organizations.[type eq "work"].name|String|
   |organizations.[type eq "work"].symbol|String|
   |OrgUnitPath|String|
   |phoneNumbers.[type eq "home"].value|String|
   |phoneNumbers.[type eq "other"].value|String|
   |websites.[type eq "home"].value|String|
   |websites.[type eq "other"].value|String|
   |websites.[type eq "work"].value|String|
   

10. **[マッピング]** セクションの **[Provision Azure Active Directory Groups]\(Azure Active Directory グループをプロビジョニングする\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から G Suite に同期されるグループ属性を確認します。 **[Matching] (照合)** プロパティとして選択されている属性は、更新操作のために G Suite のグループを照合するために使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

      |属性|Type|
      |---|---|
      |email|String|
      |メンバー|String|
      |name|String|
      |description|String|

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. G Suite で Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択することによって、G Suite にプロビジョニングするユーザーまたはグループ、あるいはその両方を定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

> [!NOTE]
> ユーザーが Azure AD ユーザーのメール アドレスを使用する既存の個人/コンシューマー アカウントを既に持っている場合は、何らかの問題が発生する可能性がありますが、ディレクトリ同期を実行する前に Google の転送ツールを使用して解決できます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="change-log"></a>ログの変更

* 10/17/2020 - G Suite の追加のユーザーおよびグループ属性に対するサポートが追加されました。
* 10/17/2020 - G Suite ターゲットの属性名が、[ここで](https://developers.google.com/admin-sdk/directory)定義されている内容に一致するように更新されました。
* 10/17/2020 - 既定の属性マッピングが更新されました。
* 03/18/2021 - 新しいユーザーについては、ID ではなくマネージャーのメールが同期されるようになりました。 マネージャーを ID としてプロビジョニングされている既存のユーザーについては、[Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) を通じて再開することができます。その際、メールが確実にプロビジョニングされるよう、スコープは "フル" とします。 この変更は GSuite プロビジョニング ジョブにのみ影響し、Goov2OutDelta で始まる古いプロビジョニング ジョブには影響しません。 マネージャーのメールがプロビジョニングされるのは、ユーザーが最初に作成されたときか、またはマネージャーが変わったときであることに注意してください。 マネージャーが自分のメール アドレスを変更しても、マネージャーのメールはプロビジョニングされません。 

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
