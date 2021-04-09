---
title: チュートリアル:MidiusFlow を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure AD から MediusFlow に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: a49258208f7a5945ac71c8f17db56fccfdcd6515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662002"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングのために MediusFlow を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために MediusFlow と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、ユーザーとグループの [MediusFlow](https://www.mediusflow.com/) へのプロビジョニングおよびプロビジョニング解除が Azure AD プロビジョニング サービスを使って自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * MediusFlow でユーザーを作成する
> * アクセスが不要になった場合に MediusFlow のユーザーを削除する
> * Azure AD と MediusFlow の間でユーザー属性の同期を維持する
> * MediusFlow でグループとグループ メンバーシップをプロビジョニングする
> * MediusFlow へのシングル サインオン (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md) 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* 品質保証または運用テナントがあるアクティブな MediusFlow サブスクリプション。
* Mediusflow で構成を実行できる管理者アクセス権を持つ MediusFlow のユーザー アカウント。
* ユーザーをプロビジョニングする必要がある MediusFlow テナントに会社が追加されていること。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と MediusFlow の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように MediusFlow を構成する

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>MediusFlow 内で Microsoft 365 アプリをアクティブ化する
まず、次の手順を実行して、Azure AD ログインと MediusFlow 内の Azure AD 構成機能へのアクセスを有効にします。

#### <a name="user-login"></a>ユーザー ログイン
Microsoft 365/Azure AD へのログイン フローを有効にするには、こちら (https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) ) の記事を参照してください。

#### <a name="user-transfer-configuration"></a>ユーザー転送の構成
Azure AD からのプロビジョニングのためにユーザーの構成ポータルを有効にするには、[こちら](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup)の記事を参照してください。

#### <a name="configure-user-provisioning"></a>ユーザー プロビジョニングの構成

