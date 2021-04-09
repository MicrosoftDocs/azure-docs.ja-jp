---
title: チュートリアル:getAbstract を構成し、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる | Microsoft Docs
description: Azure Active Directory から getAbstract に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を学習します。
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616130"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>チュートリアル:getAbstract を構成し、自動ユーザー プロビジョニングに対応させる

このチュートリアルでは、自動ユーザー プロビジョニングを構成するために getAbstract と Azure Active Directory (Azure AD) の両方で実行する必要がある手順について説明します。 構成すると、Azure AD による Azure AD プロビジョニング サービスを使用して [getAbstract](https://www.getabstract.com) へのユーザーとグループのプロビジョニングとプロビジョニング解除が自動的に行われます。 このサービスで実行されること、しくみ、よく寄せられる質問の重要な詳細については、[Azure AD によるサービスとしてのソフトウェア (SaaS) アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)に関する記事を参照してください。

## <a name="capabilities-supported"></a>サポートされる機能

> [!div class="checklist"]
> * getAbstract でユーザーを作成する。
> * アクセスが不要になった場合に getAbstract のユーザーを削除する。
> * Azure AD と getAbstract の間でのユーザー属性の同期を維持する。
> * getAbstract でグループとグループ メンバーシップをプロビジョニングする。
> * getAbstract への[シングル サインオン (SSO) ](getabstract-tutorial.md)を有効にする (推奨)。

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)が割り当てられた Azure AD のユーザー アカウント。 たとえば、アプリケーション管理者、クラウド アプリケーション管理者、アプリケーションの所有者、グローバル管理者があります。
* getAbstract テナント (getAbstract コーポレート ライセンス)。
* Azure AD テナントと getAbstract テナントで SSO が有効になっている。
* getAbstract に対して承認およびクロスドメインの ID 管理 (SCIM) システムを有効にする。 (b2b.itsupport@getabstract.com に電子メールを送信します。)

