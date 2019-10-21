---
title: Azure Monitor ログ クエリの resource() 式 | Microsoft Docs
description: リソース式は、リソース中心の Azure Monitor ログ クエリで複数のリソースからデータを取得するために使用されます。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/10/2018
ms.author: bwren
ms.openlocfilehash: deca6e7ef1c231a82a73067971d86a6e9cdd0599
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817374"
---
# <a name="resource-expression-in-azure-monitor-log-query"></a>Azure Monitor ログ クエリの resource() 式

`resource` 式は、他のリソースからデータを取得するために、[リソースをスコープとする](scope.md#query-scope) Azure Monitor クエリで使用されます。 


## <a name="syntax"></a>構文

`resource(`*識別子*`)`

## <a name="arguments"></a>引数

- *識別子*:リソースのリソース ID。

| ID | 説明 | 例
|:---|:---|:---|
| リソース | リソースのデータが含まれます。 | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm") |
| リソースグループまたはサブスクリプション | リソースとそれに含まれるすべてのリソースのデータが含まれます。  | resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup) |


## <a name="notes"></a>メモ

* リソースへの読み取りアクセスが必要です。


## <a name="examples"></a>例

```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup/providers/microsoft.compute/virtualmachines/myvm").Heartbeat) | summarize count() by _ResourceId, TenantId
```
```Kusto
union (Heartbeat),(resource("/subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcesgroups/myresourcegroup).Heartbeat) | summarize count() by _ResourceId, TenantId
```


## <a name="next-steps"></a>次の手順

- クエリのスコープの詳細については、「[Azure Monitor Log Analytics のログ クエリのスコープと時間範囲](scope.md)」を参照してください。
- [Kusto クエリ言語](/azure/kusto/query/)の完全なドキュメントにアクセスします。
