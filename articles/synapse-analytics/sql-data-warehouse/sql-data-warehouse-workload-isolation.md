---
title: ワークロードの分離
description: Azure Synapse Analytics のワークロード グループを使用してワークロードの分離を設定するためのガイダンスです。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 506aed16f1b8a6c631a759bb1367aef8242859ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98734782"
---
# <a name="azure-synapse-analytics-workload-group-isolation"></a>Azure Synapse Analytics ワークロード グループ分離

この記事では、ワークロード グループを使用してワークロードの分離を構成する方法、リソースを含める方法、およびクエリ実行のランタイム ルールを適用する方法について説明します。

## <a name="workload-groups"></a>ワークロード グループ

ワークロード グループは、一連の要求のコンテナーであり、ワークロードの分離などのワークロードの管理をシステム上で構成するための基礎となります。  ワークロード グループは、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文を使用して作成されます。  単純なワークロード管理構成では、データの読み込みとユーザークエリを管理できます。  たとえば、`wgDataLoads` という名前のワークロード グループは、システムに読み込まれるデータのワークロードの側面を定義します。 また、`wgUserQueries` という名前のワークロード グループは、システムからデータを読み取るクエリを実行するユーザーのワークロードの側面を定義します。

次のセクションでは、ワークロード グループが分離、包含、要求リソース定義を定義し、実行ルールに準拠する機能を提供する方法について説明します。

## <a name="workload-isolation"></a>ワークロードの分離

ワークロードの分離とは、リソースがワークロード グループ専用で予約されることを意味します。  ワークロードを分離するには、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文で MIN_PERCENTAGE_RESOURCE パラメーターを 0 より大きい値に設定します。  厳格な SLA に従う必要がある継続的な実行ワークロードの場合、分離することでワークロード グループで常にリソースが使用できるようになります。

ワークロードの分離を構成することで、保証されるコンカレンシーのレベルを暗黙的に定義します。 たとえば、`MIN_PERCENTAGE_RESOURCE` が 30% に設定され、`REQUEST_MIN_RESOURCE_GRANT_PERCENT` が 2% に設定されているワークロード グループは、15 個の同時実行が保証されます。  同時実行のレベルが保証されるのは、リソースの 15～2% のスロットがワークロード グループ内で常に予約されるためです (`REQUEST_*MAX*_RESOURCE_GRANT_PERCENT` がどのように構成されているかは関係ありません)。  `REQUEST_MAX_RESOURCE_GRANT_PERCENT` が `REQUEST_MIN_RESOURCE_GRANT_PERCENT` より大きく、`CAP_PERCENTAGE_RESOURCE` がより大きい場合、`MIN_PERCENTAGE_RESOURCE` 追加のリソースが要求ごとに追加されます。  `REQUEST_MAX_RESOURCE_GRANT_PERCENT` と `REQUEST_MIN_RESOURCE_GRANT_PERCENT` が等しく、`CAP_PERCENTAGE_RESOURCE` が `MIN_PERCENTAGE_RESOURCE`よりも大きい場合は、追加の同時実行が可能です。  保証されるコンカレンシーを決定するには、次の方法を検討してください。