1.  テナント ID を指定して [MediusFlow の管理コンソール](https://office365.cloudapp.mediusflow.com/)にログインします。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="MediusFlow 管理コンソールのスクリーンショット。最初の統合手順で、[MediusFlow tenant name]\(MediusFlow テナント名\) ボックスと [Authenticate]\(認証\) ボタンが強調表示されています。" border="false":::

2. MediusFlow との接続を確認します。

    ![確認](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Azure AD テナント ID を指定します。

    ![テナント ID を指定する](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   取得する方法について詳しくは、[FAQ](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) を参照してください。

4. 構成を保存します。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="4 番目の統合手順を示す MediusFlow 管理コンソールのスクリーンショット。[Save configuration]\(構成の保存\) ボタンが強調表示されています。" border="false":::

5. [User Provisioning]\(ユーザー プロビジョニング\) を選択して **[OK]** をクリックします。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="5 番目の統合手順を示す MediusFlow 管理コンソールのスクリーンショット。[Use user provisioning]\(ユーザー プロビジョニングの使用\) ボタンと [Ok] ボタンが強調表示されています。" border="false":::

6. **[Generate Secret Key]\(秘密鍵の生成\)** をクリックします。 この値をコピーして保存します。この値は、Azure portal の MediusFLow アプリケーションの **[プロビジョニング]** タブにある **[シークレット トークン]** フィールドに入力します。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="MediusFlow 管理コンソールの [User provisioning configuration]\(ユーザー プロビジョニングの構成\) タブのスクリーンショット。[Generate secret key]\(秘密キーの生成\) ボタンと [Copy]\(コピー\) ボタンが強調表示されています。" border="false":::

7. **[OK]** をクリックします。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="[Ok] をクリックして新しいシークレット キーを生成することをユーザーに通知する、MediusFlow 管理コンソールのスクリーンショット。[Ok] ボタンが強調表示されています。" border="false":::

8. MediusFlow で事前定義した一連のロール、会社、およびその他の一般的な構成を使用してユーザーをインポートするには、最初に構成する必要があります。 まず、 **[Add new configuration]\(新しい構成の追加\)** をクリックして構成を追加します。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="MediusFlow 管理コンソールの [User provisioning configuration]\(ユーザー プロビジョニングの構成\) タブのスクリーンショット。[Add new configuration]\(新しい構成の追加\) ボタンが強調表示されています。" border="false":::

9. ユーザーの既定の設定を指定します。 このビューでは、既定の属性を設定することができます。 標準設定で問題ない場合は、有効な会社名を入力するだけで十分です。 これらの構成設定は Mediusflow からフェッチされるため、最初に構成する必要があります。 詳細については、この記事の「**前提条件**」セクションを参照してください。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="MediusFlow の [Add new configuration]\(新しい構成の追加\) ウィンドウのスクリーンショット。ロケールの設定、フィルター、ユーザー ロールなど、多くの設定が表示されています。" border="false":::

10. **[保存]** をクリックして、構成を保存します。

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="MediusFlow 管理コンソールの [User provisioning configuration]\(ユーザー プロビジョニングの構成\) タブのスクリーンショット。[保存] ボタンが強調表示されています。" border="false":::

11. ユーザー プロビジョニング リンクを取得するには、 **[Copy SCIM Link]\(SCIM リンクをコピー\)** をクリックします。 この値をコピーして保存します。 この値は、Azure portal の MediusFLow アプリケーションの **[プロビジョニング]** タブにある **[テナント URL]** フィールドに入力します。
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="MediusFlow 管理コンソールの [User provisioning configuration]\(ユーザー プロビジョニングの構成\) タブのスクリーンショット。[Copy SCIM link]\(SCIM リンクのコピー\) ボタンが強調表示されています。" border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから MediusFlow を追加する

Azure AD アプリケーション ギャラリーから MediusFlow を追加して、MediusFlow へのプロビジョニングの管理を開始します。 SSO のために MediusFlow を以前に設定した場合は、その同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* MediusFlow にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>手順 5. MediusFlow への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザー、グループ、またはその両方の割り当てに基づいて、TestApp でユーザー、グループ、またはその両方が作成、更新、および無効化されるように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Azure AD で MediusFlow に対する自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[MediusFlow]** を選択します。

    ![アプリケーションの一覧の [MediusFlow] リンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、先ほど取得したテナント URL 値を **[テナント URL]** に入力します。 前に取得したシークレット トークンの値を、 **[シークレット トークン]** に入力します。 **[テスト接続]** をクリックして、Azure AD から MediusFlow への接続を確認します。 接続できない場合は、使用中の MediusFlow アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

      ![テナント URL とシークレット トークンを入力できる場所である [管理者資格情報] ダイアログ ボックスを示すスクリーンショット。](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to MediusFlow]\(Azure Active Directory ユーザーを MediusFlow に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から MediusFlow に同期されるユーザー属性を確認します。 **Matching** プロパティとして選択されている属性は、更新処理で MediusFlow のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に MediusFlow API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |emails[type eq "work"].value|String|
   |name.displayName|String|
   |active|Boolean|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|リファレンス|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:configurationFilter|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:identityProvider|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:nameIdentifier|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText1|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText2|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText3|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText4|String|
   |urn:ietf:params:scim:schemas:extension:medius:2.0:User:customFieldText5|String|


10. **[マッピング]** セクションの **[Synchronize Azure Active Directory Groups to MediusFlow]\(Azure Active Directory グループを MediusFlow に同期する\)** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から MediusFlow に同期されるグループ属性を確認します。 **Matching** プロパティとして選択されている属性は、更新操作で MediusFlow のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    | 属性 | Type |
    |--|--|
    | displayName | String |
    | externalID | String |
    | members | リファレンス |

12. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

13. MediusFlow に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

14. **[設定]** セクションの **[スコープ]** で目的の値を選択して、MediusFlow にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="change-log"></a>ログの変更

* 2021 年 1 月 21 日 - カスタム拡張属性 **configurationFilter**、**identityProvider**、**nameIdentifier**、**customFieldText1**、**customFieldText2**、**customFieldText3**、**customFieldText3**、**customFieldText5** を追加しました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)