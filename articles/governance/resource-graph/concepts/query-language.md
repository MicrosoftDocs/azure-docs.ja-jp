---
title: クエリ言語を理解する
description: Azure Resource Graph で使用可能な Kusto の演算子と関数について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 08e4f09665a3501073f55b7f5b82bf51cf508ea9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59276679"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph クエリ言語の概要

Azure Resource Graph のクエリ言語では、さまざまな演算子と関数がサポートされています。 それぞれ [Azure Data Explorer ](../../../data-explorer/data-explorer-overview.md)をベースにして機能し､動作します。

Resoruce Graph が使用するクエリ言語について学習する最善の方法は、Azure Data Explorer の [Query Language](/azure/kusto/query/index)用のドキュメントから始めることです｡ 言語の構造やサポートされている各種演算子および関数の連携について学ぶことができます｡

## <a name="supported-tabular-operators"></a>サポートされているテーブル演算子

Resource Graph でサポートされているテーブル演算子の一覧を次に示します。

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>サポートされている関数

Resource Graph でサポートされている関数の一覧を次に示します。

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>次の手順

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を参照してください
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を参照してください
- [その他のリソース](explore-resources.md)