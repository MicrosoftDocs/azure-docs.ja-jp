---
title: 変数の代入
description: この記事では、Azure Synapse Analytics の専用 SQL プールに T-SQL 変数を割り当てる際の重要なヒントについて説明します。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 87448ea737c11af13a52632e5bf4f67dc54d9ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459232"
---
# <a name="assign-variables-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールに変数を割り当てる

この記事では、専用 SQL プールに T-SQL 変数を割り当てる際の重要なヒントについて説明します。

## <a name="set-variables-with-declare"></a>DECLARE を使用して変数を設定する

専用 SQL プールの変数は、`DECLARE` ステートメントまたは `SET` ステートメントを使用して設定します。 DECLARE を使用した変数の初期化は、SQL プールで変数値を設定する最も柔軟性の高い方法の 1 つです。

```sql
DECLARE @v  int = 0
;
```

DECLARE を使用して、一度に複数の変数を設定することもできます。 SELECT または UPDATE を使用して次を行うことはできません。

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

同じ DECLARE ステートメントで変数を初期化して使用することはできません。 要点を示すために、次の例では、同じ DECLARE ステートメントで @p1 を初期化し、使用しています。このようなステートメントは使用 **できません**。 そのため、次の例ではエラーが発生します。

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="set-values-with-set"></a>SET を使用して値を設定する

SET は、1 つの変数を設定する際によく使用されるメソッドです。

次のステートメントはすべて、SET を使用して変数を設定する有効な方法です。

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

SET を使用して設定できる変数は、一度に 1 つに限られます。 ただし、複合演算子は許容されます。

## <a name="limitations"></a>制限事項

変数代入で UPDATE は使用できません。

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
