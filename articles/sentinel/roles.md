---
title: Azure Sentinel のアクセス許可 | Microsoft Docs
description: この記事では、Azure Sentinel でロールベースのアクセス制御を使用してアクセス許可をユーザーに割り当て、ロールごとに許可するアクションを特定する方法について説明します。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2020
ms.author: yelevin
ms.openlocfilehash: b48ff1043ae8128a5cbfdcbba0548d89b5af2624
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88565844"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel のアクセス許可

Azure Sentinel では、 [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) を使用して、Azure のユーザー、グループ、およびサービスに割り当てることができる [組み込みロール](../role-based-access-control/built-in-roles.md) が提供されています。

RBAC を使用して、セキュリティ運用チーム内でロールを作成して割り当て、Azure Sentinel への適切なアクセス権を付与します。 さまざまなロールを使用して、Azure Sentinel のユーザーが表示および実行できることをきめ細かく制御できます。 Azure ロールは、Azure Sentinel ワークスペースで直接割り当てるか (下記の注を参照してください)、またはワークスペースが属しているサブスクリプションまたはリソース グループで割り当てることができます (これは、Azure Sentinel が継承するものです)。

## <a name="roles-for-working-in-azure-sentinel"></a>Azure Sentinel で作業するためのロール

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel 固有のロール

Azure Sentinel 専用の組み込みロールが 3 つあります。

**すべての Azure Sentinel の組み込みロールでは、Azure Sentinel ワークスペース内のデータに対して読み取りアクセス権が付与されます。**

- [Azure Sentinel 閲覧者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)では、データ、インシデント、ブック、その他の Azure Sentinel リソースを表示できます。

- [Azure Sentinel レスポンダー](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)では、上記に加え、インシデントの管理 (割り当て、無視など) を行うことができます。

- [Azure Sentinel 共同作成者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)では、上記に加え、ブック、分析ルール、その他の Azure Sentinel リソースの作成と編集を行うことができます。

> [!NOTE]
>
> - 最適な結果を得るには、これらのロールを、Azure Sentinel ワークスペースが含まれる**リソース グループ**に割り当てる必要があります。 Azure Sentinel をサポートするためにデプロイされるすべてのリソースを同じリソース グループに配置する必要があるため、このようにすると、それらのリソースにロールが適用されます。
>
> - もう 1 つの方法として、Azure Sentinel **ワークスペース**自体にロールを直接割り当てます。 これを行う場合は、そのワークスペースの SecurityInsights **ソリューション リソース**でも同じロールを割り当てる必要があります。 他のリソースでもそれらを割り当てることが必要になる場合があり、リソースに対するロールの割り当てを絶えず管理する必要があります。

### <a name="additional-roles-and-permissions"></a>追加のロールとアクセス許可

特定のジョブ要件があるユーザーには、タスクを実行するために、追加のロールまたは特定のアクセス許可を割り当てることが必要な場合があります。

