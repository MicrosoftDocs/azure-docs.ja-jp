---
title: Azure Monitor ログ クエリの resource() 式 | Microsoft Docs
description: リソース式は、リソース中心の Azure Monitor ログ クエリで複数のリソースからデータを取得するために使用されます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2018
ms.openlocfilehash: 9a5e5c7959a243d6c9d243b706524f624ffa3cdb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047215"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure Monitor ログ クエリの resource() 式

`resource` 式は、他のリソースからデータを取得するために、[リソースをスコープとする](scope.md#query-scope) Azure Monitor クエリで使用されます。 


## <a name="syntax"></a>構文

`resource(`*識別子*`)`

## <a name="arguments"></a>引数

- *識別子*:リソースのリソース ID。

| 識別子 | 説明 | 例
|:---|:---|:---|
| リソース | リソースのデータが含まれます。 | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| リソースグループまたはサブスクリプション | リソースとそれに含まれるすべてのリソースのデータが含まれます。  | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>Notes

* リソースへの読み取りアクセスが必要です。


## <a name="examples"></a>例

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>次のステップ

- クエリのスコープの詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](scope.md)」を参照してください。
- [Kusto クエリ言語](/azure/kusto/query/)の完全なドキュメントにアクセスします。
