---
title: チュートリアル:Azure Active Directory を使用した自動ユーザー プロビジョニング用に Tic-Tac Mobile を構成する | Microsoft Docs
description: Azure AD から Tic-Tac Mobile に対してユーザー アカウントを自動的にプロビジョニング、またはプロビジョニング解除する方法を説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 91ae51b9a2785dbc40c55fa58b26763916e8d16c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101644580"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>チュートリアル:自動ユーザー プロビジョニング用に Tic-Tac Mobile を構成する

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Tic-Tac Mobile と Azure Active Directory (Azure AD) の両方で行う必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用した [Tic-Tac Mobile](https://www.tictacmobile.com/) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスで実行されること、しくみ、およびよく寄せられる質問については、[SaaS (サービスとしてのソフトウェア) アプリケーションへのユーザーのプロビジョニングとプロビジョニング解除を Azure AD で自動化する方法](../app-provisioning/user-provisioning.md)に関するページを参照してください。


## <a name="capabilities-supported"></a>サポートされる機能

> [!div class="checklist"]
> * Tic-Tac Mobile のユーザーを作成する。
> * アクセスが不要になった Tic-Tac Mobile のユーザーを削除する。
> * Azure AD と Tic-Tac Mobile の間でユーザー属性の同期を維持する。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)が割り当てられた Azure AD のユーザー アカウント。 たとえば、アプリケーション管理者、クラウド アプリケーション管理者、アプリケーションの所有者、グローバル管理者があります。
* スーパー管理者ロールを持つ [Tic-Tac Mobile](https://www.tictacmobile.com/) アカウント。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と Tic-Tac Mobile の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD によるプロビジョニングをサポートするように Tic-Tac Mobile を構成する

support@tictacmobile.com に連絡して、**テナント URL** と **シークレット トークン** を入手してください。 トークンを受け取るには、Tic-Tac Mobile のスーパー管理者ロールを持っている必要があります。 トークンは、Azure portal で Tic-Tac Mobile アプリケーションの **[プロビジョニング]** タブにある **[シークレット トークン]** ボックスに入力します。

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Tic-Tac Mobile を追加する

Azure AD アプリケーション ギャラリーから Tic-Tac Mobile を追加して、Tic-Tac Mobile へのプロビジョニングの管理を開始します。 シングル サインオンのために Tic-Tac Mobile を以前に設定している場合は、同じアプリケーションを使用できます。 統合を初めてテストするときは、別のアプリを作成してください。 ギャラリーからアプリケーションを追加する方法について詳しくは、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](../manage-apps/add-application-portal.md)」を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当てや、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、「[Azure Active Directory のアプリに対するユーザー割り当ての管理](../manage-apps/assign-user-or-group-access-portal.md)」の手順に従って、ユーザーとグループをアプリケーションに割り当てます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)」で説明されているスコープ フィルターを使用します。

* Tic-Tac Mobile にユーザーとグループを割り当てる場合は、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。
* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、制御を維持するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>手順 5. Tic-Tac Mobile への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて TestApp のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Azure AD で Tic-Tac Mobile の自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ペインのスクリーンショット。](common/enterprise-applications.png)

1. アプリケーションの一覧で **Tic-Tac Mobile** を選択します。

    ![アプリケーションの一覧に表示される Tic-Tac Mobile のリンクを示すスクリーンショット。](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブを示すスクリーンショット。](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング] タブの [自動] オプションを示すスクリーンショット。](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、Tic-Tac Mobile の **[テナントの URL]** と **[シークレット トークン]** を入力します。 **[テスト接続]** を選択して、Azure AD から Tic-Tac Mobile に接続できることを確認します。 接続できない場合は、使用している Tic-Tac Mobile アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![[シークレット トークン] ボックスを示すスクリーンショット。](common/provisioning-testconnection-tenanturltoken.png)

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![[通知用メール] ボックスを示すスクリーンショット。](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Tic-Tac Mobile]\(Azure Active Directory ユーザーを Tic-Tac Mobile に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から Tic-Tac Mobile に同期されるユーザー属性を確認します。 **[Matching]\(照合\)** プロパティとして選択されている属性は、更新処理で Tic-Tac Mobile のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Tic-Tac Mobile API で確実にサポートされている必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |title|String|
   |emails[type eq "work"].value|String|
   |preferredLanguage|String|
   |externalId|String|
   |userType|String|
   |locale|String|
   |timezone|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|String|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|String|

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

1. Tic-Tac Mobile に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![[プロビジョニングの状態] が [オン] に設定された画面のスクリーンショット。](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Tic-Tac Mobile にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープを示すスクリーンショット。](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![プロビジョニングの構成の保存を示すスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する

プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
1. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
1. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、「[検疫状態のアプリケーションのプロビジョニング](../app-provisioning/application-provisioning-quarantine-status.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)