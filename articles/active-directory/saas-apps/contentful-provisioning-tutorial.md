---
title: チュートリアル:Contentful を構成して、Azure Active Directory を使用した自動ユーザー プロビジョニングに対応させる
description: Azure Active Directory (Azure AD) から Contentful に対してユーザー アカウントを自動的にプロビジョニングおよびプロビジョニング解除する方法を学習します。
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97516334"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>チュートリアル:Contentful を構成して自動ユーザー プロビジョニングに対応させる

この記事では、自動ユーザー プロビジョニングを構成するために Contentful と Azure Active Directory (Azure AD) で完了する必要がある手順について説明します。 構成すると、Azure AD では、Azure AD プロビジョニング サービスを使用して、[Contentful](https://www.contentful.com/) に対するユーザーとグループのプロビジョニングおよびプロビジョニング解除が自動的に行われます。 このサービスが実行する内容やしくみについての重要な情報と、よく寄せられる質問については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../app-provisioning/user-provisioning.md)」を参照してください。 

## <a name="capabilities-supported"></a>サポートされる機能

> [!div class="checklist"]
> * Contentful でユーザーを作成する
> * アクセスが不要になった場合に Contentful のユーザーを削除する
> * Azure AD と Contentful の間でユーザー属性の同期を維持する
> * Contentful でグループとグループ メンバーシップをプロビジョニングする
> * Contentful に[シングル サインオンする](contentful-tutorial.md) (推奨)

## <a name="prerequisites"></a>前提条件

このチュートリアルで説明するシナリオでは、次の前提条件目があることを前提としています。

* [Azure AD テナント](../develop/quickstart-create-new-tenant.md)。 
* プロビジョニングを構成するための[アクセス許可](../roles/permissions-reference.md)を持つ Azure AD のユーザー アカウント (アプリケーション管理者、クラウド アプリケーション管理者、アプリケーション所有者、グローバル管理者など)。 
* クロスドメイン ID 管理システム (SCIM) のプロビジョニングをサポートするサブスクリプションを持つ Contentful 組織アカウント。 組織のサブスクリプションについてご質問がある場合は、[Contentful サポート](mailto:support@contentful.com)にご連絡ください。
 
## <a name="plan-your-provisioning-deployment"></a>プロビジョニングのデプロイを計画する

1. [プロビジョニング サービスのしくみ](../app-provisioning/user-provisioning.md)を確認します。
2. [プロビジョニングの対象](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)となるユーザーを決定します。
3. [Azure AD と Contentful の間でマップする](../app-provisioning/customize-application-attributes.md)データを決定します。 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>Azure AD でのプロビジョニングをサポートするように Contentful を構成する

1. Contentful で **サービス ユーザー** アカウントを作成します。 Azure のすべてのプロビジョニング アクセス許可は、このアカウントを通じて提供されます。 **所有者** を、このアカウントの組織ロールとして選択することをお勧めします。

2. **サービス ユーザー** として Contentful にサインインします。

3. 左側のメニューで、 **[組織の設定]**  >  **[アクセス ツール]**  >  **[ユーザーのプロビジョニング]** を選択します。

   ![[アクセス ツール] の下で [ユーザーのプロビジョニング] が強調表示された、Contentful の [組織の設定] メニューのスクリーンショット。](media/contentful-provisioning-tutorial/access.png)

4. **SCIM URL** をコピーし、保存します。 この値は、Azure portal 内で Contentful アプリケーションの **[プロビジョニング]** タブに入力します。

5. **[個人用アクセス トークンの生成]** を選択します。

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. モーダル ウィンドウで、個人用アクセス トークンの名前を入力し、 **[生成]** を選択します。

7. SCIM URL とシークレット トークンが生成されます。 これらの値をコピーし、保存します。 これらの値は、Azure portal 内で Contentful アプリケーションの **[プロビジョニング]** タブに入力します。

    ![C F P A T とトークン プレースホルダー名が強調表示された、[個人用アクセス トークン] ウィンドウのスクリーンショット。](media/contentful-provisioning-tutorial/token.png)


Contentful 管理コンソールでプロビジョニングを構成するときにご質問がある場合は、[Contentful サポート](mailto:support@contentful.com)にお問い合わせください。

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Azure AD アプリケーション ギャラリーから Contentful を追加する

Contentful へのプロビジョニングを管理するには、Azure AD アプリケーション ギャラリーから Contentful を追加します。 以前に Contentful をシングル サインオン用に設定している場合は、同じアプリケーションを使用できます。 ただし、統合を初めてテストするには別のアプリを作成することをお勧めします。 [ギャラリーでのアプリケーションの追加](../manage-apps/add-application-portal.md)方法について説明します。 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>プロビジョニングの対象となるユーザーを定義する 

Azure AD プロビジョニング サービスを使用して、アプリケーションへの割り当てや、ユーザーまたはグループの属性に基づいてプロビジョニングされるユーザーのスコープを設定できます。 

割り当てに基づいてアプリにプロビジョニングされるユーザーのスコープを設定する場合、手順を完了して、[ユーザーとグループをアプリケーションに割り当て](../manage-apps/assign-user-or-group-access-portal.md)ます。

ユーザーまたはグループの属性のみに基づいてプロビジョニングされるユーザーのスコープを設定する場合、スコープ フィルターを使用して、[ユーザー アカウントをプロビジョニングするための条件付き規則を定義](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)します。 

* Contentful にユーザーとグループを割り当てるときは、**既定のアクセス** 以外のロールを選択する必要があります。 既定のアクセス ロールを持つユーザーは、プロビジョニングから除外され、プロビジョニング ログで実質的に資格がないと示されます。 アプリケーションで使用できる唯一のロールが既定のアクセス ロールである場合は、[アプリケーション マニフェストを更新](../develop/howto-add-app-roles-in-azure-ad-apps.md)してロールを追加することができます。 
* 小さいところから始めましょう。 全員にロールアウトする前に、少数のユーザーとグループでテストします。 プロビジョニング スコープが割り当て済みユーザーとグループに設定される場合、スコープを制御するには、1 つまたは 2 つのユーザーまたはグループをアプリに割り当てます。 スコープがすべてのユーザーとグループに設定されている場合は、[属性ベースのスコープ フィルター](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)を指定できます。 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Contentful への自動ユーザー プロビジョニングを構成する 

このセクションでは、Azure AD でのユーザーやグループの割り当てに基づいてテスト アプリのユーザーやグループを作成、更新、無効化するように Azure AD プロビジョニング サービスを設定する手順について説明します。

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Azure AD で Contentful の自動ユーザー プロビジョニングを構成する

1. [Azure portal](https://portal.azure.com) にサインインします。 **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。

   ![[すべてのアプリケーション] が強調表示されている、Azure portal の [エンタープライズ アプリケーション] メニューを示したスクリーンショット。](common/enterprise-applications.png)

2. アプリケーションの一覧で **[Contentful]** を選択します。

   ![アプリケーションの一覧に返された最初の 20 件の結果を示したスクリーンショット。](common/all-applications.png)

3. **[プロビジョニング]** タブを選択します。

   ![左側のメニューの [管理] セクションに強調表示された [プロビジョニング] タブのスクリーンショット。](common/provisioning.png)

4. **[プロビジョニング モード]** を **[自動]** に設定します。

   ![[自動] が強調表示されている、[プロビジョニング モード] オプションを示したスクリーンショット。](common/provisioning-automatic.png)

5. **[管理者資格情報]** セクションで、Contentful のテナントの URL とシークレット トークンを入力します。 Azure AD から Contentful へ接続できることを確認するために、 **[テスト接続]** を選択します。 接続できない場合は、使用中の Contentful アカウントに "管理者" アクセス許可があることを確認してから、もう一度試します。

   ![[テスト接続] ボタンが強調表示されている、[テナント URL] および [秘密トークン] テキスト ボックスを示すスクリーンショット。](common/provisioning-testconnection-tenanturltoken.png)

6. **[通知用メール]** に、プロビジョニングのエラー通知を受け取るユーザーまたはグループの電子メール アドレスを入力して、 **[エラーが発生したときにメール通知を送信します]** チェック ボックスをオンにします。

   ![[通知用メール] テキスト ボックスを示すスクリーンショット。](common/provisioning-notification-email.png)

7. **[保存]** を選択します。

8. **[マッピング]** セクションの **[Azure Active Directory ユーザーを Contentful に同期する]** を選択します。

9. **[属性マッピング]** セクションで、Azure AD から Contentful に同期されるユーザー属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Contentful のユーザー アカウントとの照合に使用されます。 [一致する対象の属性](../app-provisioning/customize-application-attributes.md)を変更する場合は、その属性に基づいたユーザーのフィルター処理が確実に Contentful API でサポートされているようにする必要があります。 **[保存]** ボタンをクリックして変更をコミットします。

   |属性|Type|フィルター処理のサポート|
   |---|---|---|
   |userName|String|&check;|
   |name.givenName|String|
   |name.familyName|String|

10. **[マッピング]** セクションの **[Azure Active Directory グループを Contentful に同期する]** を選択します。

11. **[属性マッピング]** セクションで、Azure AD から Contentful に同期されるグループ属性を確認します。 **[照合]** プロパティとして選択されている属性は、更新処理で Contentful のグループとの照合に使用されます。 **[保存]** ボタンをクリックして変更をコミットします。

    |属性|Type|フィルター処理のサポート|
    |---|---|---|
    |displayName|String|&check;|
    |members|リファレンス|

12. スコープ フィルターを設定するには、[スコープ フィルターのチュートリアル](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)で説明している手順を行います。

13. Contentful に対して Azure AD プロビジョニング サービスを有効にするには、 **[設定]** セクションで **[プロビジョニング状態]** を **[オン]** に変更します。

    ![[プロビジョニングの状態] が [オン] と [オフ] に設定された画面のスクリーンショット。](common/provisioning-toggle-on.png)

14. Contentful にプロビジョニングするユーザーまたはグループを定義するには、 **[設定]** セクションの **[スコープ]** で適切なオプションを選択します。

    ![[スコープ] ウィンドウで選択できるオプションを示すスクリーンショット。](common/provisioning-scope.png)

15. プロビジョニングの準備ができたら、 **[保存]** を選択します。

    ![[保存] ボタンと [キャンセル] ボタンを示すスクリーンショット。](common/provisioning-configuration-save.png)

この操作により、 **[設定]** の **[スコープ]** で定義したすべてのユーザーとグループの初期同期サイクルが開始されます。 初期サイクルは後続の同期よりも実行に時間がかかります。後続のサイクルは、Azure AD のプロビジョニング サービスが実行されている限り約 40 分ごとに実行されます。 

## <a name="monitor-your-deployment"></a>デプロイを監視する

プロビジョニングを構成したら、次のリソースを使用してデプロイを監視します。

* 正常にプロビジョニングされたユーザーと失敗したユーザーを特定するには、[プロビジョニング ログ](../reports-monitoring/concept-provisioning-logs.md)を確認します。
* プロビジョニング サイクルの状態と完了までの時間を確認するには、[進行状況バー](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md)を確認します。
* プロビジョニング構成が異常な状態になったと考えられる場合、アプリケーションは検疫されます。 検疫状態の詳細については、[こちら](../app-provisioning/application-provisioning-quarantine-status.md)をご覧ください。  

## <a name="next-steps"></a>次のステップ

* [プロビジョニング アクティビティのログの確認方法およびレポートの取得方法](../app-provisioning/check-status-user-account-provisioning.md)
* [エンタープライズ アプリのユーザー アカウント プロビジョニングの管理](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