## <a name="step-1-plan-your-provisioning-deployment"></a>手順 1. プロビジョニングのデプロイを計画する

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
1. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
1. [Azure AD と getAbstract の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>手順 2. Azure AD でのプロビジョニングをサポートするように getAbstract を構成する

1. getAbstract にサインインします。
1. 右上隅にある設定アイコンを選択し、 **[My Central Admin]\(個人用全体管理\)** オプションを選択します。

    ![getAbstract の [My Central Admin]\(個人用全体管理\) を示すスクリーンショット。](media/getabstract-provisioning-tutorial/my-account.png)

1. **[SCIM Admin]\(SCIM 管理\)** オプションを見つけて選択します。

    ![getAbstract の [SCIM Admin]\(SCIM 管理\) を示すスクリーンショット。](media/getabstract-provisioning-tutorial/scim-admin.png)

1. **[Go] \(移動)** を選択します。

    ![getAbstract の SCIM クライアント ID を示すスクリーンショット。](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. **[新しいトークンの生成]** を選択します。

    ![getAbstract の SCIM トークン 1 を示すスクリーンショット。](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. 確認できたら、 **[新しいトークンの生成]** を選択します。 それ以外の場合、 **[キャンセル]** を選択します。

    ![getAbstract の SCIM トークン 2 を示すスクリーンショット。](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. クリップボードにコピーするアイコンを選択するか、トークン全体を選択してコピーします。 また、テナント URL (ベース URL) が `https://www.getabstract.com/api/scim/v2` であることも書き留めてください。 これらの値は、Azure portal で getAbstract アプリケーションの **[プロビジョニング]** タブにある **[シークレット トークン]** および **[テナントの URL]** ボックスに入力します。

    ![getAbstract の SCIM トークン 3 を示すスクリーンショット。](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>手順 3. Azure AD アプリケーション ギャラリーから getAbstract を追加する

Azure AD アプリケーション ギャラリーから getAbstract を追加して、getAbstract へのプロビジョニングの管理を開始します。 SSO のために getAbstract を以前に設定している場合は、その同じアプリケーションを使用できます。 統合を初めてテストするときは、別のアプリを作成することをお勧めします。 ギャラリーからアプリケーションを追加する方法について詳しくは、[こちらのクイックスタート](../manage-apps/add-application-portal.md)に関するページを参照してください。

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>手順 4. プロビジョニングの対象となるユーザーを定義する

Azure AD プロビジョニング サービスを使用して、アプリケーションへの割り当てや、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、以下の[手順](../manage-apps/assign-user-or-group-access-portal.md)を使用して、ユーザーとグループをアプリケーションに割り当てることができます。 ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、[スコープ フィルダーによるアプリのプロビジョニング](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)に関するページで説明されているスコープ フィルターを使用できます。

* getAbstract にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないとマークされます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。

* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニングのスコープが割り当て済みユーザーとグループに設定される場合、このオプションを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>手順 5. getAbstract への自動ユーザー プロビジョニングを構成する

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいて TestApp のユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを構成する手順について説明します。

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Azure AD で getAbstract の自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]**  >  **[すべてのアプリケーション]** の順に選択します。

    ![[エンタープライズ アプリケーション] ペインのスクリーンショット。](common/enterprise-applications.png)

1. アプリケーションの一覧で、 **[getAbstract]** を選択します。

    ![アプリケーションの一覧の getAbstract リンクを示すスクリーンショット。](common/all-applications.png)

1. **[プロビジョニング]** タブを選択します。

    ![[プロビジョニング] タブを示すスクリーンショット。](common/provisioning.png)

1. **[プロビジョニング モード]** を **[自動]** に設定します。

    ![[プロビジョニング モード] が [自動] に設定されていることを示すスクリーンショット。](common/provisioning-automatic.png)

1. **[管理者資格情報]** セクションで、getAbstract の **テナントの URL** と **シークレット トークン** を入力します。 **[テスト接続]** を選択して、Azure AD から getAbstract に接続できることを確認します。 接続できない場合は、間違いなく getAbstract アカウントに管理者アクセス許可があるようにしてから、もう一度試します。

    ![[テナントの URL] ボックスと [シークレット トークン] ボックスを示すスクリーンショット。](common/provisioning-testconnection-tenanturltoken.png)

1. プロビジョニングのエラー通知を受け取るユーザーまたはグループのメール アドレスを **[通知用メール]** フィールドに入力し、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

    ![[通知用メール] ボックスを示すスクリーンショット。](common/provisioning-notification-email.png)

1. **[保存]** を選択します。

1. **[マッピング]** セクションで **[Synchronize Azure Active Directory Users to getAbstract]\(Azure Active Directory ユーザーを getAbstract に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から getAbstract に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で getAbstract のユーザー アカウントを照合するために使用されます。 [照合する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が getAbstract API で確実にサポートされている必要があります。 すべての変更をコミットするには、 **[保存]** を選択します。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |active|Boolean|
   |emails[type eq "work"].value|String|
   |name.givenName|String|
   |name.familyName|String|
   |externalId|String|
   |preferredLanguage|String|

1. **[マッピング]** セクションで、 **[Synchronize Azure Active Directory Groups to getAbstract]\(Azure Active Directory グループを getAbstract に同期する\)** を選択します。

1. **[属性マッピング]** セクションで、Azure AD から getAbstract に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新操作で getAbstract のグループを照合するために使用されます。 すべての変更をコミットするには、 **[保存]** を選択します。

    |属性|Type|フィルター処理のサポート|
    |---|---|---|
    |displayName|String|&check;|
    |externalId|String|
    |members|リファレンス|

1. スコープ フィルターを構成するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)に記載されている手順を参照してください。

1. getAbstract に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニングの状態]** を **[オン]** に変更します。

    ![[プロビジョニングの状態] が [オン] に設定された画面のスクリーンショット。](common/provisioning-toggle-on.png)

1. **[設定]** セクションの **[スコープ]** で目的の値を選択して、getAbstract にプロビジョニングするユーザーまたはグループを定義します。

    ![プロビジョニングのスコープを示すスクリーンショット。](common/provisioning-scope.png)

1. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![[保存] ボタンを示すスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** セクションの **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。

## <a name="step-6-monitor-your-deployment"></a>手順 6. デプロイを監視する

プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* [プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を使用して、正常にプロビジョニングされたユーザーと失敗したユーザーを特定します。
* [進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認して、プロビジョニング サイクルの状態と完了までの時間を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫の状態の詳細については、「[検疫状態のアプリケーションのプロビジョニング](../app-provisioning/application-provisioning-quarantine-status.md)」を参照してください。

## <a name="additional-resources"></a>その他のリソース

* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