[保証されるコンカレンシー] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> min_percentage_resource には、特定のサービス レベルの実行可能な最小値があります。  詳細については、[有効な値](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json?view=azure-sqldw-latest&preserve-view=true#effective-values)に関する記事を参照してください。

ワークロードの分離がされない場合、要求はリソースの[共有プール](#shared-pool-resources)で動作します。  共有プール内のリソースへのアクセスは保証されず、[重要度](sql-data-warehouse-workload-importance.md)基準で割り当てられます。

ワークロード グループにアクティブな要求がない場合でもワークロード グループにはリソースが割り当てられるため、ワークロードの分離の構成は慎重に行う必要があります。 必要以上に分離するよう構成すると、システム全体の使用率が低下する可能性があります。

ワークロードの分離を 100% 構成するワークロード管理ソリューションは使用しないでください。100% の分離は、すべてのワークロード グループで構成されている min_percentage_resource の合計が 100% である状態です。  この種類の構成は非常に限定的で厳格であり、誤って分類されたリソース要求を扱う余裕がほとんどなくなってしまいます。 分離用に構成されていないワークロード グループから要求を 1 つ実行することを許可するプロビジョニングがあります。 この要求に割り当てられたリソースは、システム DMV に 0として表示され、システムで予約されたリソースから smallrc レベルのリソース付与を借用します。

> [!NOTE]
> リソース使用率を最適化するには、分離を活用して SLA が満たされていることを確認し、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に基づいてアクセスされる共有リソースと混在させることを検討してください。

## <a name="workload-containment"></a>ワークロードの包含

ワークロードの包含とは、ワークロード グループが使用できるリソースの量を制限することを指します。  ワークロードを包含するには、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文で CAP_PERCENTAGE_RESOURCE パラメーターを 100 より小さい値に設定します。  アドホック クエリを使用して what-if 分析を実行できるよう、ユーザーがシステムへの読み取りアクセスを必要とするシナリオを考えてみます。  このような要求は、システムで実行されている他のワークロードに悪影響を与える可能性があります。  包含を構成すると、リソースの量が制限されます。

ワークロードの包含の構成では、コンカレンシーの最大レベルが暗黙的に定義されます。  CAP_PERCENTAGE_RESOURCE を 60% に設定し、REQUEST_MIN_RESOURCE_GRANT_PERCENT を 1% に設定した場合、ワークロード グループではレベル 60 のコンカレンシーが保証されます。  コンカレンシーの最大数を決定するには、次の方法を検討してください。

[最大コンカレンシー] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE]
> MIN_PERCENTAGE_RESOURCE のレベルが 0 より大きいワークロード グループが作成されている場合、ワークロード グループの有効な CAP_PERCENTAGE_RESOURCE は100% にはなりません。  有効なランタイム値については、「[ssys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。

## <a name="resources-per-request-definition"></a>要求の定義ごとのリソース

ワークロード グループは、REQUEST_MIN_RESOURCE_GRANT_PERCENT パラメーターと REQUEST_MAX_RESOURCE_GRANT_PERCENT パラメーターで要求ごとに割り当てられるリソースの最小容量と最大量を [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文で定義するメカニズムを提供します。  この場合のリソースは、CPU とメモリです。  これらの値を構成すると、システムで実現できるリソースの量とコンカレンシーのレベルが決まります。

> [!NOTE]
> REQUEST_MAX_RESOURCE_GRANT_PERCENT は省略可能なパラメーターで、既定値は REQUEST_MIN_RESOURCE_GRANT_PERCENT に対して指定されている値と同じです。

リソース クラスを選択する場合と同様に、REQUEST_MIN_RESOURCE_GRANT_PERCENT を構成すると要求によって使用されるリソースの値が設定されます。  設定値によって示されるリソース量は、要求が実行を開始する前に、その要求に割り当てられることが保証されます。  リソース クラスからワークロード グループに移行するお客様については、まず[ハウツー](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)に関する記事に従って、リソース クラスからワークロード グループへマッピングすることを検討してください。

REQUEST_MIN_RESOURCE_GRANT_PERCENT を超える値に REQUEST_MAX_RESOURCE_GRANT_PERCENT を構成すると、システムは要求ごとにより多くのリソースを割り当てることができます。  要求のスケジュール設定中に、システムは、共有プールのリソースの可用性とシステムの現在の負荷に基づいて、要求に対する実際のリソース割り当てを REQUEST_MIN_RESOURCE_GRANT_PERCENT と REQUEST_MAX_RESOURCE_GRANT_PERCENT の間で決定します。  クエリがスケジュールされている場合は、リソースの[共有プール](#shared-pool-resources)にリソースが存在している必要があります。  

> [!NOTE]
> REQUEST_MIN_RESOURCE_GRANT_PERCENT と REQUEST_MAX_RESOURCE_GRANT_PERCENT には、有効な MIN_PERCENTAGE_RESOURCE と CAP_PERCENTAGE_RESOURCE の値に依存する有効な値があります。  有効なランタイム値については、「[ssys.dm_workload_management_workload_groups_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)」を参照してください。

## <a name="execution-rules"></a>実行規則

アドホック レポート システムでは、他のユーザーの生産性に深刻な影響を与えるランナウェイ クエリを誤って実行する可能性があります。  システム管理者は、システム リソースを解放するために、ランナウェイ クエリの強制終了に時間を費やすことになります。  ワークロード グループには、指定された値を超えたクエリを取り消すクエリ実行タイムアウトルールを構成する機能があります。  ルールを構成するには [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 構文で `QUERY_EXECUTION_TIMEOUT_SEC` パラメーターを設定します。

## <a name="shared-pool-resources"></a>共有プールのリソース

共有プールのリソースは、分離用に構成されていないリソースです。  MIN_PERCENTAGE_RESOURCE が 0 に設定されたワークロード グループは、共有プール内のリソースを利用して要求を実行します。  CAP_PERCENTAGE_RESOURCE が MIN_PERCENTAGE_RESOURCE よりも大きいワークロード グループも、共有リソースを使用します。  共有プールで利用できるリソースの量は、次のように計算されます。

[共有プール] = 100-[すべてのワークロード グループにおける `MIN_PERCENTAGE_RESOURCE` の合計]

共有プール内のリソースへのアクセスは、[重要度](sql-data-warehouse-workload-importance.md)基準で割り当てられます。  重要度レベルが同じ要求は、先入れ先出しで共有プールリソースにアクセスします。

## <a name="next-steps"></a>次のステップ

- [クイック スタート: ワークロードの分離を構成する](quickstart-configure-workload-isolation-tsql.md)
- [ワークロード グループを作成する](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [リソース クラスからワークロード グループへの変換](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
- [ワークロード管理ポータルの監視](sql-data-warehouse-workload-management-portal-monitor.md)。  
