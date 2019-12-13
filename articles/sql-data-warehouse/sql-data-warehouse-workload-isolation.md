---
title: ワークロードの分離
description: Azure SQL Data Warehouse のワークロード グループを使用してワークロードの分離を設定するためのガイダンスです。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 11/27/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 82270c126d8a0894cd3a388dcab62017ed63c2cd
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974650"
---
# <a name="sql-data-warehouse-workload-group-isolation-preview"></a>SQL Data Warehouse のワークロード グループの分離 (プレビュー)

この記事では、ワークロード グループを使用してワークロードの分離を構成する方法、リソースを含める方法、およびクエリ実行のランタイム ルールを適用する方法について説明します。

## <a name="workload-groups"></a>ワークロード グループ

ワークロード グループは、一連の要求のコンテナーであり、ワークロードの分離などのワークロードの管理をシステム上で構成するための基礎となります。  ワークロード グループは、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 構文を使用して作成されます。  単純なワークロード管理構成では、データの読み込みとユーザークエリを管理できます。  たとえば、`wgDataLoads` という名前のワークロード グループは、システムに読み込まれるデータのワークロードの側面を定義します。 また、`wgUserQueries` という名前のワークロード グループは、システムからデータを読み取るクエリを実行するユーザーのワークロードの側面を定義します。

次のセクションでは、ワークロード グループが分離、包含、要求リソース定義を定義し、実行ルールに準拠する機能を提供する方法について説明します。

## <a name="workload-isolation"></a>ワークロードの分離

ワークロードの分離とは、リソースがワークロード グループ専用で予約されることを意味します。  ワークロードを分離するには、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 構文で MIN_PERCENTAGE_RESOURCE パラメーターを 0 より大きい値に設定します。  厳格な SLA に従う必要がある継続的な実行ワークロードの場合、分離することでワークロード グループで常にリソースが使用できるようになります。 

ワークロードの分離を構成することで、保証されるコンカレンシーのレベルを暗黙的に定義します。  MIN_PERCENTAGE_RESOURCE を 30% に設定し、REQUEST_MIN_RESOURCE_GRANT_PERCENT を 2% に設定した場合、ワークロード グループではレベル 15 のコンカレンシーが保証されます。  保証されるコンカレンシーを決定するには、次の方法を検討してください。

