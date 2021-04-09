---
title: Azure Sentinel のアクセス許可 | Microsoft Docs
description: この記事では、Azure Sentinel で Azure ロールベースのアクセス制御を使用してアクセス許可をユーザーに割り当て、ロールごとに許可するアクションを特定する方法について説明します。
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
ms.openlocfilehash: 77a8e208e463b8ab20d563421d447813b1ce84ee
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772554"
---
# <a name="permissions-in-azure-sentinel"></a>Azure Sentinel のアクセス許可

Azure Sentinel では、[Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) を使用して、Azure のユーザー、グループ、およびサービスに割り当てることができる[組み込みロール](../role-based-access-control/built-in-roles.md)が提供されています。

Azure RBAC を使用して、セキュリティ運用チーム内でロールを作成して割り当て、Azure Sentinel への適切なアクセス権を付与します。 さまざまなロールを使用して、Azure Sentinel のユーザーが表示および実行できることをきめ細かく制御できます。 Azure ロールは、Azure Sentinel ワークスペースで直接割り当てるか (下記の注を参照してください)、またはワークスペースが属しているサブスクリプションまたはリソース グループで割り当てることができます (これは、Azure Sentinel が継承するものです)。

## <a name="roles-for-working-in-azure-sentinel"></a>Azure Sentinel で作業するためのロール

### <a name="azure-sentinel-specific-roles"></a>Azure Sentinel 固有のロール

Azure Sentinel 専用の組み込みロールが 3 つあります。

**すべての Azure Sentinel の組み込みロールでは、Azure Sentinel ワークスペース内のデータに対して読み取りアクセス権が付与されます。**

