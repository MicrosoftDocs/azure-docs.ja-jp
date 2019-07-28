---
title: クエリ言語を理解する
description: Azure Resource Graph で使用可能な Kusto の演算子と関数について説明します。
author: dcthegeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800510"
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

## <a name="escape-characters"></a>エスケープ文字

`.` や `$` を含むものなど、一部のプロパティ名はクエリ内でラップまたはエスケープする必要があります。そうしないと、プロパティ名が正しく解釈されず、期待する結果が得られません。

- `.` - プロパティ名を `['propertyname.withaperiod']` のようにラップします。
  
  プロパティ _odata.type_ をラップするクエリ例:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - プロパティ名の文字をエスケープします。 使用されるエスケープ文字は、Resource Graph が実行されるシェルによって異なります。

  - **bash** - `\`

    bash でプロパティ _\$type_ をエスケープするクエリ例:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - `$` 文字をエスケープしないでください。

  - **PowerShell** - ``` ` ```

    PowerShell でプロパティ _\$type_ をエスケープするクエリ例:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>次の手順

- [初歩的なクエリ](../samples/starter.md)で使用されている言語を参照してください
- [高度なクエリ](../samples/advanced.md)で高度な使用方法を参照してください
- [その他のリソース](explore-resources.md)