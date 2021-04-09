---
title: Azure Active Directory のエンタイトルメント管理を使用して外部アクセスを管理する
description: 外部アクセスの全体的なセキュリティ プランの一部として、Azure Active Directory のエンタイトルメント管理を使用する方法。
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f06a54f59405d9833194b2e7d4488bc93d2437ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98725180"
---
# <a name="manage-external-access-with-entitlement-management"></a>エンタイトルメント管理を使用して外部アクセスを管理する 


[エンタイトルメント管理](../governance/entitlement-management-overview.md)は、アクセス要求ワークフロー、アクセス割り当て、レビュー、および期限切れ処理を自動化することで、ID とアクセスのライフサイクルを大規模に管理できる、ID ガバナンス機能です。 エンタイトルメント管理を使用すると、管理者以外のユーザーに権限を委任し、そのユーザーに[アクセス パッケージ](../governance/entitlement-management-overview.md)を作成させて、他の組織の外部ユーザーがパッケージへのアクセスを要求できるようにすることができます。 1 段階または複数段階の承認ワークフローを構成して要求を評価し、定期的なレビューを伴う期間限定のアクセス権にユーザーを[プロビジョニング](../governance/what-is-provisioning.md)することができます。 エンタイトルメント管理を使用すれば、外部アカウントに対してポリシーベースのプロビジョニングとプロビジョニング解除を実施することができます。

## <a name="key-concepts-for-enabling-entitlement-management"></a>エンタイトルメント管理を導入するための主要な概念

エンタイトルメント管理について理解するには、次の重要な概念を押さえることが重要です。

### <a name="access-packages"></a>アクセス パッケージ

[アクセス パッケージ](../governance/entitlement-management-overview.md)は、エンタイトルメント管理の基礎となる概念です。 アクセス パッケージとは、ユーザーがプロジェクトでコラボレートしたり、他のタスクを実行したりする必要があるリソースをグループ化して、ポリシーで管理できるようにするものです。 たとえば、アクセス パッケージには次のようなものを含めることができます。

* 特定の SharePoint サイトへのアクセス。

* カスタムの社内アプリケーションや、Salesforce などの SaaS アプリを含むエンタープライズ アプリケーション。

* Microsoft Teams チャネル。

* Microsoft 365 グループ。 

### <a name="catalogs"></a>カタログ

アクセス パッケージは [カタログ](../governance/entitlement-management-catalog-create.md)内に存在します。 関連するリソースとアクセス パッケージをグループ化する際には、カタログを作成し、それらの管理権限を委任します。 まずはカタログにリソースを追加し、その後それらのリソースをアクセス パッケージに追加しします。 たとえば、"Finance" カタログを作成し、[その管理を Finance チームのメンバーに委任](../governance/entitlement-management-delegate.md)します。 すると、そのユーザーが[リソースを追加](../governance/entitlement-management-catalog-create.md)し、アクセス パッケージを作成し、それらのパッケージに対するアクセス承認を管理できるようになります。

次の図は、期限付きのアクセス パッケージに対するアクセス権を外部ユーザーに付与する場合の、一般的なガバナンス ライフサイクルを示したものです。

![外部ユーザーのガバナンス サイクルの図。](media/secure-external-access/6-governance-lifecycle.png)

### <a name="self-service-external-access"></a>セルフサービスの外部アクセス

アクセス パッケージは [Azure AD のマイ アクセス ポータル](../governance/entitlement-management-request-access.md)を使用してを表示できます。これにより、外部ユーザーがアクセスを要求できるようにすることができます。 アクセス パッケージを要求できるユーザーは、ポリシーによって決定されます。 アクセス パッケージの要求を許可するユーザーは、以下にオプションによって指定します。

* 特定の[接続済み組織](../governance/entitlement-management-organization.md)

* 構成済みの接続されたすべての組織

* すべての組織のすべてのユーザー

* テナントに既に参加しているメンバーまたはゲスト ユーザー

### <a name="approvals"></a>承認   
アクセス パッケージには、アクセスに対する必須の承認を含めることができます。 **外部ユーザーについては、必ず承認プロセスを実装するようにしてください**。 承認は、1 段階または複数段階の承認にすることができます。 承認はポリシーによって決定されます。 内部ユーザーと外部ユーザーの両方が同じパッケージにアクセスする必要がある場合は、通常、接続済み組織のカテゴリごとや、内部ユーザーごとに異なるアクセス ポリシーを設定します。

### <a name="expiration"></a>有効期限  
アクセス パッケージには有効期限を含めることができます。 有効期限は特定の日付で設定することもできますし、ユーザーがアクセスできる期間を日数で設定することもできます。 アクセス パッケージの有効期限が切れて、ユーザーが他のアクセス権を持っていない場合には、そのユーザーを表す B2B ゲスト ユーザー オブジェクトを削除するか、サインインをブロックすることができます。 外部ユーザーについては、アクセス パッケージへの有効期限の設定を必須にすることをお勧めします。 アクセス パッケージには、必ずしも有効期限が設定されるわけではありません。 設定されていない場合は、必ずアクセス レビューを行ってください。

### <a name="access-reviews"></a>アクセス レビュー

アクセス パッケージについては、定期的な[アクセス レビュー](../governance/manage-guest-access-with-access-reviews.md)を義務付けることができます。その場合、パッケージの所有者、または指名を受けた担当者は、ユーザーのアクセスが引き続き必要であることを立証する必要があります。 

レビューを設定する前に、次のことを確認してください。

* 担当者

   * アクセスを継続するための条件は何か?

   * 指定されたレビュー担当者は誰か?