- [Azure Sentinel 閲覧者](../role-based-access-control/built-in-roles.md#azure-sentinel-reader)では、データ、インシデント、ブック、その他の Azure Sentinel リソースを表示できます。

- [Azure Sentinel レスポンダー](../role-based-access-control/built-in-roles.md#azure-sentinel-responder)では、上記に加え、インシデントの管理 (割り当て、無視など) を行うことができます。

- [Azure Sentinel 共同作成者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)では、上記に加え、ブック、分析ルール、その他の Azure Sentinel リソースの作成と編集を行うことができます。

- [Azure Sentinel Automation 共同作成者](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)は、プレイブックに自動化ルールを追加することを Azure Sentinel に許可します。 これはユーザー アカウント用ではありません。

> [!NOTE]
>
> - 最適な結果を得るには、これらのロールを、Azure Sentinel ワークスペースが含まれる **リソース グループ** に割り当てる必要があります。 Azure Sentinel をサポートするためにデプロイされるすべてのリソースを同じリソース グループに配置する必要があるため、このようにすると、それらのリソースにロールが適用されます。
>
> - もう 1 つの方法として、Azure Sentinel **ワークスペース** 自体にロールを直接割り当てます。 これを行う場合は、そのワークスペースの SecurityInsights **ソリューション リソース** でも同じロールを割り当てる必要があります。 他のリソースでもそれらを割り当てることが必要になる場合があり、リソースに対するロールの割り当てを絶えず管理する必要があります。

### <a name="additional-roles-and-permissions"></a>追加のロールとアクセス許可

特定のジョブ要件があるユーザーには、タスクを実行するために、追加のロールまたは特定のアクセス許可を割り当てることが必要な場合があります。

- プレイブックを使用して脅威への対応を自動化する

    Azure Sentinel では、自動化された脅威の対応に **プレイブック** を使用します。 プレイブックは、**Azure Logic Apps** に基づいて構築されている、別の Azure リソースです。 セキュリティ運用チームの特定のメンバーに、セキュリティ オーケストレーション、自動化、および対応 (SOAR) の操作に Logic Apps を使用する権能を割り当てることができます。 [Logic App 共同作成者](../role-based-access-control/built-in-roles.md#logic-app-contributor)ロールを使用して、プレイブックを使用するための明示的なアクセス許可を割り当てることができます。

- Azure Sentinel にデータ ソースを接続する

    ユーザーが **データ コネクタ** を追加するには、Azure Sentinel ワークスペースでの書き込みアクセス許可をユーザーに割り当てる必要があります。 また、関連するコネクタのページで示されている、各コネクタに必要な追加のアクセス許可を確認してください。

- インシデントを割り当てるゲスト ユーザー

    ゲスト ユーザーによるインシデント割り当てを可能にする必要がある場合、ユーザーには、Azure Sentinel レスポンダー ロールに加えて、[ディレクトリ閲覧者](../active-directory/roles/permissions-reference.md#directory-readers)のロールが割り当てられている必要があります。 このロールは、Azure ロール *ではなく*、**Azure Active Directory** ロールであり、通常の (非ゲスト) ユーザーには既定でこのロールが割り当てられていることに注意してください。 

それらを並べて比較したものについては、[下の表](#roles-and-allowed-actions)を参照してください。

### <a name="other-roles-you-might-see-assigned"></a>割り当てられる可能性のあるその他のロール

Azure Sentinel 固有の Azure ロールの割り当てでは、Azure および Log Analytics の他の Azure ロールが他の目的でユーザーに割り当てられている可能性があります。 これらのロールでは、Azure Sentinel ワークスペースや他のリソースへのアクセスを含む広範なアクセス許可のセットが付与されていることに注意してください。

- **Azure ロール:** [所有者](../role-based-access-control/built-in-roles.md#owner)、[共同作成者](../role-based-access-control/built-in-roles.md#contributor)、および [閲覧者](../role-based-access-control/built-in-roles.md#reader)です。 Azure ロールによって、Log Analytics ワークスペースや Azure Sentinel リソースなど、すべての Azure リソースへのアクセス権が付与されます。

- **Log Analytics のロール:** [Log Analytics 共同作成者](../role-based-access-control/built-in-roles.md#log-analytics-contributor)、[Log Analytics 閲覧者](../role-based-access-control/built-in-roles.md#log-analytics-reader)。 Log Analytics のロールでは、Log Analytics ワークスペースに対するアクセス権が付与されます。 

たとえば、**Azure Sentinel 閲覧者** ロールを割り当てられていて、**Azure Sentinel 共同作成者** ロールを割り当てられていないユーザーでも、Azure レベルで **共同作成者** ロールが割り当てられている場合は、Azure Sentinel の項目を編集できます。 そのため、Azure Sentinel 内のユーザーにのみアクセス許可を付与する場合は、このユーザーの以前のアクセス許可を慎重に削除して、別のリソースに必要なアクセス権が無効にならないようにする必要があります。

## <a name="roles-and-allowed-actions"></a>ルールと許可されているアクション

次の表は、Azure Sentinel でのロールと許可されているアクションをまとめたものです。 

| Role | プレイブックを作成して実行する| ブック、分析ルール、およびその他の Azure Sentinel リソースの作成と編集 | インシデントを管理する (無視、割り当てなど) | データ、インシデント、ブック、その他の Azure Sentinel リソースを表示する |
|---|---|---|---|---|
| Azure Sentinel 閲覧者 | -- | -- | -- | &#10003; |
| Azure Sentinel レスポンダー | -- | -- | &#10003; | &#10003; |
| Azure Sentinel 共同作成者 | -- | &#10003; | &#10003; | &#10003; |
| Azure Sentinel 共同作成者 + ロジック アプリの共同作成者 | &#10003; | &#10003; | &#10003; | &#10003; |

## <a name="custom-roles-and-advanced-azure-rbac"></a>カスタム ロールと高度な Azure RBAC

- **カスタム ロール**。 Azure 組み込みロールの使用に加え、またはその代わりに、Azure Sentinel の Azure カスタム ロールを作成することができます。 Azure Sentinel の Azure カスタム ロールは、[Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) および [Azure Log Analytics リソース](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights)に対する特定のアクセス許可に基づいて、他の[Azure カスタム ロール](../role-based-access-control/custom-roles-rest.md#create-a-custom-role)を作成する場合と同じ方法で作成されます。

- **Log Analytics RBAC**。 Azure Sentinel ワークスペースのデータに対して、Log Analytics の高度な Azure ロールベースのアクセス制御を使用できます。 これには、データ型ベースの Azure RBAC とリソースコンテキストの Azure RBAC の両方が含まれます。 詳細については、次を参照してください。

    - [Azure Monitor でログ データとワークスペースを管理する](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Azure Sentinel のリソースコンテキストの RBAC](resource-context-rbac.md)
    - [テーブルレベルの RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    リソースコンテキスト RBAC とテーブルレベル RBAC は、Azure Sentinel を丸ごと利用できる許可を与えることなく、Azure Sentinel ワークスペースに含まれる特定のデータへのアクセスを許可する 2 つの手法です。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Sentinel ユーザーのロールと、各ロールでユーザーが実行できる操作について学習しました。

Azure のセキュリティとコンプライアンスに関するブログ記事は、[Azure Sentinel ブログ](https://aka.ms/azuresentinelblog)で見つかります。