- プレイブックを使用して脅威への対応を自動化する

    Azure Sentinel では、自動化された脅威の対応に**プレイブック**を使用します。 プレイブックは、**Azure Logic Apps** に基づいて構築されている、別の Azure リソースです。 セキュリティ運用チームの特定のメンバーに、セキュリティ オーケストレーション、自動化、および対応 (SOAR) の操作に Logic Apps を使用する権能を割り当てることができます。 [Logic App 共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)または [Logic App オペレーター](../role-based-access-control/built-in-roles.md#logic-app-operator) ロールを使用して、プレイブックを使用するための明示的なアクセス許可を割り当てることができます。

- Azure Sentinel にデータ ソースを接続する

    ユーザーが**データ コネクタ**を追加するには、Azure Sentinel ワークスペースでの書き込みアクセス許可をユーザーに割り当てる必要があります。 また、関連するコネクタのページで示されている、各コネクタに必要な追加のアクセス許可を確認してください。

- インシデントを割り当てるゲスト ユーザー

    ゲスト ユーザーによるインシデント割り当てを可能にする必要がある場合、ユーザーには、Azure Sentinel レスポンダー ロールに加えて、[ディレクトリ閲覧者](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers)のロールが割り当てられている必要があります。 このロールは、Azure RBAC ロール*ではなく*、**Azure Active Directory** ロールであり、通常の (非ゲスト) ユーザーには既定でこのロールが割り当てられていることに注意してください。 

それらを並べて比較したものについては、[下の表](#roles-and-allowed-actions)を参照してください。

### <a name="other-roles-you-might-see-assigned"></a>割り当てられる可能性のあるその他のロール

Azure Sentinel 固有の Azure ロールの割り当てでは、Azure および Log Analytics の他の Azure ロールが他の目的でユーザーに割り当てられている可能性があります。 これらのロールでは、Azure Sentinel ワークスペースや他のリソースへのアクセスを含む広範なアクセス許可のセットが付与されていることに注意してください。

- **Azure ロール:** [所有者](../role-based-access-control/built-in-roles.md#owner)、[共同作成者](../role-based-access-control/built-in-roles.md#contributor)、および[閲覧者](../role-based-access-control/built-in-roles.md#reader)です。 Azure ロールによって、Log Analytics ワークスペースや Azure Sentinel リソースなど、すべての Azure リソースへのアクセス権が付与されます。

- **Log Analytics のロール:** [Log Analytics 共同作成者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)、[Log Analytics 閲覧者](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics のロールでは、Log Analytics ワークスペースに対するアクセス権が付与されます。 

たとえば、**Azure Sentinel 閲覧者**ロールを割り当てられていて、**Azure Sentinel 共同作成者**ロールを割り当てられていないユーザーでも、Azure レベルで**共同作成者**ロールが割り当てられている場合は、Azure Sentinel の項目を編集できます。 そのため、Azure Sentinel 内のユーザーにのみアクセス許可を付与する場合は、このユーザーの以前のアクセス許可を慎重に削除して、別のリソースに必要なアクセス権が無効にならないようにする必要があります。

## <a name="roles-and-allowed-actions"></a>ルールと許可されているアクション

次の表は、Azure Sentinel でのロールと許可されているアクションをまとめたものです。 

| Role | プレイブックを作成して実行する| ブック、分析ルール、およびその他の Azure Sentinel リソースの作成と編集 | インシデントを管理する (無視、割り当てなど) | データ、インシデント、ブック、その他の Azure Sentinel リソースを表示する |
|---|---|---|---|---|
| Azure Sentinel 閲覧者 | -- | -- | -- | &#10003; |
| Azure Sentinel レスポンダー | -- | -- | &#10003; | &#10003; |
| Azure Sentinel 共同作成者 | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel 共同作成者 + ロジック アプリの共同作成者 | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-rbac"></a>カスタム ロールと高度な RBAC

- Azure 組み込みロールの使用に加え、またはその代わりに、Azure Sentinel の Azure カスタム ロールを作成することができます。 Azure Sentinel の Azure カスタム ロールは、[Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) および [Azure Log Analytics リソース](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)に対する特定のアクセス許可に基づいて、他の[カスタム Azure RBAC](../role-based-access-control/custom-roles-rest.md#create-a-custom-role) ロールを作成する場合と同じ方法で作成されます。

- Azure Sentinel ワークスペースのデータに対して、Log Analytics の高度なロールベースのアクセス制御を使用できます。 これには、データ型ベースの RBAC とリソース中心の RBAC の両方が含まれます。 Log Analytics ロールの詳細については、 [Azure Monitor でのログ データとワークスペースの管理](../azure-monitor/platform/manage-access.md#manage-access-using-workspace-permissions)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel ユーザーのロールと、各ロールでユーザーが実行できる操作について学習しました。

* [Azure Sentinel ブログ](https://aka.ms/azuresentinelblog)。 Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