* スケジュールされたレビューの実施頻度

   * 組み込みのオプションとしては、毎月、毎四半期、隔週、毎年があります。 

   * 外部アクセスをサポートしているパッケージについては、四半期ごとか、それ以上の頻度で実施することをお勧めします。 

 

> [!IMPORTANT]
> アクセス パッケージのアクセス レビューでは、エンタイトルメント管理を通じて付与されたアクセスのみがレビューされます。 そのため、エンタイトルメント管理以外で外部ユーザーに提供されたアクセス権については、別途レビュー プロセスを設定する必要があります。

アクセス レビューの詳細については、[Azure AD アクセスレビューのデプロイの計画](../governance/deploy-access-reviews.md)に関する記事を参照してください。

## <a name="using-automation-in-entitlement-management"></a>エンタイトルメント管理でのオートメーションの使用

[Microsoft Graph を使用して実行できるエンタイトルメント管理機能](/graph/tutorial-access-package-api)としては、次のようなものがあります

* [アクセス パッケージを管理する](/graph/api/resources/accesspackage?view=graph-rest-beta)

* [アクセス レビューを管理する](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta)

* [接続済み組織を管理する](/graph/api/resources/connectedorganization?view=graph-rest-beta)

* [エンタイトルメント管理の設定を管理する](/graph/api/resources/entitlementmanagementsettings?view=graph-rest-beta)

## <a name="recommendations"></a>推奨事項 

外部アクセスの管理には、エンタイトルメント管理を使用することをお勧めします。

**1 社以上のビジネス パートナーが存在するプロジェクトでは、パートナーのユーザーをオンボードしたり、リソースへのアクセスをプロビジョニングするために、[アクセス パッケージを作成し、使用する](../governance/entitlement-management-access-package-create.md)ようにしましょう**。 

* ディレクトリに既に B2B ユーザーが存在する場合は、それらのユーザーを適切なアクセス パッケージに直接割り当てることもできます。

* アクセスは、[Azure portal](../governance/entitlement-management-access-package-assignments.md) か、または [Microsoft Graph](/graph/api/resources/accesspackageassignmentrequest?view=graph-rest-beta) を使用して割り当てることができます。

**ID ガバナンス設定を使用して、アクセス パッケージが期限切れになったときにディレクトリからユーザーが削除されるようにしましょう**。

![外部ユーザーのライフサイクル管理を構成する画面のスクリーンショット。](media/secure-external-access/6-manage-external-lifecycle.png)

これらの設定は、エンタイトルメント管理を通じてオンボードされたユーザーにのみ適用されます。

**どのユーザーにアクセス権が必要かをよく知っているビジネス所有者に、[カタログとアクセス パッケージの管理を委任](../governance/entitlement-management-delegate.md)しましょう**。

![カタログを構成する画面のスクリーンショット。](media/secure-external-access/6-catalog-management.png)

**‎外部ユーザーがアクセス権を持つ [アクセス パッケージについては、有効期限の設定を必須化](../governance/entitlement-management-access-package-lifecycle-policy.md)しましょう。**


![アクセス パッケージの有効期限を構成する画面のスクリーンショット。](media/secure-external-access/6-access-package-expiration.png)

* プロジェクトベースのアクセス パッケージの終了日がわかっている場合は、[日付] を使用して特定の日付を設定します。 

* それ以外の場合は、有効期限を 365 日以内にすることをお勧めします (ただし、複数年のエンゲージメントであることがわかっている場合を除く)。

* ユーザーがアクセス権を延長できるようにしましょう。

* 延長に際しては、承認の取得を必須条件としましょう。

**[パッケージのアクセス レビューを実施](../governance/manage-guest-access-with-access-reviews.md)して、ゲストへの不適切なアクセス権付与を回避しましょう**。

![アクセス パッケージの新規作成画面のスクリーンショット。](media/secure-external-access/6-new-access-package.png)

* レビューは四半期ごとに実施しましょう。

* コンプライアンス重視のプロジェクトについては、外部ユーザーの自己レビューではなく、特定のレビュー担当者を設定しましょう。 レビュー担当者を決める際には、まずアクセス パッケージ マネージャーを候補として検討することをお勧めします。 

* 機密性の低いプロジェクトの場合は、ユーザーに自己レビューしてもらうことで、ホーム組織を退社したユーザーからアクセス権を削除する際の組織の負担を軽減できます。

詳細については、「[Azure AD のエンタイトルメント管理で外部ユーザーのアクセスを管理する](../governance/entitlement-management-external-users.md)」を参照してください 

### <a name="next-steps"></a>次のステップ

リソースへの外部アクセスをセキュリティで保護する方法については、次の記事を参照してください。 アクションは、表示されている順序で実行することをお勧めします。

1. [外部アクセスに対するセキュリティ体制を決定する](1-secure-access-posture.md)

2. [現在の状態を理解する](2-secure-access-current-state.md)

3. [ガバナンス プランを作成する](3-secure-access-plan.md)

4. [セキュリティにグループを使用する](4-secure-access-groups.md)

5. [Azure AD B2B に移行する](5-secure-access-b2b.md)

6. [エンタイトルメント管理を使用してアクセスをセキュリティで保護する](6-secure-access-entitlement-managment.md) (このページのトピック)

7. [条件付きアクセス ポリシーを使用してアクセスをセキュリティで保護する](7-secure-access-conditional-access.md)

8. [秘密度ラベルを使用してアクセスをセキュリティで保護する](8-secure-access-sensitivity-labels.md)

9. [Microsoft Teams、OneDrive、SharePoint へのアクセスをセキュリティで保護する](9-secure-access-teams-sharepoint.md)

 

