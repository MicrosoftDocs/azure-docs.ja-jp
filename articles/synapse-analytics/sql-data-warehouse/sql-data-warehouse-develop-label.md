---
title: インスツルメント化されたクエリにラベルを使用する
description: Azure Synapse Analytics の専用 SQL プールでのラベルを使用したクエリのインストルメント化に関するヒント。
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
ms.openlocfilehash: 7958caf71658dcdcbf31bac84697931e9049452f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462749"
---
# <a name="using-labels-to-instrument-queries-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics の専用 SQL プールでラベルを使用してクエリをインストルメント化します

この記事に含まれているのは、専用 SQL プールでラベルを使用してクエリをインストルメント化するソリューションを開発するためのヒントです。

## <a name="what-are-labels"></a>ラベルとは?

専用 SQL プールでは、クエリ ラベルと呼ばれる概念がサポートされています。 難解な領域に入る前に、1 つの例を説明しましょう。

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

この最後の行は、クエリに、 'My Query Label' という文字列をタグ付けします。 このタグが便利なのは、DMV を介してラベルをクエリできるためです。

ラベルのクエリを実行できることで、問題のあるクエリを検索したり、ELT 実行の進行状況を識別したりするためのメカニズムが提供されます。

良好な命名規則はたいへん役立ちます。 たとえば、ラベルを PROJECT、PROCEDURE、STATEMENT、または COMMENT で始めると、ソース管理内のすべてのコード間でクエリを一意に識別する上で役立ちます。

次のクエリでは、動的管理ビューを使用して、ラベルごとに検索が行われます。

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> クエリをする際に、語句のラベルを角かっこまたは二重引用符で囲むことが必要です。 ラベルは予約語であるため、区切られていない場合はエラーが発生します。

## <a name="next-steps"></a>次のステップ

開発についてのその他のヒントは、[開発の概要](sql-data-warehouse-overview-develop.md)に関するページをご覧ください。
