---
title: Azure Sentinel のアクセス許可 | Microsoft Docs
description: この記事では、Azure Sentinel でロールベースのアクセス制御を使用してアクセス許可をユーザーに割り当て、ロールごとに許可するアクションを特定する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: rkarlin
manager: angrobe
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: rkarlin
ms.openlocfilehash: 0479eba13fd747ea1bcab2672fce1df2156802e8
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/09/2019
ms.locfileid: "74947588"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel のアクセス許可

Azure Sentinel では、 [ロールベースのアクセス制御 (RBAC)](../role-based-access-control/role-assignments-portal.md) を使用して、Azure のユーザー、グループ、およびサービスに割り当てることができる [組み込みロール](../role-based-access-control/built-in-roles.md) を提供しています。

RBAC を使用すると、セキュリティ運用チーム内でロールを使用して作成し、Azure Sentinel への適切なアクセス権を付与することができます。 ロールに基づいて、Azure Sentinel へのアクセス権を持つユーザーが表示できる内容を詳細に制御できます。 RBAC ロールは、Azure Sentinel ワークスペースで直接割り当てるか、ワークスペースが属しているサブスクリプションまたはリソース グループに割り当てることができます。

組み込みの Azure Sentinel ロールは 3 つあります。  
**すべての Azure Sentinel の組み込みロールでは、Azure Sentinel ワークスペース内のデータに対して読み取りアクセス権が付与されます。**
- [Azure Sentinel 閲覧者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel レスポンダー](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel 共同作成者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Azure Sentinel 専用 RBAC ロールに加えて、Azure Sentinel ワークスペースやその他のリソースへのアクセスを含む広範なアクセス許可セットを付与できる Azure と Log Analytics RBAC ロールもあります。

- **Azure ロール:** [所有者](../role-based-access-control/built-in-roles.md#owner)、[共同作成者](../role-based-access-control/built-in-roles.md#contributor)、および[閲覧者](../role-based-access-control/built-in-roles.md#reader)です。 Azure ロールによって、Log Analytics ワークスペースや Azure Sentinel リソースなど、すべての Azure リソースへのアクセス権が付与されます。

-   **Log Analytics のロール:** [Log Analytics 共同作成者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)、[Log Analytics 閲覧者](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics ロールによっては、すべての Log Analytics ワークスペースにまたがるアクセス権が付与されます。 

> [!NOTE]
> また、Log Analytics ロールによって、すべての Azure リソースにまたがる読み取りアクセス権も付与されますが、Log Analytics リソースに対する書き込みアクセス許可のみが割り当てられます。


たとえば、**Azure Sentinel 閲覧者**および **Azure 共同作成者** (**Azure Sentinel 共同作成者**ではありません) ロールを持つユーザーは、Azure Sentinel ではデータを編集できますが、持っているのは **Sentinel 閲覧者**のアクセス許可のみです。 そのため、Azure Sentinel 内にのみアクセス許可を付与する場合は、このユーザーの以前のアクセス許可を慎重に削除して、別のリソースに必要なアクセス許可ロールが無効にならないようにする必要があります。

> [!NOTE]
>- Azure Sentinel では、自動化された脅威の対応にプレイブックを使用します。 プレイブックは Azure Logic Apps を利用する、別の Azure リソースです。 セキュリティ運用チームの特定のメンバーに、セキュリティ オーケストレーション、自動化、および対応 (SOAR) の操作に Logic Apps を使用するオプションを割り当てることができます。 [Logic App 共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)または [Logic App オペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator) ロールを使用して、プレイブックを使用するための明示的なアクセス許可を割り当てることができます。
>- データ コネクタを追加するために、各コネクタに必要なロールがコネクタの種類ごとにあり、関連するコネクタ ページに一覧表示されます。 また、どのデータ ソースを接続する場合でも、Azure Sentinel ワークスペースに対して書き込みアクセス許可をもっている必要があります。



## <a name="roles-and-allowed-actions"></a>ルールと許可されているアクション

次の表は、Azure Sentinel でのロールと許可されているアクションを示しています。 ○ は、アクションがそのロールに許可されていることを示します。

| Role | プレイブックを作成して実行する| ダッシュボード、分析ルール、およびその他の Azure Sentinel リソースの作成と編集 | インシデントを管理する (無視、割り当てなど) | データ、インシデント、ダッシュボード、その他の Azure Sentinel リソースを表示する |
|--- |---|---|---|---|
| Azure Sentinel 閲覧者 | -- | -- | -- | X |
| Azure Sentinel レスポンダー|--|--| X | X |
| Azure Sentinel 共同作成者 | -- | X | X | X |
| Azure Sentinel 共同作成者 + ロジック アプリの共同作成者 | X | X | X | X |


> [!NOTE]
> - タスクを実行するために必要となる最小限の権限ロールをユーザーに割り当てることをお勧めします。 たとえば、Azure Sentinel 共同作成者ロールは、ルールまたはダッシュボードを作成する必要があるユーザーにのみ割り当てます。
> - リソース グループのスコープで Azure Sentinel のアクセス許可を設定することをお勧めします。こうすることで、同じリソース グループ内のすべての Azure Sentinel ワークスペースにユーザーがアクセスできるようになります。
>
## <a name="building-custom-rbac-roles"></a>カスタム RBAC ロールの作成

組み込みの RBAC ロールの使用に加え、またはその代わりに、Azure Sentinel のカスタムの RBAC ロールを作成することができます。 Azure Sentinel のカスタム RBAC ロールは、[Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) および [Azure Log Analytics リソース](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)に対する特定のアクセス許可に基づいて、他の[カスタム Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) ロールを作成する場合と同じ方法で作成されます。

## <a name="advanced-rbac-on-the-data-you-store-in-azure-sentinel"></a>Azure Sentinel に格納するデータに対する高度な RBAC
  
Azure Sentinel ワークスペースのデータに対して、Log Analytics の高度なロールベースのアクセス制御を使用できます。 これには、データ型ごとのロールベースのアクセス制御と、リソース中心のロールベースのアクセス制御の両方が含まれます。 Log Analytics ロールの詳細については、 [Azure Monitor でのログ データとワークスペースの管理](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure Sentinel ユーザーのロールと、各ロールでユーザーが実行できる操作について学習しました。

* [Azure Sentinel ブログ](https://aka.ms/azuresentinelblog)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
