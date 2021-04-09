---
title: Synapse SQL でクエリ ラベルを使用する
description: この記事には、Synapse SQL でクエリ ラベルを使用する際の重要なヒントが含まれています。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 59fa68d12f1d8be598810399fc5623c2af983979
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462252"
---
# <a name="use-query-labels-in-synapse-sql"></a>Synapse SQL でクエリ ラベルを使用する

この記事には、Synapse SQL でクエリ ラベルを使用する際の重要なヒントが含まれています。

> [!NOTE]
> サーバーレス SQL プールでは、ラベル付けクエリはサポートされていません。

## <a name="what-are-query-labels"></a>クエリ ラベルとは

専用 SQL プールでは、クエリ ラベルと呼ばれる概念がサポートされています。 難解な領域に入る前に、1 つの例を説明しましょう。

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

この最後の行は、クエリに、 'My Query Label' という文字列をタグ付けします。 このタグが便利なのは、DMV を介してラベルをクエリできるためです。 ラベルのクエリの実行では、問題のあるクエリを検索するためのメカニズムが提供され、ELT 実行の進行状況を識別するのに役立ちます。

適切な名前付け規則が最も役立ちます。 たとえば、ラベルを PROJECT、PROCEDURE、STATEMENT、または COMMENT で始めると、ソース管理内のすべてのコード間でクエリを一意に識別する上で役立ちます。

次のクエリでは、動的管理ビューを使用して、ラベルごとに検索が行われます。

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> クエリをする際に、語句のラベルを角かっこまたは二重引用符で囲むことが必要です。 ラベルは予約語であるため、区切られていない場合はエラーが発生します。 
> 
> 

## <a name="next-steps"></a>次のステップ
開発についてのその他のヒントは、[開発の概要](develop-overview.md)に関するページをご覧ください。