[保証されるコンカレンシー] = [`MIN_PERCENTAGE_RESOURCE`]/[`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> min_percentage_resource には、特定のサービス レベルの実行可能な最小値があります。  詳細については、[有効な値](https://review.docs.microsoft.com/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest#effective-values)に関する記事を参照してください。

ワークロードの分離がされない場合、要求はリソースの[共有プール](#shared-pool-resources)で動作します。  共有プール内のリソースへのアクセスは保証されず、[重要度](sql-data-warehouse-workload-importance.md)基準で割り当てられます。

ワークロード グループにアクティブな要求がない場合でもワークロード グループにはリソースが割り当てられるため、ワークロードの分離の構成は慎重に行う必要があります。  必要以上に分離するよう構成すると、システム全体の使用率が低下する可能性があります。

ワークロードの分離を 100% 構成するワークロード管理ソリューションは使用しないでください。100% の分離は、すべてのワークロード グループで構成されている min_percentage_resource の合計が 100% である状態です。  この種類の構成は非常に限定的で厳格であり、誤って分類されたリソース要求を扱う余裕がほとんどなくなってしまいます。  分離用に構成されていないワークロード グループから要求を 1 つ実行することを許可するプロビジョニングがあります。  この要求に割り当てられたリソースは、システム DMV に 0として表示され、システムで予約されたリソースから smallrc レベルのリソース付与を借用します。

> [!NOTE] 
> リソース使用率を最適化するには、分離を活用して SLA が満たされていることを確認し、[ワークロードの重要度](sql-data-warehouse-workload-importance.md)に基づいてアクセスされる共有リソースと混在させることを検討してください。

## <a name="workload-containment"></a>ワークロードの包含

ワークロードの包含とは、ワークロード グループが使用できるリソースの量を制限することを指します。  ワークロードを包含するには、[CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 構文で CAP_PERCENTAGE_RESOURCE パラメーターを 100 より小さい値に設定します。  アドホック クエリを使用して what-if 分析を実行できるよう、ユーザーがシステムへの読み取りアクセスを必要とするシナリオを考えてみます。  このような要求は、システムで実行されている他のワークロードに悪影響を与える可能性があります。  包含を構成すると、リソースの量が制限されます。

ワークロードの包含の構成では、コンカレンシーの最大レベルが暗黙的に定義されます。  CAP_PERCENTAGE_RESOURCE を 60% に設定し、REQUEST_MIN_RESOURCE_GRANT_PERCENT を 1% に設定した場合、ワークロード グループではレベル 60 のコンカレンシーが保証されます。  コンカレンシーの最大数を決定するには、次の方法を検討してください。

[最大コンカレンシー] = [`CAP_PERCENTAGE_RESOURCE`] / [`REQUEST_MIN_RESOURCE_GRANT_PERCENT`]

> [!NOTE] 
> MIN_PERCENTAGE_RESOURCE のレベルが 0 より大きいワークロード グループが作成されている場合、ワークロード グループの有効な CAP_PERCENTAGE_RESOURCE は100% にはなりません。  有効なランタイム値については、「[ssys.dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest)」を参照してください。

## <a name="resources-per-request-definition"></a>要求の定義ごとのリソース

ワークロード グループは、REQUEST_MIN_RESOURCE_GRANT_PERCENT パラメーターと REQUEST_MAX_RESOURCE_GRANT_PERCENT パラメーターで要求ごとに割り当てられるリソースの最小容量と最大量を [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 構文で定義するメカニズムを提供します。  この場合のリソースは、CPU とメモリです。  これらの値を構成すると、システムで実現できるリソースの量とコンカレンシーのレベルが決まります。

> [!NOTE] 
> REQUEST_MAX_RESOURCE_GRANT_PERCENT は省略可能なパラメーターで、既定値は REQUEST_MIN_RESOURCE_GRANT_PERCENT に対して指定されている値と同じです。

リソース クラスを選択する場合と同様に、REQUEST_MIN_RESOURCE_GRANT_PERCENT を構成すると要求によって使用されるリソースの値が設定されます。  設定値によって示されるリソース量は、要求が実行を開始する前に、その要求に割り当てられることが保証されます。  リソース クラスからワークロード グループに移行するお客様については、まず[ハウツー](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)に関する記事に従って、リソース クラスからワークロード グループへマッピングすることを検討してください。

REQUEST_MIN_RESOURCE_GRANT_PERCENT を超える値に REQUEST_MAX_RESOURCE_GRANT_PERCENT を構成すると、システムは要求ごとにより多くのリソースを割り当てることができます。  要求のスケジュール設定中に、システムは、共有プールのリソースの可用性とシステムの現在の負荷に基づいて、要求に対する実際のリソース割り当てを REQUEST_MIN_RESOURCE_GRANT_PERCENT と REQUEST_MAX_RESOURCE_GRANT_PERCENT の間で決定します。  クエリがスケジュールされている場合は、リソースの[共有プール](#shared-pool-resources)にリソースが存在している必要があります。  

> [!NOTE] 
> REQUEST_MIN_RESOURCE_GRANT_PERCENT と REQUEST_MAX_RESOURCE_GRANT_PERCENT には、有効な MIN_PERCENTAGE_RESOURCE と CAP_PERCENTAGE_RESOURCE の値に依存する有効な値があります。  有効なランタイム値については、「[ssys.dm_workload_management_workload_groups_stats](https://review.docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql?view=azure-sqldw-latest)」を参照してください。

## <a name="execution-rules"></a>実行規則

アドホック レポート システムでは、他のユーザーの生産性に深刻な影響を与えるランナウェイ クエリを誤って実行する可能性があります。  システム管理者は、システム リソースを解放するために、ランナウェイ クエリの強制終了に時間を費やすことになります。  ワークロード グループには、指定された値を超えたクエリを取り消すクエリ実行タイムアウトルールを構成する機能があります。  ルールを構成するには [CREATE WORKLOAD GROUP](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) 構文で `QUERY_EXECUTION_TIMEOUT_SEC` パラメーターを設定します。

## <a name="shared-pool-resources"></a>共有プールのリソース

共有プールのリソースは、分離用に構成されていないリソースです。  MIN_PERCENTAGE_RESOURCE が 0 に設定されたワークロード グループは、共有プール内のリソースを利用して要求を実行します。  CAP_PERCENTAGE_RESOURCE が MIN_PERCENTAGE_RESOURCE よりも大きいワークロード グループも、共有リソースを使用します。  共有プールで利用できるリソースの量は、次のように計算されます。

[共有プール] = 100-[すべてのワークロード グループにおける `MIN_PERCENTAGE_RESOURCE` の合計]

共有プール内のリソースへのアクセスは、[重要度](sql-data-warehouse-workload-importance.md)基準で割り当てられます。  重要度レベルが同じ要求は、先入れ先出しで共有プールリソースにアクセスします。

## <a name="next-steps"></a>次の手順

- [クイック スタート: ワークロードの分離を構成する](quickstart-configure-workload-isolation-tsql.md)
- [ワークロード グループを作成する](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
- [リソース クラスからワークロード グループへの変換](sql-data-warehouse-how-to-convert-resource-classes-workload-groups.md)
