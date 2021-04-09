---
title: チュートリアル:Azure Active Directory を使用して Playvox を自動ユーザー プロビジョニング用に構成する | Microsoft Docs
description: Azure AD から Playvox へのユーザー アカウントのプロビジョニングとプロビジョニング解除を自動的に実行する方法について説明します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96862481"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>チュートリアル:Playvox を構成して自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために Playvox と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[Playvox](https://www.playvox.com) に対するユーザーまたはグループのいずれかをプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容やしくみについての重要な情報と、よく寄せられる質問については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Playvox でユーザーを作成する。
> * アクセスが不要になった Playvox のユーザーを削除する。
> * Azure AD と Playvox の間でユーザー属性の同期を維持する。

## <a name="prerequisites"></a>前提条件

このチュートリアル内のシナリオでは、次の前提条件がすでに満たされていることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)が割り当てられた Azure AD のユーザー アカウント。 たとえば、アプリケーション管理者、クラウド アプリケーション管理者、アプリケーションの所有者、またはグローバル管理者ロールが割り当てられているアカウントです。
* Super Admin アクセス許可がある、[Playvox](https://www.playvox.com) のユーザー アカウント。

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1:プロビジョニングのデプロイを計画する

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。

2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。

3. [Azure AD と Playvox の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>手順 2:Azure AD を使用したプロビジョニングをサポートするために Playvox を構成する

1. Playvox 管理コンソールにログインし、 **[設定] > [API キー]** にアクセスします。

2. **[API キーの作成]** を選択します。

    ![Playvox ユーザー インターフェイスの [API キーの作成] ボタンの場所を示す部分的なスクリーンショット。](media/playvox-provisioning-tutorial/create.png)

3. API キーのわかりやすい名前を入力し、 **[保存]** を選択します。 API キーが生成されたら、 **[閉じる]** を選択します。

4. 作成した API キーの **[詳細]** アイコンを選択します。

    ![Playvox ユーザー インターフェイス内の詳細アイコン (虫眼鏡) の場所を示す部分的なスクリーンショット。](media/playvox-provisioning-tutorial/api.png)

5. **BASE64 KEY** の値をコピーし、保存します。 その後、Azure portal で、Playvox アプリケーションの **[プロビジョニング]** タブにある **[シークレット トークン]** テキスト ボックスにこの値を入力します。

    ![BASE64 キーの値が強調表示されている、[API キーの詳細] メッセージ ボックスのスクリーンショット。](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>手順 3:Azure AD アプリケーション ギャラリーから Playvox を追加する

Playvox へのプロビジョニングの管理を開始するには、アプリケーション ギャラリーから Azure AD テナントに Playvox を追加します。 詳しくは、「[クイック スタート: Azure Active Directory (Azure AD) テナントにアプリケーションを追加する](../manage-apps/add-application-portal.md)」を参照してください。

シングル サインオン (SSO) のために Playvox を以前に設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4:プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスは、アプリケーションへの割り当てや、ユーザーまたはグループの属性のいずれかに基づいてプロビジョニングされるユーザーのスコープを設定するために使用します。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定するには、「[Azure Active Directory のアプリに対するユーザー割り当ての管理](../manage-apps/assign-user-or-group-access-portal.md)」を参照して、ユーザーまたはグループをアプリケーションに割り当てる方法について確認します。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定するには、「[スコープ フィルターを使用した属性ベースのアプリケーション プロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)」で説明されているスコープ フィルターを使用します。

次の点に注意してください:

* Playvox にユーザーを割り当てるときは、既定のアクセス以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセスである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)して他のロールを追加することができます。

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーまたはグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーまたはグループに基づいている場合、アプリに 1 人 (1 つ) または 2 人 (2 つ) のユーザーまたはグループを割り当てることでセットのサイズを制御できます。 プロビジョニングのスコープにすべてのユーザーとグループが含まれている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定して、テスト セットのサイズを制限することができます。

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>手順 5:Playvox への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいてユーザーやグループを作成、更新、無効化する手順について説明します。

Azure AD で Playvox の自動ユーザー プロビジョニングを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] と [すべてのアプリケーション] の項目が強調表示されている、Azure portal の部分的なスクリーンショット。](common/enterprise-applications.png)

2. アプリケーションの一覧で、**Playvox** を検索して選択します。

    ![アプリケーションの検索ボックスが強調表示されている、アプリケーションの一覧の部分的なスクリーンショット。](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] メニュー項目を示す部分的なスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング モード] ドロップダウン リスト ボックスで選択されている [自動] オプションを示す [プロビジョニング] タブの部分的なスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Playvox の **テナント URL** を次の形式で入力します。

    `https://{tenant}.playvox.com/scim/v1`

    前の手順 2 でコピーした **[シークレット トークン]** を入力します。 次に、Azure AD から Playvox に接続できることを確認するために、 **[テスト接続]** を選択します。 接続できない場合は、使用中の Playvox アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![[テナント URL] と [シークレット トークン] テキスト ボックスを含む、[管理者資格情報] セクションを示す部分的なスクリーンショット。[テスト接続] リンクが強調表示されています。](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** テキスト ボックスにプロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力します。 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![[通知用メール] テキスト ボックスとメール通知のチェック ボックスが示されている部分的なスクリーンショット。](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Synchronize Azure Active Directory Users to Playvox]\(Azure Active Directory ユーザーを Playvox に同期する\)** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Playvox に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Playvox のユーザー アカウントとの照合に使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が Playvox API でサポートされていることを確認します。 すべての変更をコミットするには、 **[保存]** を選択します。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |displayName|String|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |name.formatted|String|
   |externalId|String|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の手順を参照してください。

11. Playvox に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![[プロビジョニング状態] が [オン] に設定されている、[設定] セクションの部分的なスクリーンショット。](common/provisioning-toggle-on.png)

12. 引き続き **[設定]** で、 **[スコープ]** で目的の値を選択して Playvox にプロビジョニングするユーザーまたはグループを定義します。

    ![[スコープ] ドロップダウン リスト ボックスが示されている [設定] セクションの部分的なスクリーンショット。](common/provisioning-scope.png)

13. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![[保存] および [破棄] のオプションを示す部分的なスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは、以降のサイクルよりも時間がかかります。 以降のサイクルは、Azure AD プロビジョニング サービスが実行されている場合、約 40 分ごとに発生します。

## <a name="step-6-monitor-your-deployment"></a>手順 6:デプロイを監視する

プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、「[検疫状態のアプリケーションのプロビジョニング](../app-provisioning/application-provisioning-quarantine-status.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)