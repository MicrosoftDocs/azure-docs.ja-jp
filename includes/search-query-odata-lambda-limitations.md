---
author: brjohnstmsft
ms.service: search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: b4d0bc73e652a1cd6ef59589318b92f63727c7f5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081832"
---
| データ型 | `any` を含むラムダ式で許可されている機能 | `all` を含むラムダ式で許可されている機能 |
|---|---|---|
| `Collection(Edm.ComplexType)` | `search.ismatch` と `search.ismatchscoring` を除くすべて | 同じ |
| `Collection(Edm.String)` | `eq` または `search.in` での比較 <br/><br/> `or` でのサブ式の組み合わせ | `ne` または `not search.in()` での比較 <br/><br/> `and` でのサブ式の組み合わせ |
| `Collection(Edm.Boolean)` | `eq` または `ne` での比較 | 同じ |
| `Collection(Edm.GeographyPoint)` | `lt` または `le` での `geo.distance` の使用 <br/><br/> `geo.intersects` <br/><br/> `or` でのサブ式の組み合わせ | `gt` または `ge` での `geo.distance` の使用 <br/><br/> `not geo.intersects(...)` <br/><br/> `and` でのサブ式の組み合わせ |
| `Collection(Edm.DateTimeOffset)`、`Collection(Edm.Double)`、`Collection(Edm.Int32)`, `Collection(Edm.Int64)` | `eq`、`ne`、`lt`、`gt`、`le`、または `ge` を使用した比較 <br/><br/> `or` を使用した、比較と他のサブ式の組み合わせ <br/><br/> `and` を使用した、`ne` を除く比較と他のサブ式の組み合わせ <br/><br/> [選言標準形 (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) での `and` と `or` の組み合わせを使用した式 | `eq`、`ne`、`lt`、`gt`、`le`、または `ge` を使用した比較 <br/><br/> `and` を使用した、比較と他のサブ式の組み合わせ <br/><br/> `or` を使用した、`eq` を除く比較と他のサブ式の組み合わせ <br/><br/> [連言標準形 (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) での `and` と `or` の組み合わせを使用した式 |
