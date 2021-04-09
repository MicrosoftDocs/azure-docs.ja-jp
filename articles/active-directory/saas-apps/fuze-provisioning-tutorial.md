---
title: チュートリアル:Fuze を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory を構成して、ユーザー アカウントを Fuze に自動的にプロビジョニング/プロビジョニング解除する方法を説明します。
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
ms.openlocfilehash: 2fcb85b2c16bc2dd418c2260420733dd8663382a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96179257"
---
# <a name="tutorial-configure-fuze-for-automatic-user-provisioning"></a>チュートリアル:Fuze を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルの目的は、[Fuze](https://www.fuze.com/) に対するユーザーやグループのプロビジョニングまたはプロビジョニング解除が自動的に行われるように Azure AD を構成するため、Fuze と Azure Active Directory (Azure AD) で実行する手順を示すことです。 このサービスが実行する内容、しくみ、よく寄せられる質問の重要な詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。

> [!NOTE]
> 現在、このコネクタはパブリック プレビュー段階にあります。 プレビュー機能を使用するための一般的な Microsoft Azure 使用条件の詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。


## <a name="capabilities-supported"></a>サポートされる機能
> [!div class="checklist"]
> * Fuze でユーザーを作成する
> * アクセスが不要になった場合に Fuze のユーザーを削除する
> * Azure AD と Fuze の間でユーザー属性の同期を維持する
> * Fuze に[シングル サインオンする](./fuze-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。
* [Fuze テナント](https://www.fuze.com/)。
* Admin アクセス許可がある Fuze のユーザー アカウント。


## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する
1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Fuze の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="step-2-configure-fuze-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように Fuze を構成する

Azure AD での自動ユーザー プロビジョニング用に Fuze を構成する前に、Fuze 上で SCIM プロビジョニングを有効にする必要があります。 

1. 以下の必要な情報について、Fuze 担当者に問い合わせることから始めます。

    * 現在社内で使用されている Fuze 製品 SKU の一覧。
    * 会社の所在地に対応する場所コードの一覧。
    * 会社の部門コードの一覧。

2. そのような SKU およびコードは、Fuze の契約書および構成に関するドキュメントを調べるか、または Fuze の担当者に連絡することで確認することができます。

3. 要件が受理されると、統合を有効にするのに必要とされる Fuze 認証トークンが Fuze 担当者から提供されます。 この値を、Azure portal で Fuze アプリケーションの [プロビジョニング] タブ内の [シークレット トークン] フィールドに入力します。

## <a name="step-3-add-fuze-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから Fuze を追加する

Azure AD アプリケーション ギャラリーから Fuze を追加して、Fuze へのプロビジョニングの管理を開始します。 SSO のために Fuze を以前に設定している場合は、その同じアプリケーションを使用することができます。 ただし、統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法の詳細については、[こちら](../manage-apps/add-application-portal.md)を参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用すると、アプリケーションへの割り当て、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[こちら](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明されているスコープ フィルターを使用できます。 

* Fuze にユーザーを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーでテストします。 プロビジョニングのスコープが割り当て済みユーザーに設定される場合、これを制御するには、1 人または 2 人のユーザーをアプリに割り当てます。 スコープがすべてのユーザーに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="step-5-configuring-automatic-user-provisioning-to-fuze"></a>手順 5. Fuze への自動ユーザー プロビジョニングの構成 

このセクションでは、Azure AD プロビジョニング サービスを構成し、Azure AD でのユーザーやグループの割り当てに基づいて Fuze のユーザーやグループを作成、更新、無効化する手順について説明します。

### <a name="to-configure-automatic-user-provisioning-for-fuze-in-azure-ad"></a>Azure AD で Fuze の自動ユーザー プロビジョニングを構成するには、次の操作を行います。

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Fuze]** を選択します。

    ![アプリケーションの一覧の Fuse のリンク](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] オプションが強調表示された [管理] オプションのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[自動] オプションが強調表示された [プロビジョニング モード] ドロップダウン リストのスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションの **[テナント URL]** と **[シークレット トークン]** に、前に Fuze 担当者から取得した **SCIM 2.0 ベースの URL と SCIM 認証トークン** の値を入力します。 **[テスト接続]** をクリックして、Azure AD から Fuze への接続を確保します。 接続できない場合は、使用中の Fuze アカウントに管理者アクセス許可があることを確認してから、もう一度試します。

    ![テナント URL トークン](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** フィールドに、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![通知用メール](common/provisioning-notification-email.png)

7. **[保存]** をクリックします。

8. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Fuze に同期する]** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Fuze に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Fuze のユーザー アカウントとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|
   |---|---|
   |userName|String|
   |name.givenName|String|
   |name.familyName|String|
   |emails[type eq "work"].value|String|
   |active|Boolean|

10. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)の次の手順を参照してください。

11. Fuze に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![プロビジョニングの状態を [オン] に切り替える](common/provisioning-toggle-on.png)

12. **[設定]** セクションの **[スコープ]** で目的の値を選択して、Fuze にプロビジョニングするユーザーやグループを定義します。

    ![プロビジョニングのスコープ](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** をクリックします。

    ![プロビジョニング構成の保存](common/provisioning-configuration-save.png)

これにより、 **[設定]** セクションの **[スコープ]** で 定義したユーザーやグループの初期同期が開始されます。 初期同期は後続の同期よりも実行に時間がかかります。後続の同期は、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する
プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

1. [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
2. [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
3. プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)を参照してください。

## <a name="connector-limitations"></a>コネクタの制限事項

* Fuze では、**エンタイトルメント** と呼ばれるカスタム SCIM 属性がサポートされています。 これらの属性は作成することはできますが、更新することはできません。 

## <a name="change-log"></a>ログの変更

* 06/15/2020 - 統合のレート制限を、10 要求/秒に調整しました。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)。
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)