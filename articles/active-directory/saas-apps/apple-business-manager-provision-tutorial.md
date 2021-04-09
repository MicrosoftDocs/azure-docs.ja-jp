---
title: チュートリアル:Azure Active Directory での自動ユーザー プロビジョニングに対応するように Apple Business Manager を構成する | Microsoft Docs
description: Azure AD から Apple Business Manager に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4ad30031-9904-4ac3-a4d2-e8c28d44f319
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: Zhchia
ms.openlocfilehash: b4f24c9beffcd67fb84940c2e159da615496d9aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96180372"
---
# <a name="tutorial-configure-apple-business-manager-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニングに対応するように Apple Business Manager を構成する



このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Apple Business Manager と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、ユーザーの [Apple Business Manager](https://business.apple.com/) へのプロビジョニングおよびプロビジョニング解除が、Azure AD プロビジョニング サービスを使用して自動的に行われるようになります。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Apple Business Manager でユーザーを作成する
> * アクセスが不要になった場合に Apple Business Manager のユーザーを削除する
> * Azure AD と Apple Business Manager の間でユーザー属性の同期を維持する

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* 管理者またはユーザー マネージャーの役割を持つ Apple Business Manager のアカウント。

> [!NOTE]
> Azure AD へのトークン転送と正常な接続の確立は 4 日以内に完了する必要があり、そうしないと、このプロセスを再度開始しなければならなくなります。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Apple Business Manager の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-apple-business-manager-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Apple Business Manager を構成する

1. Apple Business Manager で、管理者またはユーザー マネージャーの役割を持つアカウントを使用してサインインします。
2. サイドバーの下部にある [設定] をクリックし、[組織の設定] の下にある [データ ソース] をクリックしてから、[データ ソースに接続] をクリックします。
3. SCIM の横にある [接続] をクリックし、警告を注意深く読んでから、[コピー] をクリックし、その後で [閉じる] をクリックします。
[[Connect to SCIM] ウィンドウ。トークンとその下に [コピー] ボタンが表示されている。] テナント URL を Apple Business Manager から Azure AD にコピーできるように、このウィンドウは開いたままにしておきます (https://federation.apple.com/feeds/business/scim )。

    ![Apple Business Manager](media/applebusinessmanager-provisioning-tutorial/scim-token.png)

> [!NOTE]
> シークレット トークンは、Azure AD の管理者以外のユーザーと共有しないでください。

## <a name="step-3-add-apple-business-manager-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Apple Business Manager を追加する

Azure AD アプリケーション ギャラリーから Apple Business Manager を追加して、Apple Business Manager へのプロビジョニングの管理を開始します。 前に SSO 用に Apple Business Manager を設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Apple Business Manager にユーザーを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、これを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="step-5-configure-automatic-user-provisioning-to-apple-business-manager"></a>手順 5. Apple Business Manager への自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で、 **[Apple Business Manager]** を選択します。

    ![アプリケーションの一覧の Apple Business Manager](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブ](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動]](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** および **[シークレット トークン]** に、Apple Business Manager から取得した **SCIM 2.0 ベース URL およびアクセス トークン** の値をそれぞれ入力します。 **[テスト接続]** をクリックして、Azure AD が Apple Business Manager に接続できることを確認します。 接続できない場合は、お使いの Apple Business Manager アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![トークン](common/provisioning-testconnection-tenanturltoken.png)

> [!NOTE]
>接続に成功すると、Apple Business Manager によって SCIM 接続がアクティブとして表示されます。 このプロセスでは、Apple Business Manager に最新の接続状態が反映されるまで最長で 60 秒ほどかかる場合があります。

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Apple Business Manager に同期する]** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Apple Business Manager に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で Apple Business Manager のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |active|Boolean|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.givenName|String|
   |externalId|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Apple Business Manager に対して Azure AD プロビジョニング サービスを有効にするには、[設定] セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Apple Business Manager にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。  

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
* [Apple Business Manager で SCIM を使用するための必要条件を確認する](https://support.apple.com/guide/apple-business-manager/apdd88331cd6)
* [Apple Business Manager でのユーザー ID の用途](https://support.apple.com/guide/apple-business-manager/apd69e1e48e9)
* [SCIM を使用して Apple Business Manager にユーザーをインポートする](https://support.apple.com/guide/apple-business-manager/apd3ec7b95ad)
* [Apple Business Manager で SCIM ユーザー アカウントの競合を解決する](https://support.apple.com/guide/apple-business-manager/apd313013d12)
* [Apple Business Manager に表示される Azure AD のアカウントを削除する](https://support.apple.com/guide/apple-business-manager/apdaa5798fbe)
* [Apple Business Manager で SCIM のアクティビティを表示する](https://support.apple.com/guide/apple-business-manager/apd1bfd8dfde)
* [Apple Business Manager で既存の SCIM トークンと接続を管理する](https://support.apple.com/guide/apple-business-manager/apdc9a823611)
* [Apple Business Manager で SCIM 接続を切断する](https://support.apple.com/guide/apple-business-manager/apd609be3a61)
* [Apple Business Manager での SCIM 接続のトラブルシューティング](https://support.apple.com/guide/apple-business-manager/apd403a0f3bd/web)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)